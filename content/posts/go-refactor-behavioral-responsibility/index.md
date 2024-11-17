---
title: "Go Refactor Behavioral Responsibility"
date: 2024-11-16T14:56:56-05:00
draft: false
---

Go promotes simplicity, it makes Go more attractive to the community.
A recent app I released only consume around 12MB Memory and 0.02m CPU to serve 90k emails per day, the app includes most of features of a MVC web app.
It is really impressive how lightweight a Golang application can be.

Simplicity could be an advantage, but also a disadvantage. As developer has to write everything from scratch (or most of), it is easy to create technical debt
or introduce bug when dealing with low level configuration, such as serializing and deserializing, io related, handling error, handling shutting down, 
generating metrics and so on.

In this post, I'd like to share some tips to create less future problematic code by using some common pattern to hide or reuse these responsibilities from the application logic.


<!--more-->

## Serialize and Deserialize JSON object

The most common deserialization is `json.Unmarshall`. I am sure that this piece of code are very familiar

```go
func CreateJob(w http.ResponseWriter, r *http.Request) {
    var job Job

    err := json.NewDecoder(r.Body).Decode(&job)
    if err != nil {
        http.Error(w, err.Error(), http.StatusBadRequest)
        return
    }

    // Store job to the Database

    err = json.NewEncoder(w).Encode(job)
    if err != nil {
        http.Error(w, "Internal error", http.StatusInternalServerError)

        return
    }
}
```

The actual code that generates value is in the comment "Store job to the Database".
The rest is just low level setup to retrieve and response to the user.

Unless there are complicated logic to handle exeception, these lines of code are going to be the same in every request handler across the project.

To reduce the amount of duplication, a http wrapper function could be added. This function extract the decode/encode part away, and replace the signature of the
original `CreateJob` function to receive the `Job` and return an `CreateJobResponse` (if needed).

The wrapper function may look like 

```go
type CreateJobRequest struct {
	Body *CreateJobJSONBody
}

func WrapCreateJob(w http.ResponseWriter, r *http.Request) {
	var request CreateJobRequest

	var body CreateJobJSONBody
	if err := json.NewDecoder(r.Body).Decode(&body); err != nil {
		requestErrorHandle(w, r, fmt.Errorf("can't decode JSON body: %w", err))
		return
	}

    request.Body = &body

	response, err := CreateJobHandle(r.Context(), request)

	if err != nil {
		responseErrorHandle(w, r, err)
	} else if validResponse, ok := response.(CreateJobResponse); ok {
		if err := validResponse.Write(w); err != nil {
			responseErrorHandle(w, r, err)
		}
	} else if response != nil {
	    responseErrorHandle(w, r, fmt.Errorf("unexpected response type: %T", response))
	}
}
```

And the `CreateJobHanle` is as simple as

```go

func CreateJobHandle(ctx context.Context, request CreateJobRequest) (CreateJobResponse, error) {
    err := StoreJobIntoDB(ctx, request.Body)
    if err != nil {
        return nil, err
    }

    response = CreateJobResponse{}// fill up the data

    return response, nil
}

```

With this improvement, we can move the decoding/encoding logic outside of the action handler, leave the business logic in the handle function. This approach
enhances readability, makes the testing more straightforward, and allow further reuseability.


## Produce logs before and after executing a command/query

When using [Command and Query Object](/posts/command-query-in-go-project/), several logs can be structurize consistently amongs all query and command handlers.
Certain logs include a log before the command runs and a log after command completes, with any errors for the developer to investigate further.

It is easy to forget adding a log when writing a new command because it doesn't fail any testcase, and it isn't part of the business logic.
Unlike the wrapper function described above, a command and query should follow a predefined interface

```go
type QueryHandler[Q any, R any] interface {
	Handle(ctx context.Context, q Q) (R, error)
}

type CommandHandler[C any] interface {
	Handle(ctx context.Context, cmd C) error
}
```

By following these interfaces, the command and query are easy to implemented and tested.
In this setup, it is possible to add the decorator that extend the functionality of the command.

At brief, decorator object has exact same interface with the target object, for instance, the log decorator for
the command may look like below

```go
type queryLoggingDecorator[Q any, R any] struct { // implements QueryHandler interface
	query QueryHandler[Q, R]
	logger Logger
}

func (d queryLoggingDecorator[Q, R]) Handle(ctx context.Context, query Q) (result R, err error) {
	logger := d.logger.
		With().
		Str("query", generateActionName(query)).
		Str("query_body", fmt.Sprintf("%#v", query)).
		Logger()

	logger.Debug().Msg("Executing Query")

	defer func() {
		if err == nil {
			logger.Info().Msg("Query executed successfully")
		} else {
			logger.Error().Err(err).Msg("Query executed failed")
		}
	}()

	return d.base.Handle(ctx, query)
}

```

There could be more decorators as needed by the project.
We need to replace the query object with the decorator, and decorator can stack up
A static factory function could simplify the query constructor step

```go
func DecorateQuery[H any, R any](
	handler QueryHandler[H, R],
	logger zerolog.Logger
) QueryHandler[H, R] {
	return queryLoggingDecorator[H, R]{
		base: handler,
		logger: logger,
	}
}
```

Example for using this static factory function

```go

func NewGetJobHandler(jobRepository Repository, logger Logger) {}
    return DecorateQuery[GetJobQuery, Job] {
        GetJobHandler{ repo: jobRepository },
        logger
    }
}

getJobCmd := NewGetJobHandler(jobRepo, logger)
getJobCmd.Handle(...)

```

This setup allows hiding the logging responsibility away from the actual query by using decorator pattern.
By calling the `getJobCmd.Handle(ctx,...)`, the consumer expects the same output without knowing the decorators are added by another developer.

In real life scenario, the `DecorateQuery` could get more complicated with multiple decorators stacking. This example below may not be the most complicated
setup, hopefully it still gives you an idea how much extra functionalities can be added into the project

```go
func DecorateQuery[H any, R any](
	handler QueryHandler[H, R],
    // more parameters
    // we can consider to have several decorate query helper functions
) QueryHandler[H, R] {
	return queryLoggingDecorator[H, R]{
		base: queryMetricsDecorator[H, R]{
			base:         queryAnalyticDecorator[H, R] {
                base:     queryAsyncHistoryPublishDecorator[H, R] {
                    base: handler,
                }
            },
			metricClient: metricClient,
		},
		logger: logger,
	}
}
```

## Conclusion

Decorator and function wrapper are very useful to extend the service functionalities. It hides the extra logic that doesn't contribute directly to the business domain
away for the core functionalities, keeps the project easy to maintain. I highly recommend applying these pattern to your project if possible. Please let me know if you find
them useful. See you all in the next post.




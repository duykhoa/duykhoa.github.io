---
title: "Test Command Query With Mock"
date: 2024-10-23T21:26:22-04:00
tags:
  - architecture
  - golang
  - domain driven development
---

This post explains how to test the command and query handler, given the project applies [Command Query Separation](/content/posts/command-query-in-go-project/index.md) pattern.

<!--more-->

## CQS Structure

Since both Query and Command handlers follow the same approach, the blog post only mentions how to build the test for the command handler.

The CQS structure defines a command handler interface, which receives a `C` generic command object and return nothing but an error in non-happy path. 


The `CreateArticleHandler` is a specific interface which handle the `CreateArticle` command.

```golang
type CommandHandler[C any] interface {
	Handle(ctx context.Context, cmd C) error
}

type CreateArticleHandler = common.CommandHandler[CreateArticle]

type createArticleHandler struct {
    repo ArticleRepository
}

func (c createArticleHandler) Handle(ctx context.Context, cmd CreateArticle) error {
    // TODO
}
```

Given the command handler is following this structure, we need to test the command handler and the higher component, e.g the controller.

We need to deal with the command handler's dependency first, which is the `ArticleRepository`. It would be easier if the `ArticleRepository` is an interface. If there is no interface for the repository, please consider to have define the interface and use the interface for the repo dependency.

With the interface setup, we can create a dummy implementation to test the `ArticleRepository`. [mockery`](https://github.com/vektra/mockery) or [gomock](https://github.com/uber-go/mock) could do the boring works by creating a mock implementation for the interface. By specify the `go:generate` comment, `go generate` will generate the mock for us.

```golang
// file location: internal/domain/article/repository.go

//go:generate mockgen -destination mocks/mock_article_repository.go -package mocks . ArticleRepository
type ArticleRepository interface {
	CreateArticle(
		ctx context.Context,
		article *Article,
	) error
}
```

The mocked article repository is extremely useful in simulating different scenarios, here is an example where the repository returning an error when creating new article

```golang
ctrl := gomock.NewController(t)

repo.EXPECT().CreateArticle(
    gomock.Any(),
    gomock.AssignableToTypeOf(&domain.Article{}),
).Return(errors.New("invalid article category"))
```

The full testcase written with [Ginkgo](https://github.com/onsi/ginkgo) (this is optional). There is no expensive setup with actual DB connection, the syntax is very descriptive.

```golang
It("failed to create article", func() {
    id := uuid.New()

    repo.EXPECT().CreateArticle(
        gomock.Any(),
        gomock.AssignableToTypeOf(&domain.Article{}),
    ).Return(errors.New("invalid article category"))

    cmd := command.NewCreateArticleHandler(
        repo,
        logger,
    )

    err := cmd.Handle(ctx, command.CreateArticle{
        Article: &domain.Article{
            ArticleID: id,
        },
    })

    Expect(err).To(HaveOccurred())
})
```

After complete both successful and failed scenarios for the command handlers, we can move on to test the higher component (e.g the `ArticlesController`), which uses the command handler as a dependency.

The same mocking strategy can be used, a single line of `go:generate` command can be added into the command handler interface:

```golang

//go:generate mockgen -destination mocks/mock_create_article.go -package mocks . CreateArticleHandler
type CreateArticleHandler = common.CommandHandler[CreateArticle]
```

By adding this comment, the `go generate` command will generates the mock implementation for the command handler, which allows to setup different scenarios to test the higher component.

```golang
It("returns 500 response when creating article command returning an error", func() {
    cmdError := errors.New("create article error")
    cmd := mocks.NewMockCreateArticleHandler(mockCtrl)

    controller := ports.NewArticlesController(cmd)

    cmd.
        EXPECT().
        Handle(
            gomock.Any(),
            gomock.AssignableToTypeOf(command.CreateArticle{}),
        ).
        Return(cmdError)

    resp, err := controller.CreateArticle(ctx, generateRequest())

    Expect(err).To(Equal(cmdError))
    Expect(resp).To(BeAssignableToTypeOf(ports.CreateArticle500Response{}))
})
```

The happy scenario is similar, by altering the `Return` parameter to nil in the mock expection setup.

# Conclusion

CQS can help streamline testing efforts by promoting a clear separation of concerns, making it easier to write comprehensive and maintainable tests.
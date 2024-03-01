---
title: "A project specific command-line"
date: 2023-02-28T22:59:40-05:00
tags:
  - developer-experience
  - devops
---
Build a commandline tool that helps bootstrap the project

<!--more-->

We have a Nodejs monorepo project that is the place where we develop the code of all applications.
The inspiration behind using monorepo is focusing on improving developer productivity.
Having our own commandline is an idea to further enhance the productivity by providing a simpler way to develop the code.

In this post, we will walk through the use cases of the tool and how does it help the team.

# Usecases

# Running the app

As a typical NodeJS project, the common scripts is defined in the `package.json` file. By typing `npm start -- <app_name>`, the actual command to run the app web server is executed, and the app is up.
A web application usually requires some components like database, cache service. By using the npm script alone, the developer has to ensure the dependent services are available for the app to run.
This isn't always straightforward task depends on the software's installation and configuration complexity.
Once in a while, developer may face the issue where the [database software couldn't start](https://stackoverflow.com/questions/36436120/fatal-error-lock-file-postmaster-pid-already-exists).
Spending time on configuring and troubleshooting the software issue significantly affects to the productivity, and the effects could multiply if more than one developers having the issue.

That's why Docker has been used in our setup to minimize the cost of setting up these dependency softwares. A docker-compose configuration file is added under each application sourcecode in the monorepo,
which defines the software services required and how they are interacted.

By calling the same docker-compose command across each application, the developer is able to skip completely the dependencies setup effort, instead jumping into exploring the project and implement the requirement. Of course, as you may think, the command to run the app in docker could be a bit complicated like below

```
  docker-compose up -d --project-directory ./ --project-name <project_name> --file <path-to-project-docker-compose-file>
```

Similar to that, the log command is

```
  docker-compose logs <service_name> -d --project-directory ./ --project-name <project_name> --file <path-to-project-docker-compose-file>
```

In practice, before the app could be run, developers may need to perform some installation tasks. For example, most of the NodeJS applications are built from open source dependencies, hence developers have to run the node package manager to install these dependencies. Other than that, the dependencies' services must be ready to proceed with the incoming requests. Without some utility script to check if the services' ports are open, developer have to check the services' statuses or retry after a couple of seconds.

With a custom command line solution, these tasks can be handled by the command line. In our project, the only command developer have to type in the terminal is:

```
  iae serve -a <app_name>
```

*iae*: it is my company abbreviation name

This command takes care of how to run the Docker script, check the dependencies service, install the dependencies and run the app server.
The restart, stop commands are similar: `iae restart -a <app_name>`. For logging, `iae log -a <app_name>` is the log command.

## Running the test suite

Typically, there are several kinds of tests in a software project: unit test, integration test, end to end (E2E) test.
The unit tests aren't required any dependencies' services to executed (this statement is based on the best practice of writing test), other types may require some service to run like the database. To support running different kind of tests, the command line tool makes use of Docker and the corresponding test configuration.

```
  iae test unit|integration|e2e -a <app_name>
```

Similar to the command to run the app, there is a utility script to run the test suites after the services' ports are open. Each kind of test uses a different configuration file. Our project uses jest, so it has a separate jest configuration for unit test, integration test, and e2e test. To boost the test performance, we use [swc](https://github.com/swc-project/swc) instead of **ts-jest** for compiling typescript when running the unit test.

Once in a while, developer may want to perform all kinds of test in the development machine, instead of typing each test command, the command line has a single command.

```
  iae ci -a <app_name>
```

## Code generator

Our project uses multiple tools that require code generator such as migration files, GRPC proto file, GraphQL Schema file, etc.
To minimize the learning curve, the command line tools wrap all of these commands from different tools into a simple interface.

```
  iae protoc gen -a <app_name> -m <module_name> -f <file_path>

```

```
  iae graphql gen -a <app_name> -m <module_name>
```

```
  iae migration gen -a <app_name> -n <migration_name>

  iae migration run -a <app_name>
  iae migration revert -a <app_name>
```

# Conclusion

This post describes some usages of a project specific in supporting development flow. It helps to remove the needs of learning and remembering each commands, as well as knowing what functionalities the project requires during the development stage. I hope this could inspire you to build own tool, please let me know if this post helps.

---
title: "Building a project layout in Golang"
date: 2024-08-16T18:13:56-04:00
draft: false
tags:
  - architecture
  - web development
---

My experience when setting up a new Golang project

In a recent project, I am asked to create a new boilerplate project in Golang follows Domain Driven Development architecture.
The project becomes a standard project for all new services. It is a great opportunity for me to deep dive into the Golang ecosystem and DDD concepts.
I have learnt many things when working on this project and I will share them with you in a series of Golang blog post.

<!--more-->

Moving from a "Framework" driven project layout to a no-framework world is like taking an adventure.

There are web frameworks in Golang but none of them attempt to create a standard for project layout.

After checking a few Golang projects, I found some common patterns in the project layout.

1. There is no `src` and `test` folders in the project. 
    It is different with a SpringBoot project, where the source code is located under `src/java/...`. As the Go "package" can be imported by another package,
2. The test file can stay in the same folder, and the package name is *_test
3. `internal` is private application related code. And the project could have multile internal directories.

[Golang standards][1] is an excellent guide for the project layout. It suggests where to put the application logic as well as where the deployment script should be in.
It also mentions about the difference of `pkg` and `internal` directory. While it is a good guidance and reference to setup the project, it isn't a rule.
The overview section of this document highlighted that it isn't an official standard defined by the Go language, and for those who are building a POC or sideproject, this setup could be an overkill.

Since I'm building an enterprise project, this is a good starting point.
The project layout I come up looks similar to the guide:

```
  ├── Dockerfile
  ├── Makefile
  ├── README.md
  ├── apis
  │   ├── gen
  │   ├── openapi
  │   └── proto
  ├── bin
  ├── buf.gen.yaml
  ├── buf.yaml
  ├── cmd
  │   └── server
  │       └── server.go
  ├── deployment
  │   └── k8s
  ├── docker-compose.yml
  ├── docs
  ├── go.mod
  ├── go.sum
  ├── internal
  │   ├── common
  │   └── <appname>
  │       ├── app
  │       │   ├── app.go
  │       │   ├── command
  │       │   └── query
  │       ├── domain
  │       ├── ports
  │       │   ├── http.go
  │       │   └── port_suite_test.go
  │       └── service
  │           └── application.go
  ├── main.go
  ├── middleware
  └── scripts
```

In the next post, I will share about the domain driven architecture approach, which reflects in the app, domain, ports, etc. directories.

[1]: https://github.com/golang-standards/project-layout

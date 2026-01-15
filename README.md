# Golang best practices

## About
here is my opinion about golang sofware development best practices

## Sources
* [Effective Go](https://golang.org/doc/effective_go.html)
* [Code Review](https://go.dev/wiki/CodeReviewComments)
* [Practical Go](https://dave.cheney.net/practical-go/presentations/gophercon-singapore-2019.html)

## Application's filesystem
```
.
├── README.md
├── cmd
│   └── binA
│       ├── binA.go
├── configs
├── internal
│   └── packageA
│       ├── packageA.go
│       ├── packageA_test.go
│       └── fixtures
│           └── fixture.yaml
├── pkg
│   └── packageA
│       ├── packageA.go
│       ├── packageA_test.go
│       └── fixtures
│           └── fixture.yaml
├── migrations
│   └── 000000_init.sql
├── .golangci.yaml
├── go.mod
└── go.sum
```
The main part of the application's filesystem is based according to the [standart](https://github.com/golang-standards/project-layout). Explanations are described [here](https://ieftimov.com/golang-package-multiple-binaries).

## Description
* **cmd** - put files which will become **executable** after build.  All files are included into the **package main**.
* **configs** - put configuration failed, probably, with default values.
* **internal** - put packages only for **internal** usage (import), but not as **external dependencies**. Package can be splitted into several files.
* **pkg** - put packages for internal and **external** usage. Package can be splitted into several files. Can be omitted.
* **migrations** - migration files for any migration tool, for example [sql-migrate](https://github.com/rubenv/sql-migrate) or [goose](https://github.com/pressly/goose). [Here](https://www.youtube.com/watch?v=hKnWq4RmNKE) (in russian lang) is good overview of migration tools.
* **.golangci.yaml** - contains settings for golang [linter](https://github.com/golangci/golangci-lint)
* **go.mod** - contains dependecies list for [modules](https://blog.golang.org/using-go-modules)
* **go.sum** - contains expected cryptographic hashes of the specific module version

## Naming
* Variables, structures, interfaces and packges naming convensions are described in [official documention](https://golang.org/doc/effective_go.html). This rules are accepted **as is** if there will not be contra-arguments.

## Formatting
* Source code should be formatted before commit (push). Use [gofmt](https://golang.org/pkg/fmt/) tool for formatting. Usage example:
```
go fmt ./...
```
* Sources should be checked with [linter](https://github.com/golangci/golangci-lint) tool. Usage example:
```
golangci-lint run
```

## Dependencies
* Use [athens](https://github.com/gomods/athens) as a proxy.

## Error handling
* **Never panic**
* Application should use it's **own** errors
* You should use dependencies' errors **only** in place the dependency is called
* Use `fmt.Errorf()` to create errors.
* Create custom errors. Create struct and implement **Error** interface for it. It will make errors more informative.

## Structures
* Avoid initialize structure directly. You should create an **NewStructName** constructor to create new variable for **StructName**.
* Apply [Options pattern](https://golang.cafe/blog/golang-functional-options-pattern) if possible.

## Interfaces instead of structures
* Interfaces can be used to hide the implementation behind an interface, advantages are:
  1. You may **change** the certain implementation without changing client's sources.
  2. You may write mock-based **unittests**, for example using [mockery](https://github.com/vektra/mockery)
* Follow [code review](https://go.dev/wiki/CodeReviewComments#interfaces) recommendations.

## Logging
* Use structured logging.
* Logger should implement following log levels:
  * Debug
  * Info
  * Warn
  * Error
  * Crit/Fatal
* Additional requirements:
  * WithField/s - to log context data in separate fields, outside of log message
  * WithError - is a shortcut for `WithField("error", err)`

## Tracing
* Use [jaeger](https://www.jaegertracing.io/) for distributed tracing between microservices

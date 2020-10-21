# Golang best practices

## About
here is my opinion about golang sofware development best practices

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
* Use [modules](https://blog.golang.org/using-go-modules) as dependency system.
* Use [athens](https://github.com/gomods/athens) as a proxy.

## Error handling
* **Never panic**
* Application should use it's **own** errors
* You should use dependencies' errors **only** in place the dependency is called
* Application errors should be described as package errors
* Better go-way is put errors into the structure which implements an **Error** interface. So you make errors more informative.

## Structures
* Avoid initialize structure directly. You should create an **NewStructName** constructor to create new variable for **StructName**.
* Avoid making public fields in structure. Fields should be private, you need to write  **getters/setters** accessors to receive/store it's values.

## Interfaces instead of structures
* Interfaces can be used to hide the implementation behind an interface, advantages are:
  1. You may **change** the certain implementation without changing client's sources.
  2. You may write mock-based **unittests**, for example using [mockery](https://github.com/vektra/mockery)
* You need to pay a lot of **attention** to choose functions/methods interfaces/signatures and return values.

## Logging
* Recommend to start with [logrus](https://github.com/sirupsen/logrus)
* Any logger should implement log levels (behind an interface) as follow:
  * Trace
  * Debug
  * Info
  * Warn
  * Error
  * Crit/Fatal
  * and their *Level*f implementations for formatted output, optional
* Additional requirements:
  * WithField/s - to log context data in separate fields, outside of log message
  * WithError - is a shortcut for `WithField("error", err)`

## Tracing
* Use [jaeger](https://www.jaegertracing.io/) for distributed tracing between microservices

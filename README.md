# Golang best practices

## About
here is my opinion about golang sofware development best practices

## Application's filesystem
```
.
├── Gopkg.toml
├── README.md
├── cmd
│   └── binA
│       ├── binA.go
│       ├── binA_test.go
│       └── testdata
│           └── testdata.yaml
├── configs
├── internal
│   └── packageA
│       ├── packageA.go
│       ├── packageA_test.go
│       └── testdata
│           └── testdata.yaml
├── migrations
│   └── 000000_init.sql
└── vendor
```
The main part of the application's filesystem is based according to the [standart](https://github.com/golang-standards/project-layout). Explanations are described [here](https://ieftimov.com/golang-package-multiple-binaries).

## Description
* **cmd** - put files which will become **executable** after build.  All files are included into the **package main**.
* **configs** - put configuration failed, probably, with default values.
* **internal** - put packages only for **internal** usage (import), but not as **external dependencies**. Each **file.go** is a separate **package**.
* **migrations** - migration files for [sql-migrate](https://github.com/rubenv/sql-migrate) tool
* **Gopkg.toml** - created by [dep](https://golang.github.io/dep/docs/introduction.html) tool dependecies list.
* **vendor** - created by [dep](https://golang.github.io/dep/docs/introduction.html) tool, contains dependencies. Dependecies are versioned with sources as well.

## Naming
* Variables, structures, interfaces and packges naming convensions are described in [official documention](https://golang.org/doc/effective_go.html). This rules are accepted **as is** if there will not be contra-arguments.

## Formatting
* Source code should be formatted before commit (push). Use [gofmt](https://golang.org/pkg/fmt/) tool for formatting. Formatter tool should exclude vendor directory. Usage example:
```
go fmt $(go list ./... | grep -v /vendor/
```
* Sources should be checked via [linter](https://github.com/golang/lint) tool, Linter should exclude the vendor directory, as fmt tool does. Usage example:
```
go list ./... | grep -v /vendor/ | xargs -L1 golint -set_exit_status
```

## Dependencies
* When there will be standart packages not enough, foreign packages should be appended as dependencies.
* The [dep](https://golang.github.io/dep/docs/introduction.html)-tool should be used to manage dependencies. Initialization call example (may take a lot of time):
```
$ dep init -v
```
* **vendor** directory and **Gopkg.lock** and **Gopkg.toml** files will be created in the result of it launch.
  * **Gopkg.lock** should be **excluded**(!!!) off the versioning
  * **vendor** and **Gopkg.toml** should be included into repository
* Dependencies should be updated atfer the new dependency appears in project (call example):
```
$ dep ensure -v
```
* To update existing dependency you should call:
```
$ dep ensure -v -update
```

## Error handling
* Application should use it's **own** errors
* You should use dependencies' errors **only** in place the dependency is called
* Application errors should be described as package errors
* Better go-way is put errors into the structure which implements an **Error** interface. So you make errors more informative,

## Structures
* Do **not** initialize structure directly. You should create an **NewStructName** function to create new variable for **StructName**.
* Avoid making public fields in structure. Fields should be private, you need to write  **getters/setters** accessors to receive/store it's values.

## Logging
* Any logger should implement log levels (behind an interface) as follow:
  * Trace
  * Debug
  * Info
  * Warn
  * Error
  * Crit/Panic/Fatal
  * and their *Level*f implementations for formatted output

## Interfaces instead of structures
* Hide the implementaion behind an interface is a good practice (like in Logging section), and that is why:
  1. You may **change** the certain implementation without changing client's sources.
  2. You may **extend** functionality using interfaces, see [embedding](https://travix.io/type-embedding-in-go-ba40dd4264df) and [go-inheritance](https://medium.com/@simplyianm/why-gos-structs-are-superior-to-class-based-inheritance-b661ba897c67)
  3. You may write mock-based **unittests**, for example using [mockery](https://github.com/vektra/mockery)
* You need to pay a lot of **attention** to choose functions/methods interfaces/signatures and return values.
* It's very important for dependencies

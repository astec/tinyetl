# tinyETL
Tiny ETL tool in Go language.

ETL stands for <b>E</b>xtract-<b>T</b>ransform-<b>L</b>oad

## Purpose
The purpose of the project was to implement a test task for a job interview.

The original task: filter list of customers in a range of 100km from a geo point and return id-name pairs sorted by ID (_ascending_).

## Build & Quality status

Build:[![CircleCI](https://circleci.com/gh/astec/tinyetl.svg?style=svg)](https://circleci.com/gh/astec/tinyetl)
[![Go Report Card](https://goreportcard.com/badge/github.com/astec/tinyetl)](https://goreportcard.com/report/github.com/astec/tinyetl)

## Architecture

- Workflow is defined by a chain of workers.
- Uses streaming processing where possible to minimize memory footprint.
- Data items are processed throw workflow wrapper in a `WorkItem` container
that holds data item as `Data() interface{}`. It also has a reference to a previous worker
for easier logging/troubleshooting in case of unexpected input. 
- Asynchronous processing is not built-in at the moment but should be easy to add.
Though current implementation allows workers return `etl.Iterator` and easily use goroutines for async processing.  
- The library was designed in such a way that it requires minimum
  boiler-plating & coding from developers.

## Project structure
- [`/examples/customers/main.go`](https://github.com/astec/tinyetl/blob/master/examples/customers/main.go) - main entry point for the job interview test.
- [`/examples/customers/customerscli/etl_workflow.go`](https://github.com/astec/tinyetl/blob/master/examples/customers/customerscli/etl_workflow.go) - ETL workflow initialization specific for our use case. Here we create fitler & sorter.
- [`/etl/`](https://github.com/astec/tinyetl/tree/master/etl) - ETL workflow library
- [`/etl/workers/`](https://github.com/astec/tinyetl/tree/master/etl/workers) - built-in ETL workflow workers

## How to run `examples/customers`
To run the program you would need Go language installed, preferably version 1.10.
1. change current directory to `examples/customers`
    ```
    cd $GOPATH/src/github.com/astec/tinyetl/examples/customers
    ```
2. Get all Go source code dependencies:
    ```
    go get ./...
    ```

3. Build the program using Go compiler
    ```
    go build .
    ```
    This should produce `customers` executable file (_`customers.exe` on Windows_).
    
4. To get hints for program arguments run with `--help` flag:
    ```
    >customers.exe --help
    usage: customers.exe [<flags>]
    
    Flags:
          --help         Show context-sensitive help (also try --help-long and
                         --help-man).
      -i, --input=INPUT  Input file or URL
      -s, --sort=SORT    Specifies how to sort customers: id, name. Prepend '-' for
                         descending order.
    ```

5. File to be processed can be specified with `--input` or short `-i` parameter. 
    ```
    customers --input=customers.txt
    ```
    It defaults to `customers.txt`.

6. Sorting can be specified with `--input` or short `-i` parameter. 
    ```
    customers --sort=id
    ```
    Currently 2 options supported: `id` and by `name`.
    
    If you want to sort in descending order prepend value with "`-`":
    ```
    customers --sort=-id
    ```

## How to run tests
Tests & unit tests are implemented using standard Go testing convention.
Test files are located next to code files with `_test` postfix. E.g.:
```
code.go
code_test.go
```

To run all tests:
```
go test ./...
```

Some quick links for tests:
- End to end tests: [/examples/customers/customerscli/end2end_test.go](https://github.com/astec/tinyetl/blob/master/examples/customers/customerscli/end2end_test.go)
  
## Notes about `examples/customers` implementation
1. To minimize memory footprint `CustomerExtended` structure is mapped to `CustomerShot` before sorting.
2. [ffjson](https://github.com/pquerna/ffjson) autogenerated code is used to speedup JSON unmarshalling.
3. To speedup development process input file name defaults to `customers.txt` if not specified.   

## Missing functionality in ETL library
This is a test demo project. Due to time & purpose constraint it misses few things essential for production use. E.g:
- Logging
- Statistics & Performance counters
- etc. 



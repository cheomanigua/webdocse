---
weight: 5050
title: "Basics"
description: ""
icon: "article"
date: "2025-10-20T09:23:34+02:00"
lastmod: "2025-10-20T09:23:34+02:00"
draft: false
toc: true
---

## Installation

### Debian

1. Download the appropiate tarball in [https://go.dev/dl/](https://go.dev/dl/)
2. Extract to `/usr/local/`:
	```bash
	$ sudo tar -C /usr/local -xzf go1.25.3.linux-amd64.tar.gz
	```
3. Add **Go** to your PATH by editing your shell configuration `~/.bashrc`:
	```bash
	$ echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
	$ source ~/.bashrc
	```
4. Verify:
	```bash
	$ go version
	$ which go
	$ echo $PATH
	```
## Commands

##### go run

- `go run main.go` executes the file, but does not produce a standalone binary file.

##### go build

- `go build hello.go` compiles your application, including any packages and their dependencies. It creates a binary file on disk with the same name or reasonable name, but does not execute your program.
- `go build -ldflags "-w -s" hello.go` is the same as above, but reduces the file size of the binary by removing debugging information and the symbol table.
- `GOOS="linux" GOARCH="amd64" go build hello.go` compiles your application to the especified OS and architecture.

##### go doc

- `go doc fmt.Println` shows the source code comments of a particular package, function, method or variable, in this case **fmt.Println**.

##### go get

- `go get github.com/stacktitan/ldapauth` obtains the **ldapauth** package source code that appears imported in your **Go** file import section. However, to avoid backward compatibility when the package receives an update, **Go** has introduced two separate tools: `dep` and `mod` to lock dependencies.

##### go fmt

- `go fmt hello.go` will style your code by enforcing the use of proper line breaks, indentation and brace alignment.

##### go vet

`go vet` inspects your code and uses heuristics to identify suspicious constructs, such as calling Printf() with the incorrect format string
types. The go vet command attempts to identify issues, some of which might
be legitimate bugs, that a compiler might miss.

- `go vet` vets the package in the current directory
- `go vet /my/project` vets the packages whose path is provided


## Tools

##### golint

golint is a standalone tool, and not a subcommand of the main go binary. You’ll need to install it separately by using `go get -u golang.org/x/lint/golint`.

`golint` reports style mistakes such as missing comments, variable naming that doesn’t follow conventions, useless type specifications, and more.

##### Go Playground

- [https://play.golang.org/](https://play.golang.org/). Go Playground is an execution environment that provides a web-based frontend for developers to quickly develop, test, execute, and share snippets of Go code.

##### A Tour of Go

- [https://tour.golang.org/](https://tour.golang.org/). A Tour of Go is a comprehensive, hands-on discussion of the language broken into bite-sized lessons that use an embedded playground to enable you to try out each of the concepts.

##### Effective Go

- [https://go.dev/doc/effective_go](https://go.dev/doc/effective_go). This document gives tips for writing clear, idiomatic Go code. It augments the language specification, the Tour of Go[](https://tour.golang.org/), and How to Write Go Code[](https://go.dev/doc/code), all of which you should read first.

## Variables

```go
package main

var x = 10			// Package-level variable, inferred integer
var int x = 10		// Package-level variable

func main() {
    var x = 10		// Function-level variable, inferred integer
    var int x = 10	// Function-level variable
    x := 10			// Only Function-level variable, infered integer
    x := (int)10	// Only Function level variable
}
```

## Slices

```go
var s = make([]string, 0)
s = append(s, "some string")
```

## Maps

```go
var m = make(map[string]string)
m["some key"] = "some value"
```

## Control structures

### if/else

```go
if x == 1 {
	fmt.Println("X is equal to 1")
} else {
	fmt.Println("X is not equal to 1")
}
```

### switch

```go
switch x {
	case "foo":
		fmt.Println("Found foo")
	case "bar":
		fmt.Println("Found bar")
	default:
		fmt.Println("Default case")
}
```

### for

```go
for i := 0; i < 10; i++ {
	fmt.Println(i)
}
```

The following example shows a slight variation of the for loop that loops over a collection, such as a slice or a map:

```go
nums := []int{2,4,6,8}

for idx, val := range nums {
	fmt.Println(idx, val)
}
```

## Concurrency

```go
func f() {
	fmt.Println("f function")
}

func main() {
	go f()
	time.Sleep(1 * time.Second)
	fmt.Println("main function")
}
```

### Channels

Go contains a data type called channels that provide a mechanism through which goroutines can synchronize their execution and communicate with one another. Let’s look at an example that uses channels to display the length of different strings and their sum simultaneously:

```go
func strlen(s string, c chan int) {
	c <- len(s)
}

func main() {
	c := make(chan int)
	go strlen("Salutations", c)
	go strlen("World", c)
	x, y := <-c, <-c
	fmt.Println(x, y, x+y)
}
```

First, you define and use a variable `c` of type `chan int`. You can define channels of various types, depending on the type of data you intend to pass via the channel. In this case, you’ll be passing the lengths of various strings as integer values between goroutines, so you should use an `int` channel.

Notice a new operator: `<-`. This operator indicates whether the data is flowing to or from a channel. You can think of this as the equivalent of placing items into a bucket or removing items from a bucket.

The function you define, `strlen()`, accepts a word as a string, as well as a channel that you’ll use for synchronizing data. The function contains a single statement, `c <- len(s)`, which uses the built-in `len()` function to determine the length of the string, and then puts the result into the `c` channel by using the `<-` operator.

The `main()` function pieces everything together.

- First, you issue a call to `make(chan int)` to create the integer channel.
- You then issue multiple concurrent calls to the `strlen()` function by using the `go` keyword, which spins up multiple goroutines. You pass to the `strlen()` function two string values, as well as the channel into which you want the results placed.
- Lastly, you read data from the channel by using the `<-` operator, this time with data flowing from the channel. This means you’re taking items out of your bucket, so to speak, and assigning those values to the variables `x` and `y`. Note that execution blocks at this line until adequate data can be read from the channel.

When the line completes, you display the length of each string as well as their sum to stdout. In this example, it produces the following output:

```
5 11 16
```

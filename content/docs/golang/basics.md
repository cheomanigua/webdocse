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

## Arrays

##### Declaration/Initialization

```go
// Declare an array (unitialized - nil)
var nums [5]int			// [0, 0, 0, 0, 0]

// Initialization
nums := [5]int{1, 2}			// [1, 2, 0, 0, 0]
nums := [5]int{1, 2, 3, 4, 5}	// [1, 2, 3, 4, 5]
nums := [...]int{1 ,2}			// [1, 2]
nums := [...]int{1, 2, 3, 4, 5}	// [1, 2, 3, 4, 5]
```

## Slices

##### Declaration/Initialization

```go
// Declare a slice (uninitialized - nil)
var numbers []int

// Initialize with make()
numbers := make([]int, 0)        // empty slice
numbers := make([]int, 5)        // length 5, capacity 5
numbers := make([]int, 3, 10)    // length 3, capacity 10

// len and cap functions
fmt.Println(len(numbers)	// 3
fmt.Println(cap(numbers)	// 10

// Initialize with Literal syntax (preferred)
numbers := []int{1, 2, 3, 4, 5}
```

##### Common Patterns

```go
// Empty slice
empty := []int{}

// Pre-allocated with capacity
numbers := make([]int, 0, 100)  // capacity 100, length 0

// From array
arr := [5]int{1, 2, 3, 4, 5}
slice := arr[:]

// From string
chars := []rune("hello")  // []rune for Unicode
bytes := []byte("hello")  // []byte
```

##### Key Operations

```go
slice := []int{1, 2, 3, 4, 5}

// Append (most important!)
slice = append(slice, 6, 7, 8)  // [1,2,3,4,5,6,7,8]

// Append to pre-allocated slice
slice2 := make([]int, 0, 10)
slice2 = append(slice2, 1, 2, 3)  // No reallocation

// Copy
slice3 := make([]int, len(slice))
copy(slice3, slice)

// Slicing
subset := slice[1:4]     // [2,3,4]
prefix := slice[:3]      // [1,2,3]
suffix := slice[2:]      // [3,4,5,6,7,8]

// Zero slice (don't do this - keeps capacity)
slice = slice[:0]        // length becomes 0
```

## Arrays/Slices Iteration

```go

numbers := [4]int{10, 20, 30, 40}   // Array
numbers := []int{10, 20, 30, 40}    // Slice

// All 4 ways work
for i := 0; i < len(numbers); i++ {
    fmt.Println(numbers[i])
}

for i, v := range numbers {
    fmt.Printf("Index %d: %d\n", i, v)
}

for i := range numbers {
    fmt.Printf("Index %d: %d\n", i, numbers[i])
}

for _, v := range numbers {
    fmt.Println(v)
}
```

## Maps

```go
// declaration
var m = map[string]string{}	// the same as below	
var m = make(map[string]string)	// the same as above

// assignement
m["Name"] = "John Doe"

// access
fmt.Println(m["Name"])	// Prints John Doe

// Declaration and assigment
m := map[string]int{"a": 1, "b": 2, "c": 3}

// Deletion
delete(m, "a")

// Iterate over all key-value pairs (order is random)
for key, value := range m {
    fmt.Printf("%s: %d\n", key, value)
}

// Only keys
for key := range m {
    fmt.Println(key)
}

// Only values
for _, value := range m {
    fmt.Println(value)
}

// Only values
for key := range m {
    fmt.Println(m[key])
}
```

##### Checking key existence

```go
users := map[string]int{"John": 42}
name := "John"

if age, exists := users[name]; exists {
    fmt.Printf("%s is %d years old\n", name, age)
    return
}

fmt.Printf("Couldn't find %s in the users map\n", name)
```

##### Testing presence only

```go
func main() {
    words := map[string]int{
        "brown": 1,
        "dog":1,
        "fox":1,
        "jumps": 1,
        "lazy":1,
        "over":1,
        "quick": 1,
        "the": 2,
    }
    // discard the value using '_'
    // keep only the existence boolean
    _, exists := words["foo"]
    fmt.Println(exists) // false
}
```

##### Exploiting Zero Value

```go
func main() {
    counts := map[string]int{}
    sentence := "The quick brown fox jumps over the lazy dog"
    words := strings.Fields(strings.ToLower(sentence))
    for _, w := range words {
        counts[w]++
    }
    fmt.Println(counts) // map[brown:1 dog:1 fox:1 jumps:1 lazy:1 over:1 quick:1 the:2]
}
```

##### Listing and Sorting

Go does not provide a way to get just a list of keys or values from a map. To build a list of keys or values, you must iterate over the map and save the keys or values to a slice or array.

In Go, maps are not ordered and there are no built-in methods for sorting maps. When iterating over a map, the order of the keys is not guaranteed.

Below is an example of listing and sorting keys:

```go
func main() {
    months := map[int]string{
        1:"January",
        2:"February",
        3:"March",
        4:"April",
        5:"May",
        6:"June",
        7:"July",
        8:"August",
        9:"September",
        10: "October",
        11: "November",
        12: "December",
    }
    keys := make([]int, 0, len(months))
    // loop through the map
    for k := range months {
        // append the key to the slice
        keys = append(keys, k)
    }
    sort.Ints(keys)
    for _, k := range keys {
        fmt.Printf("%02d: %s\n", k, months[k])
    }
}
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
In Go, it is considered best practice to avoid using the else statement whenever possible. This code is clearer:

```go
if x == 1 {
	fmt.Println("X is equal to 1")
    return
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

### switch

```go
switch x {
	case "foo":
		fmt.Println("Found foo")
	case "bar":
		fmt.Println("Found bar")
	default:
		fmt.Println("Nothing found")
}
```

##### Fallthrough

When there is a need to match more than one condition, you can use `fallthrough` to allow more than one case to be matched.

```go
func RecommendActivity(temp int) {
    fmt.Printf("It is %d degrees out. You could", temp)
    switch {
        case temp <= 32:
            fmt.Print(" go ice skating,")
            fallthrough
        case temp >= 45 && temp < 90:
            fmt.Print(" go jogging,")
            fallthrough
        case temp >= 80:
            fmt.Print(" go swimming,")
            fallthrough
        default:
            fmt.Print(" or just stay home.\n")
    }
}

func main() {
    RecommendActivity(19)
    RecommendActivity(45)
    RecommendActivity(90)
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

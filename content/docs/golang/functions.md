---
weight: 5075
title: "Functions"
description: "Using functions in Go"
icon: "article"
date: "2025-10-25T12:16:30+02:00"
lastmod: "2025-10-25T12:16:30+02:00"
draft: false
toc: true
---

## Function Definitions

### Basic

A basic `sayHello` function with no argument and no return argument

```go
func main() {
	sayHello()
}

func sayHello() {
	fmt.Println("Hello")
}
```

### Arguments

```go
func main() {
	sayHello("Hello")
}

func sayHello(greeting string) {
	fmt.Println(greeting)
}
```

### Same type arguments

When declaring multiple arguments of the same type, the type only needs to be declared at the end of the argument list.

```go
func main() {
	sayHello("Hello", "Kurt")
}

func sayHello(greeting, name string) {
	fmt.Printf("%s, %s\n", greeting, name)
}
```

### Return arguments

The type of the return value is declared after the function definition. The function `sayHello` returns a `string` value.

```go
func main() {
	fmt.Println(sayHello())
}

func sayHello() string {
	return "Hello"
}
```

### Multiple return arguments

Functions can return 0 to N numbers of values, though it is considered best practice to not return more than two or three.

Multiple return arguments need to be placed inside of a set of (). The function `info` returns three values: `string`, `int`, and `int`.

```go
func main() {
	names := []string{"Kurt", "Janis", "Jimi", "Amy"}
	gs, length, capacity := info(names)
	fmt.Println(gs)
	fmt.Println("len: ", length)
	fmt.Println("cap: ", capacity)
}

func info(s []string) (string, int, int) {
	gs := fmt.Sprintf("%#v", s)
	l := len(s)
	c := cap(s)
	return gs, l, c
}
```

### Ignored return arguments

Unwanted return arguments can be ignored by using the _ placeholder. For example, we are only interested in capturing the second return value from the `info` function above. Using the `_` operator, we can discard the values we don’t want.

```go
_, length, _ := info(names) // instead of `gs, length, capacity := info(names)`
fmt.Println("len: ", length)
```

### Functions as Variables

```go
func main() {
	f := func() {
		return "Hello"
	}

	fmt.Println(f())	// Hello
}
```

### Functions as Arguments

```go
func sayHello(fn func() string){
	fmt.Println(fn())
}
```

Calling a Function with a Function as an Argument:

```go
func main() {
	f := func() string {
		return "Hello"
	}
	sayHello(f);	// Hello
}

func sayHello(fn func() string){
	fmt.Println(fn())
}
```

### Closures

Functions close over their environment when they are defined. This means that a function has access to arguments declared before its declaration.

```go
func main() {
	name := "Janis"

	f := func() {
		fmt.Printf("Hello %s", name)
	}

	sayHello(f);
}

func sayHello(fn func()){
	fn()
}
```

### Anonymous Functions

Anonymous functions are functions that aren’t assigned to a variable or that don’t have a “name” associated with them.

This is a very common pattern in Go, and one you should get comfortable seeing and using in Go code. Using anonymous functions is very common when working with the `net/http` package, creating your own iterators, or allowing others to extend your application’s functionality.

```go
func main() {
	name := "Janis"

	sayHello(func() {
		fmt.Printf("Hello %s", name)
	})
}

func sayHello(fn func()){
	fn()
}
```

### Functions accepting arguments from Functions

Functions can accept the return arguments of one function as its input arguments. This is only possible as long as the return types and number of return arguments are identical to the input arguments and number of input arguments.

```go
func main() {
	takeTwo(returnTwo())	// hello world
}

func returnTwo() (string, string) {
	return "hello", "world"
}

func takeTwo(a string, b string) {
	fmt.Println(a, b)
}
```

## Variadic arguments

### Basic

Functions can take variadic arguments, meaning 0 to N arguments of the same type. This is done by pre-pending the type of the argument with `...`

```go
func main() {
	// call sayHello with a lot of names
	sayHello("Kurt", "Janis", "Jimi", "Amy")

	// call sayHello with only one name
	sayHello("Brian")
	
	// call sayHello with no names
	sayHello()
}

func sayHello(names ...string) {
	// iterate over the names
	for _, n := range names {
		fmt.Printf("Hello %s\n", n)
	}
}
```

Prints:

```
Hello Kurt
Hello Janis
Hello Jimi
Hello Amy
Hello Brian
```

### Expanding Slices

Slices cannot be passed directly to functions using variadic arguments. However, slices can be “expanded” using a trailing variadic operator, ..., to send them as individual arguments to a function.

```go
func main() {
	users := []string{"Kurt", "Janis", "Jimi", "Amy"}
	sayHello(users...)
}

func sayHello(names ...string) {
	// iterate over the names
	for _, n := range names {
		fmt.Printf("Hello %s\n", n)
	}
}
```

Prints:

```
Hello Kurt
Hello Janis
Hello Jimi
Hello Amy
```

### When to use a Variadic argument

While using a variadic argument is not the common use case, with a variadic function signature, the LookupUsers function can now be called with a single id or a list of ids.

```go
type User struct{}	// Empty struct just for the example

func main() {
	id1 := 1
	id2 := 2
	id3 := 3
	ids := []int{id1, id2, id3}

	LookupUsers(id1)
	LookupUsers(id1, id2, id3)
	LookupUsers(ids...)
}

func LookupUsers(ids ...int) []User {
	var users []User
	for _, id := range ids {
		fmt.Printf("looking up id: %d\n", id)
	}
	return users
}
```


Prints:

```looking up id: 1
looking up id: 1
looking up id: 2
looking up id: 3
looking up id: 1
looking up id: 2
looking up id: 3
```

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

### Basics

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

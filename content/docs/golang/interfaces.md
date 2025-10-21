---
weight: 5300
title: "Interfaces"
description: "Pointers, Structs and Interfaces working together"
icon: "article"
date: "2024-08-13T10:42:07+02:00"
lastmod: "2024-08-13T10:42:07+02:00"
draft: false
toc: true
---

### Example 1

```go
package main

import "fmt"

type Friend interface {
	SayHello()
}

type Person struct {
	Name string
}

type Dog struct {}

func Greet(f Friend) {
	f.SayHello()
}

func (p *Person) SayHello() {
	fmt.Println("Hello,", p.Name)
}

func (d *Dog) SayHello() {
	fmt.Println("Woof woof")
}

func main() {
	var foo = new(Person)
	var bar = new(Dog)

	foo.Name = "Foo"

	foo.SayHello() // Prints: Hello, Foo
	bar.SayHello() // Prints: Woof woof

	Greet(foo) // Prints: Hello, Foo
	Greet(bar) // Prints: Woof woof
}
```

The *Example 1* shows two types, Person and Dog, that are able to SayHello() and, therefore, are a Friend. You are able to Greet() both a Person and a Dog, since both are capable of SayHello().

### Example 2

```go
package main

import (
	"fmt"
	"math"
)

type geometry interface {
	area() float64
	perim() float64
}

type rect struct {
	width, height float64
}

type circle struct {
	radius float64
}

func (r rect) area() float64 {
	return r.width * r.height
}

func (r rect) perim() float64 {
	return 2*r.width + 2*r.height
}

func (c circle) area() float64 {
	return math.Pi * c.radius * c.radius
}

func (c circle) perim() float64 {
	return 2 * math.Pi * c.radius
}

func measure(g geometry) {
	fmt.Println(g)
	fmt.Println(g.area())
	fmt.Println(g.perim())
}

func main() {
	r := rect{width: 3, height: 4}
	c := circle{radius: 5}

	fmt.Println("Rectangle:")
	measure(r)
	fmt.Println("Circle:")
	measure(c)
}
```

Execute:

```
Rectangle:
{3 4}
12
14
Circle:
{5}
78.53981633974483
31.41592653589793
```

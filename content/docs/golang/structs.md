---
weight: 5200
title: "Structs"
description: "Pointers, Structs and Interfaces works together"
icon: "article"
date: "2024-08-13T10:41:58+02:00"
lastmod: "2024-08-13T10:41:58+02:00"
draft: false
toc: true
---

# Struct

Structs lack scoping modifiers such as private, public, or protected that are commonly used in other languAges to control access to their members. Instead, Go uses capitalization to determine scope: types and fields that begin with a capital letter are exported and accessible outside the package, whereas those starting with a lowercase letter are private, accessible only within the package.

## Declaration

```go
type Person struct {
	Name string
	Age int
	Pet string
}
```

## Initialization

There are several ways to initialize a struct:

##### Value initalization

```go
var bob Person

bob := Person{}

bob := Person{
	"Bob",
	40,
	"cat",
}

bob := Person{
	Name: "Bob",
	Age: 40,
	Pet: "cat",
}
```

##### Pointer initialization

```go
var bob = new(Person)

bob := &Person{}

bob := &Person{"Bob", 40, "cat"}

bob := &Person{
	Name: "Bob",
	Age: 40,
	Pet: "cat",
}
```

### Value vs Pointer - Initalization

| Situation | Prefer value (`Person`) | Prefer pointer (`*Person`) |
|-----------|------------------------|----------------------------|
| Small struct, no methods that need mutation | `Person` (cheaper, no indirection) | — |
| Method receiver is `*Person` (mutating) | — | `*Person` (or take address of a value) |
| Struct contains sync.Mutex / other non-copyable fields | — | `*Person` (copying would be illegal) |
| Very large struct (many fields, big arrays) | — | `*Person` (avoids copying on every assignment) |
| Want to share the same instance across functions | — | `*Person` (or pass value and let caller decide) |


### Value vs Pointer - in depth analysis

```go
package main

import "fmt"

type Person struct {
	Name string
}

func (p *Person) SayHello() {
	fmt.Println("Hello,", p.Name)
}

func main() {
	var foo = new(Person)
	var bar Person

	foo.Name = "Foo"
	bar.Name = "Bar"

	fmt.Println("foo:", foo) // Prints: foo: &{Foo}
	fmt.Println("bar:", bar) // Prints: bar: {Bar}

	foo.SayHello() // Prints: Hello, Foo
	bar.SayHello() // Prints: Hello, Bar
}
```

In Go code example above, the declarations of `foo` and `bar` differ in how they initialize a `Person` struct, and this has implications for their type, memory allocation, and usage. Let's break it down:

1. **`var foo = new(Person)`**:
   - **What it does**: The `new` function allocates memory for a `Person` struct and returns a *pointer* to it (`*Person`). The struct is initialized with its zero values (for `string`, that's an empty string `""`).
   - **Type of `foo`**: `foo` is a pointer to a `Person` struct, i.e., `*Person`.
   - **Memory**: The struct is allocated on the heap, and `foo` holds the memory address of this struct.
   - **Usage**: Since `foo` is a pointer, you can call methods with pointer receivers (like `SayHello`) directly on it. For example, `foo.SayHello()` works because the method expects a `*Person`.

2. **`var bar Person`**:
   - **What it does**: This declares a variable `bar` of type `Person` (not a pointer). It is initialized with the zero value of the `Person` struct (again, `Name` is an empty string `""`).
   - **Type of `bar`**: `bar` is a `Person` struct, i.e., a value type, not a pointer.
   - **Memory**: The struct is allocated wherever the variable is declared (could be stack or heap, depending on context, but typically stack for local variables). No separate memory allocation is needed.
   - **Usage**: You can call methods on `bar`, but since `SayHello` has a pointer receiver (`*Person`), Go automatically takes the address of `bar` when calling `bar.SayHello()` (this is syntactic sugar for `(&bar).SayHello()`). However, if you need to pass `bar` to a function expecting a `*Person`, you must explicitly use `&bar`.

### Value vs Pointer - Key Differences
| Aspect                  | `foo` (`new(Person)`)                     | `bar` (`Person`)                        |
|-------------------------|-------------------------------------------|-----------------------------------------|
| **Type**                | `*Person` (pointer to `Person`)           | `Person` (value type)                   |
| **Initialization**      | Allocated with `new`, returns pointer      | Declared as value, zero-initialized     |
| **Memory Allocation**   | Always on the heap                        | Typically on the stack (context-dependent) |
| **Method Call**         | Directly compatible with `*Person` methods| Auto-converted to pointer for `*Person` methods |
| **Assignment**          | Modifying `foo.Name` affects the pointed-to struct | Modifying `bar.Name` affects the local copy |
| **Passing to Functions**| Passed as pointer, changes affect original| Passed as value unless `&bar` is used    |


### Value vs Pointer - Additional Notes
- **When to use `new(Person)` vs. `Person{}`**:
  - Use `new(Person)` or `&Person{}` when you explicitly need a pointer (e.g., to share the struct across functions or to satisfy a pointer receiver).
  - Use `Person{}` or `var p Person` when you want a value type, which is simpler for local, non-shared data.
- **Alternative to `new(Person)`**: You could also write `foo := &Person{}` to achieve the same result as `new(Person)`. Both return a `*Person` with zero values.
- **Zero values**: Both `foo` and `bar` start with `Name` as an empty string because that's the zero value for `string` in Go.
- **Performance**: Declaring `var bar Person` is generally more lightweight (stack allocation) compared to `new(Person)` (heap allocation), but the difference is often negligible unless you're optimizing for performance.

In summary, `foo` is a pointer to a `Person` struct allocated on the heap, while `bar` is a `Person` struct value, typically on the stack. The choice depends on whether you need pointer semantics (e.g., for mutability across functions) or value semantics.
## Access

```go
bob.Name = "Bob"
fmt.Println(bob.Name)
```





## Anonimous Struct

You can also declare that a variable implements a struct type without first giving the struct type a Name. This is called an anonymous struct and can only be instantiated once.

```go
var Person struct {
	Name string
	Age int
	Pet string
}
```

```go
Person.Name = "Bob"
Person.Age = 40
Person.Pet = "cat"
```
or

```go
Person := struct {
	Name string
	Age int
	Pet string
}{
	Name: "Bob",
	Age: 40,
}
```

You might wonder when it’s useful to have a data type that’s associated only with a single instance. Anonymous structs are handy in two common situations:

1. The first is when you translate external data into a struct or a struct into external data (like JSON or Protocol Buffers). This is called *unmarshaling* and *marshaling* data, respectively.
2. Writing tests is another place where anonymous structs pop up.

## Composition

Composition is similar to OOP languAges inheritance. While Go doesn’t have inheritance, it encourAges code reuse via built-in support for composition and promotion.

```go
package main

import "fmt"

type employee struct {
	Name    string
	Age     uint8
	address
}

type address struct {
	street string
	city  string
}

var employee1 employee = employee{"John Doe", 43, address{"Main Street", "London"}}

func main() {
	fmt.Println(employee1.Name, employee1.Age, employee1.street, employee1.city)
}
```

The above code will print: `John Doe 43 Main Street London`


## Methods & Pointers

Methods are functions on user defined types like structs. They are the equivalent to class methods in OOP languAges. Method declarations look like function declarations, with one addition: the `receiver` specification. The receiver appears between the keyword *func* and the Name of the method.

Pointers are better explained at the [pointers pAge]( {{<ref "pointers">}}), but in the example below you see how they work together with methods.

```go
type Employee struct {
	Name    string
	Age     int
}

func (e Employee) updateName(newName string) {
	e.Name = newName
	fmt.Println(e.Name)  // It will print Bob
}

func (e *Employee) updateNamePointer(newName string) {
	e.Name = newName
	fmt.Println(e.Name)  // It will print Bob
}

func main() {
	employee1 := Employee {
		Name:   "Alice",
		Age:    38,
	}

	employee1.updateName("Bob") // The Name will not be updated to Bob. It will remain Alice
	employee1.updateNamePointer("Bob") // The Name will be updated to Bob.

	fmt.Println("Employee Name:", employee1.Name)
	fmt.Println("Employee Age:", employee1.Age)
}
```

---
weight: 5400
title: "Http"
description: ""
icon: "article"
date: "2024-08-13T10:42:13+02:00"
lastmod: "2024-08-13T10:42:13+02:00"
draft: false
toc: true
---

# Basics

Processing HTTP requests with Go is primarily about two things: `handlers` and `servemuxes`.

- Handlers are responsible for carrying out your application logic and writing response headers and bodies.
- A Servemux (also known as a router) stores a mapping between the predefined URL paths for your application and the corresponding handlers. Usually you have one servemux for your application containing all your routes.

Go's [net/http](https://pkg.go.dev/net/http) package ships with the simple but effective [ServeMux](https://pkg.go.dev/net/ServeMux) servemux, plus a few functions to generate common handlers including [http.FileServer()](https://pkg.go.dev/net/http/#FileServer), [http.NotFoundHandler()](https://pkg.go.dev/net/http/#NotFoundHandler) and [http.RedirectHandler()](https://pkg.go.dev/net/http/#RedirectHandler).

Let's take a lood at the simplest web server:

```go
package main

import (
	"net/http"
)

func main() {
	http.ListenAndServe(":8080", http.FileServer(http.Dir("./static")))
}
```
`http.ListenAndServe` creates the server.
`:8080` tells the server to listen for requests on port 8080.
`http.FileServer` servers the files to the client.
`http.Dir("./static")` instructs `http.FileServer` to look at the host's `static`directory for the files to serve.

By default, `net/http` will use `index.html` as the first file to look at. This is the structure of the project:

```
server/
    |_ go.mod
    |_ server.go
    |_ static/
        |_ index.html
        |_ about.html
        |_ contact.html
```

The simplest web server making use of Effective Go writing style: clear and idiomatic Go code:



```go
package main

import (
	"log"
	"net/http"
)

func main() {
	mux := http.NewServeMux()
	mux.Handle("/", http.FileServer(http.Dir("./static")))

	log.Print("Listening on :8080...")
	err := http.ListenAndServe(":8080", mux)
	if err != nil {
		log.Fatal(err)
	}
}
```

We can see now in the code above the `http.Handle`. What is it? It is a built in function that registers the handler for the given pattern in DefaultServerMux. You can check those patterns [here](https://pkg.go.dev/net/http#hdr-Patterns). Basically the first parameter declares the pattern to match, and the second paramenter instructs what the handler actually does when the match occurs.

The handlers that ship with `net/http` are useful, but most of the time when building a web application you'll want to use your own custom handlers instead. So how do you do that? Check below.

# 1. Handle, HandlerFunc and HandleFunc

### 1.1 Handle

`http.Handle` is a **function** that registers the handler for the given pattern in `[DefaultServer]`. So, `http.Handle` is a function with two parameters:

1. A pattern of the route.
2. An object of a user-defined type that implements the handler interface similar to ListenAndServe().

Let's see with an example. This server shows the date and time:

```go
package main

import (
	"log"
	"net/http"
	"time"
)

type timeHandler struct {
	format string
}

func (th timeHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	tm := time.Now().Format(th.format)
	w.Write([]byte("The time is: " + tm))
}

func main() {
	mux := http.NewServeMux()
	mux.Handle("/time", timeHandler{format: time.RFC822}) // <---- Handle
	log.Print("Listening...")
	http.ListenAndServe(":8080", mux)
}
```

### 1.2 HandlerFunc

For simple cases like the example above, defining new a custom type (in the above case a struct) just to make a handler feels a bit verbose. Fortunately, we can rewrite the handler as a simple function instead by coercing the fuction into being a handler by converting it to a [http.HandlerFunc](https://pkg.go.dev/net/http/#HandlerFunc):

```go
package main

import (
	"log"
	"net/http"
	"time"
)

func timeHandler(w http.ResponseWriter, r *http.Request) {
	tm := time.Now().Format(time.RFC822)
	w.Write([]byte("The time is: " + tm))
}

func main() {
    mux := http.NewServeMux()
    mux.Handle("/time", http.HandlerFunc(timeHandler))
    log.Print("Listening...")
    http.ListenAndServe(":8080", mux)
}
```

`http.HandlerFunc` type is an **adapter class** to allow the use of ordinary functions as HTTP handlers. If '**f**' is a function with the appropiate signature, HandlerFunc(f) is a [`http.Handler`](https://pkg.go.dev/net/http#Handler) that calls '**f**'.

Functions with the signature `func(w http.ResponseWriter, r *http.Request)` are http handler funcs that can be converted to an `http.Handler` using the [`http.HandlerFunc`](https://pkg.go.dev/net/http#HandlerFunc) type. Notice that the signature is the same as the signature of the `http.Handler`'s `ServeHTTP` method.

The expression `http.HandlerFunc(myHandlerFunc)` converts the `myHandlerFunc` function to the type `http.HandlerFunc` which implements the `ServeHTTP` method so the resulting value of that expression is a valid `http.Handler` and therefore it can be passed to the `http.Handle("/", ...)` function call as the second argument.

Using plain http handler funcs instead of http handler types that implement the `ServeHTTP` method is common enough that the standard library provides the alternatives [`http.HandleFunc`](https://pkg.go.dev/net/http#HandleFunc) and [`ServeMux.HandleFunc`](https://pkg.go.dev/net/ServeMux.HandleFunc). All `HandleFunc` does is what we do in the above example, it converts the passed in function to `http.HandlerFunc` and calls http.Handle with the result.

The `http.HandlerFunc()` converts a function to the `http.Handler` type so that it can be used as a normal handler in the `http.Handle()` method. In simple words, when you pass a function to the `http.HandlerFunc()`, it implements the `http.Handler` type automatically by adding `ServeHTTP()` method. You don't have to write `ServeHTTP()` method manually for your function. Therefore, your normal function becomes a handler.



### 1.3 HandleFunc

Converting a function to a `http.HandlerFunc` type and then adding it to a servemux like in the example above is so common that Go provides a shortcut: the [mux.HandleFunc](https://pkg.go.dev/net/ServeMux.HandleFunc) method. You can use this like so:


```go
func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/time", timeHandler)
	log.Print("Listening...")
	http.ListenAndServe(":8080", mux)
}
```

`http.HandleFunc` is a **function** that registers the handler function for the the given pattern in `[DefaultServerMux]`. The documentation for `[ServerMux]` explains how patterns are matched.

You could also this:

```go
func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/time", timeHandler)
    mux.Handle("/time", func(w http.ResponseWriter, r *http.Request) {
        tm := time.Now().Format(time.RFC822)
        w.Write([]byte("The time is: " + tm))
    })
	log.Print("Listening...")
	http.ListenAndServe(":8080", mux)
}
```

More convenient for registering simple function-based handlers, including anonymous functions.

### 1.4 Passing variables to handlers

Most of the time using a function as a handler like this works well. But there is a bit of a limitation when things start getting more complex.

You've probably noticed that, unlike the method before, we've had to hardcode the time format in the `timeHandler` function. What happens when you want to pass information or variables from `main()` to a handler?

A neat approach is to put our handler logic into a closure, and close over the variables we want to use, like this:

```go
func timeHandler(format string) http.Handler {
	fn := func(w http.ResponseWriter, r *http.Request) {
		tm := time.Now().Format(format)
		w.Write([]byte("The time is: " + tm))
	}
	return http.HandlerFunc(fn)
}

func main() {
	mux := http.NewServeMux()
	mux.Handle("/time", timeHandler(time.RFC1123))
	log.Print("Listening...")
	http.ListenAndServe(":8080", mux)
}
```

The `timeHandler()` function now has a subtly different role. Instead of coercing the function into a handler (like we did previously), we are now using it to return a handler.

In this example we've just been passing a simple string to a handler. But in a real-world application you could use this method to pass database connection, template map, or any other application-level context. It's a good alternative to using global variables, and has the added benefit of making neat self-contained handlers for testing.

You might also see this same pattern written as:

```go
func timeHandler(format string) http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		tm := time.Now().Format(format)
		w.Write([]byte("The time is: " + tm))
	})
}
```

Or using an implicit conversion to the `http.HandlerFunc` type on return:

```go
func timeHandler(format string) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		tm := time.Now().Format(format)
		w.Write([]byte("The time is: " + tm))
	}
}
```

### Key Differences Between mux.Handle and mux.HandleFunc

- `mux.Handle(pattern string, handler http.Handler)`:
 
    - Requires an `http.Handler` (something that implements the `http.Handler` interface, i.e., has a `ServeHTTP` method).
    - You must explicitly convert a function to `http.HandlerFunc` if you’re using a function as the handler.
    - Used for registering custom handlers (e.g., structs that implement `http.Handler`) or explicitly converted function handlers.
 
 
- `mux.HandleFunc(pattern string, handler func(http.ResponseWriter, *http.Request))`:
 
    - Accepts a function with the signature `func(http.ResponseWriter, *http.Request)` directly.
    - Internally wraps the function in `http.HandlerFunc` to make it an `http.Handler`.
    - More convenient for registering simple function-based handlers, including anonymous functions.

# 2. Handler

So, what is a handler?

`http.Handler` is an **interface** that responds to an HTTP request. [http.Handler.ServeHTTP] should write reply headers and data to the [`http.ResponseWriter`](https://pkg.go.dev/net/http#ResponseWriter) or read from the [Request.Body] after or concurrently with the completion of the ServeHTTP call.

```go
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}
```

Types that implement the `ServeHTTP(w http.ResponseWriter, r *http.Request)` method satisfy the [`http.Handler`](https://pkg.go.dev/net/http/#Handler) interface and therefore instances of those types can, for example, be used as the second argument to the [`http.Handle`](https://pkg.go.dev/net/http/#Handle) function or the equivalent [`ServeMux.Handle`](https://pkg.go.dev/net/ServeMux.Handle) method.

An example might make this more clear:

```go
type myHandler struct {
    // ...
}

func (h myHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte(`hello world`))
}

func main() {
	mux. := http.NewServeMux()
	mux.Handle("/", myHandler{})
	http.ListenAndServe(":8080", mux)
}
```

#### func FileServer - a Handler type
```go
func FileServer(root FileSystem) Handler
```
FileServer returns a handler that serves HTTP requests with the contents of the file system rooted at root.

As a special case, the returned file server redirects any request ending in "/index.html" to the same path, without the final "index.html".

To use the operating system's file system implementation, use [`http.Dir`](https://pkg.go.dev/net/http#Dir):

```go
http.Handle("/", http.FileServer(http.Dir("/tmp")))
```

Example of FileServer Handler type:

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	// Simple static webserver:
	log.Fatal(http.ListenAndServe(":8080", http.FileServer(http.Dir("/usr/share/doc"))))
}
```


# 3. Implementation

## 3.1 Handle

```go
func(pattern string, handler Handler)
```

An example of `http.Handle` implementation in six different ways:

```go
package main

import (
	"io"
	"log"
	"net/http"
)

type myHandler1 struct{}
type myHandler2 struct{}
type myHandler3 struct{}
type myHandler4 struct{}

func (h myHandler1) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 1")
}

func (h *myHandler2) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 2")
}

func (h myHandler3) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 3")
}

func (h myHandler4) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 4")
}

func myHandler5() http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		io.WriteString(w, "Hello from myHandler 5")
	})
}

func myHandler6(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 6")
}

func main() {
	var h4 myHandler4
	mux := http.NewServeMux()
	mux.Handle("/h1", myHandler1{})
	mux.Handle("/h2", &myHandler2{})
	mux.Handle("/h3", new(myHandler3))
	mux.Handle("/h4", h4)
	mux.Handle("/h5", myHandler5())
	mux.Handle("/h6", http.HandlerFunc(myHandler6))
	log.Fatal(http.ListenAndServe(":8080", mux))
}
```

## 3.2 HandleFunc


```go
func HandleFunc(pattern string, handler func(ResponseWriter, *Request))
```

Example:

```go
package main

import (
	"io"
	"log"
	"net/http"
)

func h2(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from HandleFunc #2!\n")
}

func h3(http.ResponseWriter, *http.Request) {
	log.Println("Logging HandleFunc #3!")
}

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/h1", func(w http.ResponseWriter, r *http.Request) {
		io.WriteString(w, "Hello from HandleFunc #2!\n")
	})
	mux.HandleFunc("/h2", h2)
	mux.HandleFunc("/h3", h3)
	http.ListenAndServe(":8080", mux)
}
```

## 3.3 HandlerFunc

```go
type HandlerFunc func(ResponseWriter, *Request)
```

Example:

```go
func myHandlerFunc(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte(`hello world`))
}

func main() {
	http.Handle("/", http.HandlerFunc(myHandlerFunc))
	http.ListenAndServe(":8080", nil)
}
```


#### func (HandlerFunc) ServeHTTP - a HandlerFunc type
ServeHTTP calls f(w,r)

```go
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request)
```

Example:

```go
package main

import (
	"fmt"
	"net/http"
)

// MyHandler is a simple HTTP handler function.
func MyHandler(w http.ResponseWriter, r *http.Request) {
	fmt.Fprint(w, "Hello, this is my handler!")
}

func main() {
	// Convert MyHandler function to a HandlerFunc
	handlerFunc := http.HandlerFunc(MyHandler)

	// Use handlerFunc as an HTTP handler
	http.Handle("/myhandler", handlerFunc)

	// Start the web server
	http.ListenAndServe(":8000", nil)
}
```


# 4. Uses

In the context of Go's `net/http` package, `Handle`, `HandleFunc`, and `HandlerFunc` are related to handling HTTP requests, but they serve different purposes and have distinct roles. Below, I explain the differences:

### 1. `Handle`
- **Type**: Method of the `http.ServeMux` type.
- **Purpose**: Registers a handler for a specific URL pattern.
- **Signature**:
  ```go
  func (mux *ServeMux) Handle(pattern string, handler Handler)
  ```
- **Explanation**:
  - `Handle` is used to associate a URL pattern (e.g., `/time`) with an `http.Handler` interface, which must implement the `ServeHTTP` method:
    ```go
    type Handler interface {
        ServeHTTP(http.ResponseWriter, *http.Request)
    }
    ```
  - You pass a struct or type that implements the `http.Handler` interface (like the `timeHandler` struct in your code).
  - Example from your code:
    ```go
    mux.Handle("/time", timeHandler{format: time.RFC822})
    ```
    Here, `timeHandler` implements `http.Handler` because it has a `ServeHTTP` method, and `Handle` registers it to handle requests to `/time`.
- **Use Case**: Use `Handle` when you have a custom type (e.g., a struct) that implements the `http.Handler` interface.

### 2. `HandleFunc`
- **Type**: Method of the `http.ServeMux` type.
- **Purpose**: Registers a function (not an interface) as an HTTP handler for a specific URL pattern.
- **Signature**:
  ```go
  func (mux *ServeMux) HandleFunc(pattern string, handler func(http.ResponseWriter, *http.Request))
  ```
- **Explanation**:
  - `HandleFunc` is used to register a plain function with the signature `func(http.ResponseWriter, *http.Request)` to handle HTTP requests for a given pattern.
  - Unlike `Handle`, it does not require the handler to implement the `http.Handler` interface. Instead, it takes a function directly.
  - Internally, `HandleFunc` converts the function into an `http.Handler` by wrapping it with `http.HandlerFunc` (see below).
  - Example:
    ```go
    mux.HandleFunc("/hello", func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("Hello, World!"))
    })
    ```
    This registers a function to respond with "Hello, World!" for requests to `/hello`.
- **Use Case**: Use `HandleFunc` when you want to quickly define a handler as a function without creating a custom struct or type.

### 3. `HandlerFunc`
- **Type**: A type defined in the `net/http` package.
- **Purpose**: A type that wraps a function to make it compatible with the `http.Handler` interface.
- **Signature**:
  ```go
  type HandlerFunc func(http.ResponseWriter, *http.Request)
  ```
- **Explanation**:
  - `http.HandlerFunc` is a type that allows a function with the signature `func(http.ResponseWriter, *http.Request)` to act as an `http.Handler`.
  - It implements the `http.Handler` interface by defining a `ServeHTTP` method that calls the underlying function.
  - Essentially, `HandlerFunc` is a convenience type that bridges plain functions and the `http.Handler` interface.
  - Example:
    ```go
    var myHandler http.HandlerFunc = func(w http.ResponseWriter, r *http.Request) {
        w.Write([]byte("Hello via HandlerFunc!"))
    }
    mux.Handle("/hello", myHandler)
    ```
    Here, `myHandler` is a function of type `http.HandlerFunc`, which can be passed to `mux.Handle` because it satisfies the `http.Handler` interface.
- **Use Case**: Use `HandlerFunc` when you want to explicitly treat a function as an `http.Handler` or pass it to `Handle`.

### Key Differences
| **Feature**       | **Handle**                              | **HandleFunc**                          | **HandlerFunc**                         |
|-------------------|-----------------------------------------|-----------------------------------------|-----------------------------------------|
| **Type**          | Method of `http.ServeMux`              | Method of `http.ServeMux`              | Type in `net/http`                      |
| **Input**         | URL pattern and `http.Handler`         | URL pattern and function               | Function with specific signature        |
| **Purpose**       | Registers an `http.Handler` interface  | Registers a function as a handler       | Wraps a function to act as `http.Handler` |
| **Requires**      | Type implementing `ServeHTTP`          | Plain function with correct signature  | Function with correct signature         |
| **Example**       | `mux.Handle("/time", timeHandler{})`   | `mux.HandleFunc("/hello", func(w, r) {})` | `http.HandlerFunc(func(w, r) {})`       |

### Relationship Between Them
- `HandleFunc` internally uses `HandlerFunc` to convert a function into an `http.Handler` so it can be registered with the multiplexer.
  - When you call `mux.HandleFunc(pattern, fn)`, it essentially does:
    ```go
    mux.Handle(pattern, http.HandlerFunc(fn))
    ```
- `Handle` is used for types that already implement the `http.Handler` interface, while `HandleFunc` is a convenience for registering functions directly.
- `HandlerFunc` is the type that makes `HandleFunc` possible by allowing functions to be treated as `http.Handler`.

### In Your Code
Your code uses `Handle`:
```go
mux.Handle("/time", timeHandler{format: time.RFC822})
```
This works because `timeHandler` implements `http.Handler` via its `ServeHTTP` method. If you wanted to use `HandleFunc` instead, you could rewrite it as:
```go
mux.HandleFunc("/time", func(w http.ResponseWriter, r *http.Request) {
    tm := time.Now().Format(time.RFC822)
    w.Write([]byte("The time is: " + tm))
})
```
This achieves the same result without needing a custom struct.

### Summary
- Use **`Handle`** for registering types that implement `http.Handler`.
- Use **`HandleFunc`** for registering plain functions as handlers.
- Use **`HandlerFunc`** as a type to explicitly treat a function as an `http.Handler` or when passing functions to `Handle`.


# 5. Tips

* `http.Handle` takes a value of any type that implements the `http.Handler` interface, `http.HandlerFunc` is one of those types that implements that interface, but it's not the only one.
* `http.HandleFunc` takes in a function of type `http.HandlerFunc`. Note that `http.HandleFunc` is not the same as `http.Handle`.
* also note that for the argument to `http.HandleFunc` the method `ServeHTTP(...` is not important, what's important is that the signature, ie type, of the function that you pass in is the same as defined by the argument, ie. `func(http.ResponseWriter, *http.Request)`. The ServeHTTP method is only important for the `http.Handle` or anything that depends on the `http.Handler` interface which declares that method as one of its members.

# 6. The default servemux

The default servemux is just a plain ol' servemux like we've already been using, which gets instantiated by default when the net/http package is used and is stored in a global variable. Here's the relevant line from the Go source:

```
var DefaultServeMux = NewServeMux()
```

Generally speaking, I recommended against using the default servemux because it makes your code less clear and explicit and it poses a security risk. Because it's stored in a global variable, any package is able to access it and register a route — including any third-party packages that your application imports. If one of those third-party packages is compromised, they could use the default servemux to expose a malicious handler to the web.

Instead it's better to use your own locally-scoped servemux, like we have been so far. But if you do decide to use the default servemux...

The `net/http` package provides a couple of shortcuts for registering routes with the default servemux: [http.Handle()](https://pkg.go.dev/net/http/#Handle)and [http.HandleFunc()](https://pkg.go.dev/net/http/#HandleFunc). These do exactly the same as their namesake functions we've already looked at, with the difference that they add handlers to the default servemux instead of one that you've created.

Additionally, [http.ListenAndServe()](https://pkg.go.dev/net/http#ListenAndServe) will fall back to using the default servemux if no other handler is provided (that is, the second argument is set to nil).

So as a final step, let's demonstrate how to use the default servemux in our application instead:

```go
package main

import (
	"log"
	"net/http"
	"time"
)

func timeHandler(format string) http.Handler {
	fn := func(w http.ResponseWriter, r *http.Request) {
		tm := time.Now().Format(format)
		w.Write([]byte("The time is: " + tm))
	}
	return http.HandlerFunc(fn)
}

func main() {
	// Note that we skip creating the ServeMux...

	var format string = time.RFC1123
	th := timeHandler(format)

	// We use http.Handle instead of mux.Handle...
	http.Handle("/time", th)

	log.Print("Listening...")
	// And pass nil as the handler to ListenAndServe.
	http.ListenAndServe(":3000", nil)
}
```


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

# 1. Basics

Processing HTTP requests with Go is primarily about two things: `handlers` and `servemuxes`.

- Handlers are responsible for carrying out your application logic and writing response headers and bodies.
- A Servemux (also known as a router) stores a mapping between the predefined URL paths for your application and the corresponding handlers. Usually you have one servemux for your application containing all your routes.

Go's [net/http](https://pkg.go.dev/net/http) package ships with the simple but effective [ServeMux](https://pkg.go.dev/net/ServeMux) servemux, plus a few functions to generate common handlers including [http.FileServer()](https://pkg.go.dev/net/http/#FileServer), [http.NotFoundHandler()](https://pkg.go.dev/net/http/#NotFoundHandler) and [http.RedirectHandler()](https://pkg.go.dev/net/http/#RedirectHandler).

Let's take a look at the simplest static web server:

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

## Standard Handlers in the `http` package

In Go, the `net/http` package provides `http.Handle` and `http.HandleFunc` as the primary ways to register handlers for HTTP requests, and they use the `http.DefaultServeMux`

### http.Handle

- Registers a handler that implements the `http.Handler` interface (i.e., has a `ServerHTTP` method) for a specific URL pattern. You can check those patterns [here](https://pkg.go.dev/net/http#hdr-Patterns). Basically the first parameter declares the pattern to match, and the second paramenter instructs what the handler actually does when the match occurs.
- Used when you have a custom type that implements `http.Handler` interface.

```go
http.Handle("/example", &MyHandler{})
```

### http.HandleFunc

- Registers a function with the signature `func(http.ResponseWriter, *http.Request)` as a handler for a specific URL pattern.
- Convenient for simple handlers without needing a custom type.

```go
http.HandleFunc("/example", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, World!")
})
```

### Examples

The code below shows how to create a basic web server using Effective Go writing style: clear and idiomatic. The code shows how to implement the same solution using three different methods: `http.Handle`, `http.HandleFunc` and `http.HandlerFunc`.


{{< tabs tabTotal="3">}}
{{% tab tabName="Handle" %}}

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	http.Handle("/", http.FileServer(http.Dir("./html")))

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}
```

However it is better to have a dedicated function:

```go
func rootFileServer() http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        http.FileServer(http.Dir("./html")).ServeHTTP(w, r)
    }
}

func main() {
    http.Handle("/", rootFileServer())
    // ...
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a full http.Handler (not just HandlerFunc) ===
func handlerFactory(prefix, rootDir string) http.Handler {
    return http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir)))
}

func main() {
  http.Handle("/", handlerFactory("/", "html"))
  http.Handle("/static/", handlerFactory("/static/", "static"))
  // ...
```


{{% /tab %}}
{{% tab tabName="HandleFunc" %}}

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        http.FileServer(http.Dir("./static")).ServeHTTP(w, r)
	})

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}
```

However it is better to have a dedicated function:

```go
func rootFileServer() http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        http.FileServer(http.Dir("./static")).ServeHTTP(w, r)
    }
}

func main() {
    http.HandleFunc("/", rootFileServer())
    // ...
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a HandlerFunc that serves files ===
func handlerFuncFactory(prefix, rootDir string) http.HandlerFunc {
	fs := http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir)))
	return func(w http.ResponseWriter, r *http.Request) {
		fs.ServeHTTP(w, r)
	}
}

func main() {
  http.HandleFunc("/", handlerFuncFactory("/", "html"))
  http.HandleFunc("/static/", handlerFuncFactory("/static/", "static"))
//...
```

{{% /tab %}}
{{% tab tabName="HandlerFunc" %}}

`HandlerFunc` types are used to adapt a function into a `Handler`, so `http.Handle` can use it.

```go
package main

import (
    "log"
    "net/http"
)

func staticHandler(w http.ResponseWriter, r *http.Request) {
    http.FileServer(http.Dir("./static")).ServeHTTP(w, r)
}

func main() {
    http.Handle("/", staticHandler)  // Compile error: staticHandler is a function, not Handler

    // Solution: use http.HandlerFunc to adapt the function into a Handler
    http.Handle("/", http.HandlerFunc(staticHandler))

    log.Print("Listening on :8080...")
    if err := http.ListenAndServe(":8080", nil); err != nil {
        log.Fatal(err)
    }
}
```

{{% /tab %}}
{{< /tabs >}}


### http.Handle vs http.HandleFunc

- `http.Handle(pattern string, handler http.Handler)`:
 
    - Requires an `http.Handler` (something that implements the `http.Handler` interface, i.e., has a `ServeHTTP` method).
    - You must explicitly convert a function to `http.HandlerFunc` if you’re using a function as the handler.
    - Used for registering custom handlers (e.g., structs that implement `http.Handler`) or explicitly converted function handlers.
    - Useful when you need power: middleware, state, reusability, or advanced HTTP features.
 
<br> 

- `http.HandleFunc(pattern string, handler func(http.ResponseWriter, *http.Request))`:
 
    - Accepts a function with the signature `func(http.ResponseWriter, *http.Request)` directly.
    - Internally wraps the function in `http.HandlerFunc` to make it an `http.Handler`.
    - More convenient for registering simple function-based handlers, including anonymous functions.
    - Useful for simplicity: quick endpoints with no dependencies

## Custom ServeMux 

The `net/http` package provides a couple of shortcuts for registering routes with `DefaultServeMux`: [http.Handle()](https://pkg.go.dev/net/http/#Handle)and [http.HandleFunc()](https://pkg.go.dev/net/http/#HandleFunc).

Generally speaking, I recommended against using the default servemux because it makes your code less clear and explicit and it poses a security risk. Because it’s stored in a global variable, any package is able to access it and register a route, including any third-party packages that your application imports. If one of those third-party packages is compromised, they could use the default servemux to expose a malicious handler to the web.

We can create our own serve muxes to prevent the possible issues explained above. Let's refactor the previous code to make use of our own ServeMux:










{{< tabs tabTotal="3">}}
{{% tab tabName="Handle" %}}

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	mux := http.NewServeMux()
	mux.Handle("/", http.FileServer(http.Dir("./html")))

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", mux); err != nil {
		log.Fatal(err)
	}
}
```

However it is better to have a dedicated function:

```go
func rootFileServer() http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        http.FileServer(http.Dir("./html")).ServeHTTP(w, r)
    }
}

func main() {
	mux := http.NewServeMux()
    mux.Handle("/", rootFileServer())
    // ...
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a full http.Handler (not just HandlerFunc) ===
func handlerFactory(prefix, rootDir string) http.Handler {
    return http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir)))
}

func main() {
  mux := http.NewServeMux()
  mux.Handle("/", handlerFactory("/", "html"))
  mux.Handle("/static/", handlerFactory("/static/", "static"))
  // ...
```


{{% /tab %}}
{{% tab tabName="HandleFunc" %}}

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        http.FileServer(http.Dir("./static")).ServeHTTP(w, r)
	})

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", mux); err != nil {
		log.Fatal(err)
	}
}
```

However it is better to have a dedicated function:

```go
func rootFileServer() http.HandlerFunc {
    return func(w http.ResponseWriter, r *http.Request) {
        http.FileServer(http.Dir("./static")).ServeHTTP(w, r)
    }
}

func main() {
	mux := http.NewServeMux()
    mux.HandleFunc("/", rootFileServer())
    // ...
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a HandlerFunc that serves files ===
func handlerFuncFactory(prefix, rootDir string) http.HandlerFunc {
	fs := http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir)))
	return func(w http.ResponseWriter, r *http.Request) {
		fs.ServeHTTP(w, r)
	}
}

func main() {
	mux := http.NewServeMux()
  mux.HandleFunc("/", handlerFuncFactory("/", "html"))
  mux.HandleFunc("/static/", handlerFuncFactory("/static/", "static"))
//...
```

{{% /tab %}}
{{% tab tabName="HandlerFunc" %}}

`HandlerFunc` types are used to adapt a function into a `Handler`, so `http.Handle` can use it.

```go
package main

import (
    "log"
    "net/http"
)

func staticHandler(w http.ResponseWriter, r *http.Request) {
    http.FileServer(http.Dir("./static")).ServeHTTP(w, r)
}

func main() {
	mux := http.NewServeMux()
    mux.Handle("/", staticHandler)  // Compile error: staticHandler is a function, not Handler

    // Solution: use http.HandlerFunc to adapt the function into a Handler
    mux.Handle("/", http.HandlerFunc(staticHandler))

    log.Print("Listening on :8080...")
    if err := http.ListenAndServe(":8080", mux); err != nil {
        log.Fatal(err)
    }
}
```

{{% /tab %}}
{{< /tabs >}}



## Passing variables to handlers

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
	w.Write([]byte("hello world"))
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


# 3. Implementations

##  Handle

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
	io.WriteString(w, "Hello from myHandler 1\n")
}

func (h *myHandler2) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 2\n")
}

func (h myHandler3) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 3\n")
}

func (h myHandler4) ServeHTTP(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 4\n")
}

func myHandler5() http.Handler {
	return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
		io.WriteString(w, "Hello from myHandler 5\n")
	})
}

func myHandler6(w http.ResponseWriter, r *http.Request) {
	io.WriteString(w, "Hello from myHandler 6\n")
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

## HandleFunc


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
		io.WriteString(w, "Hello from HandleFunc #1!\n")
	})
	mux.HandleFunc("/h2", h2)
	mux.HandleFunc("/h3", h3)
	http.ListenAndServe(":8080", mux)
}
```

## HandlerFunc

```go
type HandlerFunc func(ResponseWriter, *Request)
```

Example:

```go
func myHandlerFunc(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("hello world"))
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
	http.ListenAndServe(":8080", nil)
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


### Summary
- Use **`Handle`** for registering types that implement `http.Handler`.
- Use **`HandleFunc`** for registering plain functions as handlers.
- Use **`HandlerFunc`** as a type to explicitly treat a function as an `http.Handler` or when passing functions to `Handle`.


# 5. Tips

* `http.Handle` takes a value of any type that implements the `http.Handler` interface, `http.HandlerFunc` is one of those types that implements that interface, but it's not the only one.
* `http.HandleFunc` takes in a function of type `http.HandlerFunc`. Note that `http.HandleFunc` is not the same as `http.Handle`.
* also note that for the argument to `http.HandleFunc` the method `ServeHTTP(...` is not important, what's important is that the signature, ie type, of the function that you pass in is the same as defined by the argument, ie. `func(http.ResponseWriter, *http.Request)`. The ServeHTTP method is only important for the `http.Handle` or anything that depends on the `http.Handler` interface which declares that method as one of its members.

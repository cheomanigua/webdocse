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
- `ListenAndServe` starts an HTTP server with a given address and handler. The handler is usually `nil`, which means to use `DefaultServeMux`. `Handle` and `HandleFunc` add handlers to `DefaultServeMux`.
- It is recommended to create your own ServeMux, like this: `mux := http.NewServeMux()`.
- **Handle**: `func Handle(pattern string, handler Handler)`
- **HandleFunc**: `func HandleFunc(pattern string, handler func(ResponseWriter, *Request))`

Go's [net/http](https://pkg.go.dev/net/http) package ships with the simple but effective [ServeMux](https://pkg.go.dev/net/ServeMux) servemux, plus a few helper functions to generate common handlers including [http.FileServer()](https://pkg.go.dev/net/http/#FileServer), [http.NotFoundHandler()](https://pkg.go.dev/net/http/#NotFoundHandler) and [http.RedirectHandler()](https://pkg.go.dev/net/http/#RedirectHandler).

Let's take a look at the simplest static web server:

```go
package main

import (
	"net/http"
)

func main() {
	http.ListenAndServe(":8080", http.FileServer(http.Dir("./html")))
}
```

`http.ListenAndServe` creates the server.
`:8080` tells the server to listen for requests on port 8080.
`http.FileServer` servers the files to the client.
`http.Dir("./html")` instructs `http.FileServer` to look at the host's `static`directory for the files to serve.

By default, `net/http` will use `index.html` as the first file to look at. This is the structure of the project:

```
server/
    |_ go.mod
    |_ server.go
    |_ html/
        |_ index.html
        |_ about.html
        |_ contact.html
```

## Standard Handlers in the `http` package

In Go, the `net/http` package provides `http.Handle` and `http.HandleFunc` as the primary ways to register handlers for HTTP requests, and they use the `http.DefaultServeMux`

### http.Handle [](https://pkg.go.dev/net/http#Handle)

- `func Handle(pattern string, handler Handler)`
- Registers a handler ([Handler](https://pkg.go.dev/net/http#Handler) interface) for a specific URL pattern in `DefaultServeMux`. You can check those patterns [here](https://pkg.go.dev/net/http#hdr-Patterns). Basically the first parameter declares the pattern to match, and the second paramenter instructs what the handler actually does when the match occurs.
- Used when you have a custom type that implements `http.Handler` interface.

```go
http.Handle("/example", &MyHandler{})
```

### http.HandleFunc [](https://pkg.go.dev/net/http#HandleFunc)

- `func HandleFunc(pattern string, handler func(ResponseWriter, *Request))`
- Registers a handler function ([HandlerFunc](https://pkg.go.dev/net/http#HandlerFunc) func) with the signature `func(ResponseWriter, *Request)` for a specific URL pattern in `DefaultServeMux`.
- Convenient for simple handlers without needing a custom type.

```go
http.HandleFunc("/example", func(w http.ResponseWriter, r *http.Request) {
    fmt.Fprintf(w, "Hello, World!")
})
```

### http.Handler [](https://pkg.go.dev/net/http#Handler)

- A [handler](https://pkg.go.dev/net/http#Handler) responds to an HTTP request.
- There are 8 convenient helper functions to generate common handlers: AllowQuerySemicolons, FileServer, FileServerFS, MaxBytesHandler, NotFoundHandler, RedirectHandler, StripPrefix, TimeoutHandler.
- You can create your own handlers as long as they satisfy the `http.Handler` interface.

### http.HandlerFunc [](https://pkg.go.dev/net/http#HandlerFunc)

- HandlerFunc implements `ServeHTTP` under the hood.
- The [HandlerFunc](https://pkg.go.dev/net/http#HandlerFunc) type is an adapter to allow `http.Handle` the use of ordinary functions as HTTP handlers. Without `HandlerFunc`, you cannot pass a plain function to `http.Handle`.

    ```go
    func myFunc(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hello!\n")
    }

    http.Handle("/", http.HandlerFunc(myFunc))
    ```


### Basic Examples

{{< tabs tabTotal="3">}}
{{% tab tabName="Version 1" %}}

```go
package main

import (
	"fmt"
	"net/http"
)

func main() {
	http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
		fmt.Fprintf(w, "Welcome to my website!\n")
	})

	http.ListenAndServe(":8080", nil)
}
```

{{% /tab %}}
{{% tab tabName="Version 2" %}}

```go
package main

import (
	"fmt"
	"net/http"
)

func myHandlerFunc(w http.ResponseWriter, r *http.Request) {
	fmt.Fprintf(w, "Welcome to my website!\n")
}

func main() {
	http.Handle("/", http.HandlerFunc(myHandlerFunc))  // Option 1
	http.HandleFunc("/", myHandlerFunc)                // Option 2


	http.ListenAndServe(":8080", nil)
}
```

{{% /tab %}}
{{% tab tabName="Version 3" %}}

```go
package main

import (
	"fmt"
	"net/http"
)

type myHandler struct {
	name string
}

func (h *myHandler) ServeHTTP(w http.ResponseWriter, r *http.Request) {   // Declares a handler (implements ServeHTTP)
	h.name = "Alice"
	fmt.Fprintf(w, "Welcome to my website, %s!\n", h.name)
}

func main() {
	http.Handle("/", &myHandler{}) // Register the handler using http.Handle (not HandleFunc)

	fmt.Println("Server running at http://localhost:8080")
	http.ListenAndServe(":8080", nil)
}
```

{{% /tab %}}
{{< /tabs >}}

### Best to use

| Feature / Use Case                   | `http.Handle` with `http.Handler` | `http.HandleFunc` | `http.HandlerFunc`                                                                                  |
| ------------------------------------ | --------------------------------- | ----------------- | --------------------------------------------------------------------------------------------------- |
| **Handler type**                     | Struct implementing `ServeHTTP`   | Function          | Function adapter that implements `ServeHTTP`                                                        |
| **Stateful handlers**                | ✅ Yes, struct can store state     | ❌ Stateless       | ❌ Stateless (but can wrap a struct method)                                                          |
| **Complex logic / multiple methods** | ✅ Yes                             | ❌ Harder          | ⚪ Can wrap complex logic, but usually function-level                                                |
| **Simple endpoints**                 | ⚪ Works, maybe overkill           | ✅ Perfect         | ✅ Works, can be used wherever `http.Handler` is needed                                              |
| **Reusability**                      | ✅ Created once at startup         | ✅ Function reused | ✅ Can wrap a function once and reuse it                                                             |
| **Use case**                         | Stateful, production-grade        | Simple, stateless | When you need a function to behave as a `Handler` (e.g., passing method reference to `http.Handle`) |



### Examples

The code below shows how to create a basic web server using Effective Go writing style: clear and idiomatic. The code shows how to implement the same solution using three different methods: `http.Handle`, `http.HandleFunc` and `http.HandlerFunc`.


{{< tabs tabTotal="5">}}
{{% tab tabName="Handle" %}}

```go
package main

import (
	"log"
	"net/http"
)

func main() {
    http.Handle("/", http.FileServer(http.Dir("./html"))) // Create the handler ONCE, at startup

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}
```

However, it is better to have a dedicated function:

```go
func serveStaticFiles(w http.ResponseWriter, r *http.Request) {
    http.FileServer(http.Dir("./html")).ServeHTTP(w, r) // Create a handler on EVERY request (less efficient)
}

func main() {
    http.Handle("/", http.HandlerFunc(serveStaticFiles))
    // ...
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a full http.Handler (not just HandlerFunc) ===
func handlerFactory(prefix, rootDir string) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		fs := http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir))) // Create a handler on EVERY request (less efficient)
		fs.ServeHTTP(w, r)
	}
}

func main() {
	// You can use http.Handle because http.HandlerFunc implements http.Handler
	http.Handle("/", handlerFactory("/", "html"))
	http.Handle("/static/", handlerFactory("/static/", "static"))
  // ...
```

{{% /tab %}}
{{% tab tabName="Handle efficient" %}}


```go
package main

import (
	"log"
	"net/http"
)

func main() {
	rootFileServer := http.FileServer(http.Dir("./html")) // Create the handler ONCE, at startup
	http.Handle("/", rootFileServer)

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}
```


However, it is better to have a dedicated function:

```go
func serveStaticFiles(w http.ResponseWriter, r *http.Request) {
    http.FileServer(http.Dir("./html")).ServeHTTP(w, r) // Create a handler on EVERY request (less efficient)
}

func main() {
    http.Handle("/", http.HandlerFunc(serveStaticFiles))
    // ...
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a full http.Handler (not just HandlerFunc) ===
func handlerFactory(prefix, rootDir string) http.Handler {
    return http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir))) // Create the handler ONCE, at startup
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
        http.FileServer(http.Dir("./html")).ServeHTTP(w, r) // Create the handler on EVERY request (less efficient)
	})

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}
```

However it is better to have a dedicated function:

```go
// ...
func rootFileServer(w http.ResponseWriter, r *http.Request) {
	http.FileServer(http.Dir("./html")).ServeHTTP(w, r) // Create the handler on EVERY request (less efficient)
}

func main() {
	http.HandleFunc("/", rootFileServer)
    // ...
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a HandlerFunc that serves files ===
func handlerFuncFactory(prefix, rootDir string) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
	    fs := http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir))) // Create the handler on EVERY request (less efficient)
		fs.ServeHTTP(w, r)
	}
}

func main() {
  http.HandleFunc("/", handlerFuncFactory("/", "html"))
  http.HandleFunc("/static/", handlerFuncFactory("/static/", "static"))
//...
```

{{% /tab %}}
{{% tab tabName="HandleFunc efficient" %}}

```go
package main

import (
	"log"
	"net/http"
)

func main() {
    rootFileServer := http.FileServer(http.Dir("./html")) // Create the handler ONCE, at startup
	http.HandleFunc("/", rootFileServer.ServeHTTP)
	
	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", nil); err != nil {
		log.Fatal(err)
	}
}
```

However it is better to have a dedicated function:

```go
// ...
func rootFileServer(fs http.Handler) http.HandlerFunc {
	return func(w http.ResponseWriter, r *http.Request) {
		fs.ServeHTTP(w, r)
	}
}

func main() {
	fs := http.FileServer(http.Dir("./html")) // Create the handler ONCE, at startup
	http.HandleFunc("/", rootFileServer(fs))
}
```

We can do even better. Make a handler factory so we can target any path, not just `/html`:

```go
// === FACTORY: Returns a HandlerFunc that serves files ===
func handlerFuncFactory(prefix, rootDir string) http.HandlerFunc {
	fs := http.StripPrefix(prefix, http.FileServer(http.Dir(rootDir))) // Create the handler ONCE, at startup
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

func rootFileServer(w http.ResponseWriter, r *http.Request) {
    http.FileServer(http.Dir("./html")).ServeHTTP(w, r) // Create a handler on EVERY request (less efficient)
}

func main() {
    http.Handle("/", rootFileServer)  // Compile error: rootFileServer is a function, not Handler

    // Solution: use http.HandlerFunc to adapt the function into a Handler
    fs := http.HandlerFunc(rootFileServer)
    http.Handle("/", fs)

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
        http.FileServer(http.Dir("./html")).ServeHTTP(w, r)
	})

	log.Print("Listening on :8080...")
	if err := http.ListenAndServe(":8080", mux); err != nil {
		log.Fatal(err)
	}
}
```

However it is better to have a dedicated function:

```go
func rootFileServer(w http.ResponseWriter, r *http.Request) {
	mux.FileServer(http.Dir("./html")).ServeHTTP(w, r)
}

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/", rootFileServer)
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

# 2. Implementations

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

func myFunc6(w http.ResponseWriter, r *http.Request) {
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
	mux.Handle("/h6", http.HandlerFunc(myFunc6))
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
    w.Write([]byte("Hellow from HandleFunc #2"))
	log.Println("Logging HandleFunc #2!")
}

func main() {
	mux := http.NewServeMux()
	mux.HandleFunc("/h1", func(w http.ResponseWriter, r *http.Request) {
		io.WriteString(w, "Hello from HandleFunc #1!\n")
	})
	mux.HandleFunc("/h2", h2)
	http.ListenAndServe(":8080", mux)
}
```

## HandlerFunc

```go
type HandlerFunc func(ResponseWriter, *Request)
```

Example:

```go
func myFunc(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("hello world"))
}

func main() {
	http.Handle("/", http.HandlerFunc(myFunc))
	http.ListenAndServe(":8080", nil)
}
```

# 3. Uses


### Key Differences
| **Feature**       | **Handle**                              | **HandleFunc**                          | **HandlerFunc**                         |
|-------------------|-----------------------------------------|-----------------------------------------|-----------------------------------------|
| **Type**          | Method of `http.ServeMux`              | Method of `http.ServeMux`              | Type in `net/http`                      |
| **Input**         | URL pattern and `http.Handler`         | URL pattern and function               | Function with specific signature        |
| **Purpose**       | Registers an `http.Handler` interface  | Registers a function as a handler       | Wraps a function to act as `http.Handler` |
| **Requires**      | Type implementing `ServeHTTP`          | Plain function with correct signature  | Function with correct signature         |
| **Example**       | `http.Handle("/time", timeHandler{})`   | `http.HandleFunc("/hello", func(w, r) {})` | `http.HandlerFunc(func(w, r) {})`       |

### Relationship Between Them
- `HandleFunc` internally uses `HandlerFunc` to convert a function into an `http.Handler` so it can be registered with the multiplexer.
  - When you call `http.HandleFunc(pattern, fn)`, it essentially does:
    ```go
    http.Handle(pattern, http.HandlerFunc(fn))
    ```
- `Handle` is used for types that already implement the `http.Handler` interface, while `HandleFunc` is a convenience for registering functions directly.
- `HandlerFunc` is the type that makes `HandleFunc` possible by allowing functions to be treated as `http.Handler`.


### Summary
- Use **`Handle`** for registering types that implement `http.Handler`.
- Use **`HandleFunc`** for registering plain functions as handlers.
- Use **`HandlerFunc`** as a type to explicitly treat a function as an `http.Handler` or when passing functions to `Handle`.

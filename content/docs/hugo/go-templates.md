---
weight: 2400
title: "Go Templates"
description: "Generate safe, dynamic text output (HTML, JSON) from data structures"
icon: "article"
date: "2025-11-07T11:12:08+01:00"
lastmod: "2025-11-07T11:12:08+01:00"
draft: false
toc: true
---

Hugo uses the **Go Templates** package under the hood for its templates. Although is not an Hugo feature, I include Go Templates in this documentation since it is paramount to know about them in order to create Hugo templates.

Package template (html/template) implements data-driven templates for generating HTML output safe against code injection. It provides the same interface as text/template and should be used instead of text/template whenever the output is HTML.

The documentation for [html/template](https://pkg.go.dev/html/template) focuses on the security features of the package. For information about how to program the templates themselves, see the documentation for [text/template](https://pkg.go.dev/text/template).

## Use case

### Project tree

- root
    - main.go
    - home.html
    - greeting.html

### Code

##### `main.go`

```go
package main

import (
  "embed"
  "log"
  "net/http"
  "html/template"
)

type Profile struct {
    SiteName string
    UserName string
}

//go:embed *html
var templateFS embed.FS
var tmpl = template.Must(template.ParseFS(templateFS, "*.html"))

func main() {
  mux := http.NewServeMux()

  mux.HandleFunc("GET /", func(w http.ResponseWriter, r *http.Request) {
    // Strict path check + allow only GET. Prevents unexpected behavior on `/..//` or other tricks
    if r.URL.Path != "/" {
      http.NotFound(w, r)
      return
    }

    p := Profile{"My Amazing Site", "Alice"}

    if err := tmpl.ExecuteTemplate(w, "home.html", p); err != nil {
      log.Printf("Template error: %v", err)
      http.Error(w, err.Error(), http.StatusInternalServerError)
    }
  })

  server := &http.Server {
    Addr: ":3000",
    Handler: mux,
  }

  log.Println("Server running at http://localhost:3000")
  log.Fatal(server.ListenAndServe())
}
```

##### `home.html`

```html
<html>
<head>
  <title>{{ .SiteName }}</title>
</head>
<body>
  <h1>Welcome to {{ .SiteName }}</h1>
  {{ template "greeting" . }} <!-- If we don't add the dot, greeting.html cannot retrieve .UserName -->
</body>
</html>
```

##### `greeting.html`

```html
{{ define "greeting" }}
<p>Welcome {{ .UserName }}. We are very happy that you decided to join our membership subscription. We are looking forward to your input.</p>
{{ end }}
```

## ParseFiles vs //go:embed

### Option 1: Using `ParseFiles`

```go
var tmpl = template.Must(template.ParseFiles("*.html"))
```

### Option 2: Using `//go:embed`

```go
//go:embed *.html
var templateFS embed.FS
var tmpl = template.Must(template.ParseFS(templateFS, "*.html"))
```
---

### Option 1


#### ✅ Pros

* **Great for development:**
  You can edit `.html` files and just refresh the page — no rebuild required.

* **Simpler conceptually:**
  Uses the regular filesystem, no embedding directives.

#### ⚠️ Cons

* **Not self-contained:**
  You must ship the `.html` files alongside your binary, preserving the exact directory structure.

* **Can break in deployment:**
  If your program can’t find the template files (e.g., wrong working directory), it’ll crash at startup.

* **Less secure:**
  If someone modifies the files on disk, your templates change without a rebuild.

### Option 2

#### ✅ Pros

* **Self-contained binary:**
  All `.html` files are compiled *into* your Go binary. You can ship just one file (the binary) — no need to distribute templates separately.

* **No filesystem dependencies at runtime:**
  Works even if you run your app inside a Docker image, serverless function, or a read-only filesystem.

* **More secure & reliable:**
  Templates can’t be accidentally modified or deleted after compilation.

* **Production-friendly:**
  Ideal for deployment since everything is versioned and embedded.

#### ⚠️ Cons

* **You must rebuild the binary** every time you change an HTML file.
* Slightly larger binary size (the HTML is included in it).
* Not ideal for rapid development if you frequently tweak templates.

---

###  When to Use Each

| Use Case                 | Recommended Option                   |
| ------------------------ | ------------------------------------ |
| Local development        | `ParseFiles` (faster iteration)      |
| Production / deployment  | `//go:embed` (self-contained binary) |
| Docker / serverless      | `//go:embed`                         |
| Heavy template iteration | `ParseFiles`                         |

---

### A Common Hybrid Approach

Many Go developers do this:

```go
var tmpl *template.Template

func init() {
	if os.Getenv("DEV_MODE") == "1" {
		// Load from disk during development
		tmpl = template.Must(template.ParseFiles("*.html"))
	} else {
		// Use embedded templates in production
		tmpl = template.Must(template.ParseFS(templateFS, "*.html"))
	}
}
```

Then run with:

```bash
DEV_MODE=1 go run main.go
```

This gives you **both the fast dev cycle and the safety of embedding** for deployment.

---

✅ **In summary:**

* Use `ParseFiles` during development for convenience.
* Use `//go:embed` in production for reliability and simplicity in deployment.


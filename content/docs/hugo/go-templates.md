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


# Use cases

## layouts/baseof.html

```html
<!DOCTYPE html>
<html lang="{{ .Site.Language.Lang }}">
<head>
  <meta charset="UTF-8" />
	<link rel="stylesheet" href="https://unpkg.com/tachyons@4.12.0/css/tachyons.min.css">
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <link rel="stylesheet" href="{{ "css/main-menu.css" | relURL }}">
  <link rel="stylesheet" href="{{ "css/post-cards.css" | relURL }}">
  {{ partial "head.html" . }}
</head>
<body>
  <header>
    {{ partial "header.html" . }}
  </header>
  <main>
    {{ block "main" . }}
      {{ .Content }}
    {{ end }}
  </main>
  <footer>
    {{ partial "footer.html" . }}
  </footer>
</body>
</html>
```

- `{{ partial "header.html" }}`: Executes a reusable piece of markup from `header.html`.
- `{{ block "main" }}`: Make the content inside block a default that pages/section can optionally replace.
- `{{ .Content }}`: Renders the content, depending on the context, of pages in `content/`.

## layouts/home.html

```html
{{ define "main" }}
  {{ .Content }}
  {{ partial "pricing-cards.html" }}

  <div class="posts-container">
    {{ range (where .Site.RegularPages "Section" "posts").ByDate.Reverse }}
      <div class="post-card">
        <h2 class="f3 b mb1">
          <a href="{{ .Permalink }}">{{ .Title }}</a>
        </h2>
        <p class="f5 gray lh-copy">{{ .Summary }}</p>
      </div>
    {{ end }}
  </div>
{{ end }}
```

- `{{ .Content }}`: Renders the content of `content/_index.md`.
- `{{ partial "pricing-cards.html" }}`: Executes the code of `pricing-cards.html`.
- `{{ range (where .Site.RegularPages "Section" "posts").ByDate.Reverse }}`: Loop through all pages inside the `content/posts/` directory by oldest date.
- `{{ .Permalink }}` and `{{ .Title }}`: Show the title and the url of the post.\*
- `{{ .Summary }}`: Renders a excerpt of the post.\*

\* because of the context of the `range` loop, we are fetching posts.

## layouts/page.html

```html
{{ define "main" }}
  <h1>{{ .Title }}</h1>

  {{ $dateMachine := .Date | time.Format "2006-01-02T15:04:05-07:00" }}
  {{ $dateHuman := .Date | time.Format ":date_long" }}
  <time datetime="{{ $dateMachine }}">{{ $dateHuman }}</time>

  {{ .Content }}
  {{ partial "terms.html" (dict "taxonomy" "tags" "page" .) }}
{{ end }}
```

- `{{ .Content }}`: render the content associated with the current page (`.md` file)
- `{{ partial "terms.html" (dict "taxonomy" "tags" "page" .) }}`: render a list of terms (e.g., tags) associated with the current page.

## layouts/section.htlml

```html
{{ define "main" }}
  <h1>{{ .Title }}</h1>
  {{ range .Pages }}
    <section>
      <h2><a href="{{ .RelPermalink }}">{{ .LinkTitle }}</a></h2>
      {{ .Summary }}
    </section>
  {{ end }}
{{ end }}
```
- `{{ range .Pages }}`: loop through all the pages in `content/<section>/` directory.
- `{{ .Summary }}`: renders a excerpt of the page.

## layouts/taxonomy.html

```html
{{ define "main" }}
  <h1>{{ .Title }}</h1>
  {{ .Content }}
  {{ range .Pages }}
    <h2><a href="{{ .RelPermalink }}">{{ .LinkTitle }}</a></h2>
  {{ end }}
{{ end }}
```
- List all taxonomy terms.

## layouts/terms.html

```html
{{ define "main" }}
  <h1>{{ .Title }}</h1>
  {{ .Content }}
  {{ range .Pages }}
    <h2><a href="{{ .RelPermalink }}">{{ .LinkTitle }}</a></h2>
  {{ end }}
{{ end }}
```
- Lists all pages that belong to that term, with:
    - Title link
    - Optional date
    - Optional excerpt
    - Tag list using the `terms.html` partial you already have

---
weight: 300
title: "Hugo"
description: ""
icon: "article"
date: "2024-08-13T11:24:43+02:00"
lastmod: "2024-08-13T11:24:43+02:00"
draft: false
toc: true
---

# Installation

## Prerequisites

Although not required in all cases, [Git](https://git-scm.com/), [Go](https://go.dev/), and [Dart Sass](https://gohugo.io/hugo-pipes/transpile-sass-to-css/#dart-sass) are commonly used when working with Hugo. The following two methods to install '**Hugo**' also take care of the necessary dependecies.

## Methods

### 1. Prebuilt binaries (latest)

Visit the [latest release](https://github.com/gohugoio/hugo/releases/latest) and scroll down to the Assets section. If you are using Debian or its derivative distros, follow these steps:

1. Download the last extended .deb package: `hugo_extended_0.131.0_linux-amd64.deb`
2. Run:
```
$ sudo dpkg -i hugo_extended_0.131.0_linux-amd64.deb
$ hugo version
```

### 2. Repository packages (outdated)

If you are using Debian or its derivative distros, run:

```
$ sudo apt install hugo
$ hugo version
```

# Create a site

```
hugo new site my-site
cd my-site
git init
git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
echo "theme = 'ananke'" >> hugo.toml
hugo server
```

`hugo server` runs the site in developer mode.

Visit your Hugo site at [http://localhost:1313](http://localhost:1313)

# Themes

In order for a Hugo site to work, there must be at least one theme present in the project.

### Add a theme

There are four different methods to download and add themes to your project: Git clone, Git submodule, Zip file and Hugo module. 

##### 1. Git Clone
```
$ git clone https://github.com/[user-account]/[hugo-theme].git themes/[hugo-theme]
$ cp -a themes[hugo-theme]/exampleSite/. .
```
If you want to update the theme:

```
$ cd themes/[hugo-theme]
$ git pull
```

##### 2. Git Submodule

```
$ git init
$ git submodule add https://github.com/[user-account]/[hugo-theme].git themes/[hugo-theme]
```

When using CI/CD for Hugo website deployment, it’s essential to ensure that the following command is executed before running the `hugo` command.

```
$ git submodule update --init --recursive
```

If you want to update the theme:

```
$ git submodule update --remote --merge themes/[hugo-theme]
```

[Git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) is meant to separate your main project from another project within it. In above case the other project is the theme repository.

##### 3. Download an unzip

Download the theme as a **zip** file. Extract in the directory `themes/[hugo-theme]`.

##### 4. Hugo module (preferred)

{{< alert context="danger" text="You need [Go](https://go.dev/doc/install) version 1.18 or later and [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) to use Hugo Modules.." />}}

- Initialize your own hugo module system:
```
$ hugo mod init github.com/[your_user]/[your_project] 
```

- Add the Theme Module
```
$ hugo mod get github.com/[user-account]/[hugo-theme] 
```

- Configure the theme by adding it in your `hugo.toml` file:
```
theme = "github.com/[user-account]/[hugo-theme]"
```
If you want to update the theme:
```
$ hugo mode get -u github.com/[user-account]/[hugo-theme]
```

More information about Hugo Modules: [https://gohugo.io/hugo-modules/use-modules/](https://gohugo.io/hugo-modules/use-modules/)


### Set the theme in your config file

In the first three methods, be sure that your project's `config.toml` file has the theme added:
```
$ echo "theme = 'theme-name'" >> config.toml
```
or
```
$ echo "theme = 'theme-name'" >> hugo.toml
```


### Overriding a theme

In Hugo, layouts can live in either the project’s (root) or the themes’ layout folders. Any template inside the root layout folder will override the theme’s layout that is relative to it.

For example: `layouts/_default/baseof.html` will override `themes/[hugo-theme]/layouts/_default/baseof.html`. So, you can easily customize the theme without editing it directly, which makes updating the theme easier.

## Add content

```
$ hugo new about.md
```
Hugo created the file in the `content/` directory.

```
$ hugo new posts/my-first-post.md
```
Hugo created the file in the `content/posts/` directory.

If you open the file with your editor, you'll notice that the file has a [front matter](https://gohugo.io/content-management/front-matter/) already populated. This template is fetched from your project's `archetypes` directory. You can edit the archetype files to customize the front matter.

The archetype files in the root directory will override the archetype files in `themes/[hugo-theme]/

If you run `hugo server`, your new post does not appear. Why? See the next section to find out.

### Front matter
If `draft: true` is present in the front matter for a particular file, Hugo won't publish that file when building the site or running the developer server. You can override that behavior during development by running:
```
$ hugo server -D
```

Or you just can delete `draft: true` altogether or set it to `false` and run `hugo server` by itself.


## Running the site in developer mode

During development, you can visualize instantly the changes you make while editing your files every time you save your files. For that you have to run your site in developer mode:

```
$ hugo server
```

## Publish the site

Publishing a site does not deploy it. Hugo creates the entire static site in the `public` directory in the root of your project. This includes the HTML files, and assets such as images, CSS files, and JavaScript files.

```
$ hugo
```

## Deploying the site

You can upload your site directly from your local machine to a cloud provider by running:

```
$ hugo deploy
```


# Menus [](https://gohugo.io/content-management/menus/)

Standard file structure:

```

- root
	- content/
		- _index.md
		- about/
			- _index.md
		- posts/
			- _index.md
			- post01.md
			- post02.md
	- layouts/
		- index.html
		- shortcodes/
			- pricing-cards.html
```

To create a menu for your site, you must follow this workflow:

1. Define the menu entries [](https://gohugo.io/content-management/menus/)
2. Localize each entry (optional) [](https://gohugo.io/content-management/multilingual/#menus)
3. Render the menu with a template [](https://gohugo.io/templates/menu/)

## Define menu entries

There are three ways to define menu entries:

1. Define Automatically
	
	To automatically define a menu entry for each top-level [section](https://gohugo.io/quick-reference/glossary/#section) of your site, enable the section pages menu in your site configuration by adding this line in your `hugo.` config file:

	{{< tabs tabTotal="3">}}
	{{% tab tabName="yaml" %}}

	sectionPagesMenu: main

	{{% /tab %}}
	{{% tab tabName="toml" %}}

	sectionPagesMenu = 'main'

	{{% /tab %}}
	{{% tab tabName="json" %}}

	{
	   "sectionPagesMenu": "main"
	}

	{{% /tab %}}
	{{< /tabs >}}

	This creates a menu structure that you can access with **site.Menus.main** in your templates. See [menu templates](https://gohugo.io/templates/menu/) for details.

	Based in the file structure shown above, `sectionPagesMenu: main` will automatically create two entries in the main menu: About and Posts. Both are directly under Content and have an _`index.md` file.

	<br>

2. Define in front matter

	To add a page to the "main" menu, add this snipped in, for example, `content/about/_index.md`:


	{{< tabs tabTotal="3">}}
	{{% tab tabName="yaml" %}}

	---
	menus: main
	title: About
	---

	{{% /tab %}}
	{{% tab tabName="toml" %}}

	+++
	menus = 'main'
	title = 'About'
	+++

	{{% /tab %}}
	{{% tab tabName="json" %}}

	{
		"menus": "main",
		"title": "About"
	}

	{{% /tab %}}
	{{< /tabs >}}

	Access the entry with **site.Menus.main** in your templates. See [menu templates](https://gohugo.io/templates/menu/) for details.

	To add a page to the "main" and "footer" menus in `content/contact.md`:

	{{< tabs tabTotal="3">}}
	{{% tab tabName="yaml" %}}

	---
	menus:
	- main
	- footer
	title: Contact
	---

	{{% /tab %}}
	{{% tab tabName="toml" %}}

	+++
	menus = ['main', 'footer']
	title = 'Contact'
	+++

	{{% /tab %}}
	{{% tab tabName="json" %}}

	{
		"menus": [
			"main",
			"footer"
		],
		"title": "Contact"
	}

	{{% /tab %}}
	{{< /tabs >}}

	Access the entry with **site.Menus.main** and **site.Menus.footer** in your templates. See [menu templates](https://gohugo.io/templates/menu/) for details.

3. Define in site configuration

	See [configure menus](https://gohugo.io/configuration/menus/)


## Render menu with a template [](https://gohugo.io/templates/menu/)

After [defining menu entries](#31-define-menu-entries), use [menu methods](https://gohugo.io/methods/menu/) to render a menu.

Three factors determine how to render a menu:

1. The method used to define the menu entries: automatic, in front matter, or in site configuration
2. The menu structure: flat or nested
3. The method used to localize the menu entries: site configuration or translation tables

Check the [official documentation](https://gohugo.io/templates/menu/) for detailed instructions.


# Partials

We can add reausable HTML chunks into a `.md` file by using Partials.

1. Create the following file: `layouts/partials/pricing-cards.html`

	```html
	<link rel="stylesheet" href="https://unpkg.com/tachyons@4.12.0/css/tachyons.min.css">
	<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap" rel="stylesheet">

	</div>
	<ul class="list pa0 ma0 mb4">
	<li class="mb2 fw4">✓ 1 User Account</li>
	<li class="mb2 fw4">✓ 10GB Storage</li>
	<li class="mb2 fw4">✓ Basic Support</li>
	<li class="mb2 fw4 gray">✗ Advanced Analytics</li>
	</ul>
	```

2. Add the partial into `content/_index.md`

	```markdown
	+++
	date = '2025-10-19T22:44:23+02:00'
	draft = false
	title = 'My Amazing Website'
	+++
	 
	## Welcome to My Amazing Website
	 
	This is the best website in all internet.

    ### Choose your plan

    {{ partial "pricing-cards.html" }}
	```

3. If the page shows an error, stop hugo and restart again.
# Shortcodes

We can embed mini-templates into a `.md` file by using Shortcodes.

1. Create the following file: `layouts/shortcodes/pricing-cards.html`

	```html
	<link rel="stylesheet" href="https://unpkg.com/tachyons@4.12.0/css/tachyons.min.css">
	<link href="https://fonts.googleapis.com/css2?family=Roboto:wght@300;400;500;700&display=swap" rel="stylesheet">

	</div>
	<ul class="list pa0 ma0 mb4">
	<li class="mb2 fw4">✓ 1 User Account</li>
	<li class="mb2 fw4">✓ 10GB Storage</li>
	<li class="mb2 fw4">✓ Basic Support</li>
	<li class="mb2 fw4 gray">✗ Advanced Analytics</li>
	</ul>
	```

2. Add the shortcode into `content/_index.md`

	```markdown
	+++
	date = '2025-10-19T22:44:23+02:00'
	draft = false
	title = 'My Amazing Website'
	+++
	 
	## Welcome to My Amazing Website
	 
	This is the best website in all internet.
	 
	### Choose your plan
	 
	{{\< pricing-cards \>}}		## Do not use the backlashes, they are used only for escaping to avoid an error.
	```
3. If the page shows an error, stop hugo and restart again.

# Posts list

If we want to show the last five posts in a page, for ex `layout/index.md`, we can call `.Site.RegularPages` and iterate with `range`. We add a partial that is in charge of the look of the post.

```markdown
{{ range first 5 (where .Site.RegularPages "Section" "posts") }}
    {{ partial "post-card.html" . }}
{{ end }}
```

<br>
<br>

- **layouts/partials/post-card.html**

This is an example of a `post-card.html`

```html
{{ $featured_image := partial "func/GetFeaturedImage.html" . }}

<article class="mb5 w-100">
  <div class="flex flex-column flex-row-ns items-start">
    {{/* ---- IMAGE ---- */}}
    {{ if $featured_image }}
      <div class="w-100 w-40-ns pr3-ns mb3 mb0-ns">
        <a href="{{ .RelPermalink }}" class="db">
          <img src="{{ $featured_image }}" class="w-100 br2" alt="{{ .Title | plainify }}">
        </a>
      </div>
    {{ end }}

    {{/* ---- TEXT BOX ---- */}}
    <div class="{{ if $featured_image }}w-100 w-60-ns{{ end }} flex flex-column">
      <div class="bg-white br3 pa4 shadow-2 flex-grow-1">
        <h2 class="f3 fw1 mt0 mb3 lh-title">
          <a href="{{ .RelPermalink }}" class="link dim black">{{ .Title }}</a>
        </h2>

        <div class="f5 lh-copy mid-gray mb4">{{ .Summary }}</div>

        {{ with .Params.author | default .Site.Params.author }}
          <p class="f6 mt0 mb3 mid-gray">By {{ . }}</p>
        {{ end }}

        <a href="{{ .RelPermalink }}"
           class="f6 link dib ba b--black-20 br2 ph3 pv2 bg-near-white hover-bg-light-gray">
          {{ $.Param "read_more_copy" | default (T "readMore") }}
        </a>
      </div>
    </div>
  </div>
</article>
```


# Structure

Below is a **practical cheat-sheet** that shows **where each piece lives**, **what it is meant for**, and **how they relate** in a Hugo site.  
Think of it as the “who-does-what” map of Hugo’s templating system.

| Piece | Folder (default) | Primary purpose | When you use it | Key characteristics |
|-------|------------------|----------------|-----------------|---------------------|
| **_default** | `layouts/_default/` | **Fallback templates** for any content type that does not have its own specific template. | You have many content types (blog, docs, products…) and you don’t want to duplicate the same HTML for each. | Files like `baseof.html`, `single.html`, `list.html`, `home.html` live here. Hugo looks here **after** it checks a more specific folder (e.g. `layouts/post/`). |
| **partials** | `layouts/partials/` | **Reusable HTML snippets** that can be embedded anywhere (pages, shortcodes, other partials). | Header, footer, navigation, sidebar, social-share buttons, meta tags, etc. | Called with `{{ partial "header.html" . }}`. Receives the current context (`.`) and any data you pass (`partial "card.html" .Data`). No front-matter, pure HTML + Go template. |
| **shortcodes** | `layouts/shortcodes/` | **Markdown-embedded mini-templates** that let content authors write &#123;&#123;< name >&#125;&#125; or &#123;&#123;% name %&#125;&#125;. | Galleries, YouTube embeds, alerts, buttons, figures, call-outs, etc. | Two flavours: <br>• &#123;&#123;< … >&#125;&#125; – **HTML-escaped** (safe for inline text). <br>• &#123;&#123;% … %&#125;&#125; – **rendered as markdown** (allows nested markdown). Can have parameters and inner content. |
| **page** | `content/` (any section) | **Individual content file** (markdown, HTML, etc.) that becomes a **single page** (`kind = "page"`). | About, Contact, Landing pages, Documentation articles, etc. | Rendered with `layouts/_default/single.html` **or** a more specific template (e.g. `layouts/pages/single.html`). |
| **post** | `content/posts/` (or any section you configure) | **Blog-style article** that appears in lists, RSS, archives (`kind = "page"` but usually in a section named “post”). | Blog posts, news, tutorials. | Usually rendered with `layouts/_default/list.html` for the archive and `layouts/_default/single.html` (or `layouts/post/single.html`) for the article. |

---

## Visual Hierarchy (how Hugo decides which file to render)

```
content/
 └─ posts/
     └─ my-post.md   ──> kind = page (section = "posts")
                         └─ looks for:
                              1. layouts/posts/single.html
                              2. layouts/_default/single.html

layouts/
 ├─ _default/
 │    ├─ baseof.html      (wrapper)
 │    ├─ single.html      (fallback for any single page)
 │    └─ list.html        (fallback for any list page)
 ├─ posts/
 │    └─ single.html      (specific for post singles)
 ├─ pages/
 │    └─ single.html
 └─ partials/
      ├─ header.html
      └─ footer.html
```

### Rendering flow for a **post** (`content/posts/hello.md`)

1. **Determine kind** → `page` (single)  
2. **Look for template**:  
   - `layouts/posts/single.html` → **found** → use it.  
   - If missing → `layouts/_default/single.html`  
3. Inside that template you will typically see:

```html
{{ define "main" }}
  {{ partial "header.html" . }}
  <article>
    <h1>{{ .Title }}</h1>
    {{ .Content }}
  </article>
  {{ partial "footer.html" . }}
{{ end }}
```

---

## Quick “when to choose what”

| Need | Choose |
|------|--------|
| **Same HTML wrapper for every page** | `layouts/_default/baseof.html` + `{{ block "main" . }}{{ end }}` |
| **Header / Footer / Nav** | `partials/` |
| **Reusable UI component that may contain logic** | `partials/` (or a shortcode if authors need to drop it into markdown) |
| **Content authors should type &#123;&#123;< youtube id >&#125;&#125;** | `shortcodes/youtube.html` |
| **Content authors should type `{{< youtube oayOen6OOXw >}}`** | `shortcodes/youtube.html` |
| **A one-off landing page with custom layout** | Create `layouts/pages/single.html` **or** put a `layout: special` front-matter key |
| **Blog list page** | `layouts/_default/list.html` (or `layouts/posts/list.html`) |
| **All other pages (about, contact, docs…)** | Fall back to `_default/single.html` |

---

## Example Mini-Site Structure

```
my-site/
├─ content/
│   ├─ about.md
│   ├─ contact.md
│   └─ posts/
│       ├─ first.md
│       └─ second.md
├─ layouts/
│   ├─ _default/
│   │   ├─ baseof.html
│   │   ├─ single.html
│   │   └─ list.html
│   ├─ posts/
│   │   └─ list.html          # custom blog archive
│   └─ partials/
│       ├─ head.html
│       ├─ header.html
│       ├─ footer.html
│       └─ social.html
└─ layouts/shortcodes/
    ├─ figure.html
    └─ youtube.html
```

*`about.md` → `_default/single.html`*  
*`posts/*.md` → `posts/list.html` (archive) + `_default/single.html` (article)*  
*All pages share `baseof.html` → `head.html` → `header.html` → `footer.html`.*

---

### TL;DR

| Term | What it is | Where it lives | Typical use |
|------|------------|----------------|-------------|
| **_default** | Fallback layout templates | `layouts/_default/` | Base HTML for any kind of page |
| **partials** | Reusable HTML chunks | `layouts/partials/` | Header, footer, widgets |
| **shortcodes** | Markdown-callable mini-templates | `layouts/shortcodes/` | Embeds, alerts, galleries |
| **page** | Single piece of content | `content/` | Any standalone page |
| **post** | Blog article (still a “page” kind) | `content/posts/` (or any section) | Blog entries, news |

Use **_default** for the safety net, **partials** for DRY HTML, **shortcodes** for author-friendly markdown, and **specific layout folders** (`posts/`, `docs/`, etc.) when you need a custom look for a particular section. Happy Hugo-ing!



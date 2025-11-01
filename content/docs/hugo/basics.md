---
weight: 305
title: "Basics"
description: "Installation, setup and first steps"
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

## Create a base template site

```
hugo new site my-site
cd my-site
hugo new theme my-theme
echo "theme = 'my-theme'" >> hugo.toml
hugo server
```

## Create a site third party template site

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

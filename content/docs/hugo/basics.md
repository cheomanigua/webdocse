---
weight: 2100
title: "Basics"
description: "Introduction, installation, setup and first steps of Hugo"
icon: "article"
date: "2024-08-13T11:24:43+02:00"
lastmod: "2024-08-13T11:24:43+02:00"
draft: false
toc: true
---


Hugo is an open-source static site generator categorized as JAMStack. It splits the creation of a website between the [Content](https://gohugo.io/content-management/) (authors) and [Templates](https://gohugo.io/templates/introduction) (developer).

A paradigm of using Hugo (or any JAMstack) is to move the content from the database to markup files. This allows to represent unstructured content better but lost some of the capabilities to provide structured relationships.

Hugo [Taxonomies](https://gohugo.io/content-management/taxonomies/) attempt to build relationships between web pages by mimicking somehow a database.

The logic of a Hugo site is the domain of Templates, which uses [Go template](https://pkg.go.dev/html/template) language. 

# Installation

## Prerequisites

Although not required in all cases, [Git](https://git-scm.com/), [Go](https://go.dev/), and [Dart Sass](https://gohugo.io/hugo-pipes/transpile-sass-to-css/#dart-sass) are commonly used when working with Hugo. The following two methods to install '**Hugo**' also take care of the necessary dependecies.


##  Prebuilt binaries (latest)

Visit the [latest release](https://github.com/gohugoio/hugo/releases/latest) and scroll down to the Assets section. If you are using Debian or its derivative distros, follow these steps:

1. Download the last extended .deb package: `hugo_extended_0.131.0_linux-amd64.deb`
2. Run:
```
$ sudo apt install ./hugo_extended_0.131.0_linux-amd64.deb
$ hugo version
```

# Create a site

## Create a base theme site

```
hugo new site my-site
cd my-site
hugo new theme my-theme
echo "theme = 'my-theme'" >> hugo.toml
hugo server
```

## Create a third party theme site

The commands below use one of the four methods to add a theme to a Hugo site.

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

There are four different methods to download and add themes to your project: Hugo Module, Git Clone, Zip file and Git submodule.

##### 1. Hugo module (preferred)

{{< alert context="danger" text="You need [Go](https://go.dev/doc/install) version 1.18 or later and [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) to use Hugo Modules." />}}

1. Initialize your own hugo module system:

    ```
    $ hugo mod init github.com/[your_user]/[your_project] 
    ```

2. Import the theme in `hugo.toml`

    ```
    [module]
        [[module.import]]
            path = 'github.com/theNewDynamic/gohugo-theme-ananke/v2'
    ```

3. Update modules

    ```
    hugo mod get -u ./...
    ```

4. Create git repository (optional, but recommended)

    If want to download all the theme's directories and files in your project's directory, create a Git repository

    ```
    git init
    ```

More information about Hugo Modules: [https://gohugo.io/hugo-modules/use-modules/](https://gohugo.io/hugo-modules/use-modules/)

{{< alert context="warning" text="In the three methods below, don't forget to add the theme in `hugo.toml`." />}}

##### 2. Git Clone
```
$ git clone https://github.com/[user-account]/[hugo-theme].git themes/[hugo-theme]
$ cp -a themes[hugo-theme]/exampleSite/. .
```
If you want to update the theme:

```
$ cd themes/[hugo-theme]
$ git pull
```


##### 3. Download an unzip

Download the theme as a **zip** file. Extract in the directory `themes/[hugo-theme]`.


##### 4. Git Submodule

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

### Set the theme in your config file


```
$ echo "theme = 'theme-name'" >> hugo.toml
```


### Overriding a theme

In Hugo, layouts can live in either the project’s (root) or the themes’ layout folders. Any template inside the root layout folder will override the theme’s layout that is relative to it.

For example: `layouts/baseof.html` will override `themes/[hugo-theme]/layouts/baseof.html`. So, you can easily customize the theme without editing it directly, which makes updating the theme easier.

{{< alert text="If you added the theme using Hugo Modules, in order to get access to the theme's directories and files, you must create a Git repository in your root project directory: `git init`." />}}

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


# Running the site in developer mode

During development, you can visualize instantly the changes you make while editing your files every time you save your files. For that you have to run your site in developer mode:

```
$ hugo server
```

# Publish the site

Publishing a site does not deploy it. Hugo creates the entire static site in the `public` directory in the root of your project. This includes the HTML files, and assets such as images, CSS files, and JavaScript files.

```
$ hugo
```

# Deploying the site

You can upload your site directly from your local machine to a cloud provider by running:

```
$ hugo deploy
```

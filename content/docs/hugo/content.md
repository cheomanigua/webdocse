---
weight: 320
title: "Content"
description: ""
icon: "article"
date: "2025-11-02T05:52:19-07:00"
lastmod: "2025-11-02T05:52:19-07:00"
draft: false
toc: true
---

# Sections

We can classify content into sections like blog, news, or products. Each section can have multiple pages that form its content, however, it is mandatory to have an `_index.md`, even an empty one, for Hugo to consider the directory to be a section. We use the section’s index page to help the user navigate the section.

- `content/posts/_index.md`
- `content/news/_index.md`
- `content/products/_index.md`
- `content/<directory>/_index.md`

{{< alert context="warning" text="We need to create a markup file named `_index.md`, even if empty, at the root of every section. Hugo uses this file to identify a website section. Hugo does not consider a folder to be a section without this file and might not display its contents." />}}

The sections have a template for the index page where we have access to all the pages and subsections. The template is located at `layouts/section.html`

## Summary

The index pages of the sections automatically provide a summary and titles of the posts in the section. Hugo generates the summary information automatically if we do not provide it. Although the index pages are the most common places where we can use a post’s summary, it can also be used elsewhere by the theme. Here are the ways to provide the summary:

- **Automatic**: By default, Hugo picks up the first 70 words of the content as the summary for the theme. The `summaryLength` variable in the website configuration file controls the number of words in the default summary.
- **Manual**: We can specify the summary length with the marker **&#60;!&#45;&#45;more&#45;&#45;&#62;** at a location in the content to clip the summary if desired. Note that **&#60;!&#45;&#45;more&#45;&#45;&#62;** is matched verbatim by Hugo. Adding spaces, changing to upper-case, or not having a proper HTML comment is not understood by Hugo. You can see this in use in the content/blog/tropical triangles/index.md file.
- **Front matter**: We can use the summary variable in the front matter to supply the summary text.

Note that the summary variable is different than the `description` variable. The `summary` variable in Hugo is a teaser into the content, while the `description` variable is more about the content sent to search engines. The `description` field can only be supplied via the front matter and does not have an automatic value based on content. Some themes like Eclectic give a higher priority to description and fall back to `summary` in the index pages.


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


# Bundles

## Leaf bundles

We can add all necessary resources for a page in a leaf bundle. This is fine if no other pages are going to use those resources.

In the worflow below, we convert a single page called `about.md` to a bundle:

1. Move to the root of `about.md` page, normally `content/`
2. Create a new folder called `about/`
3. Copy `about.md` file to `about/` folder
4. Rename `about.md` to `index.md` (note that is not `_index.md`
5. Add all resources necessary to render the page into `about/` folder

{{< alert context="light" text="**index.md** vs **_index.md**<br><br>While `_index.md` and `index.md` are similar names, they are two very different files: `index.md` represents the content of a single web page, and `_index.md` represents a section’s root, which is a set of web pages." />}}

We can move a leaf bundle independently to a different Hugo website, which provides everything needed to render it correctly.

## Branch bundles

Branch bundles form a collection of both textual and nontextual resources that represent a website section. An ideal branch bundle contains page bundles for all the pages in the section, the `_index` file, and the resources referred to in the index page.

The objective of the independent branch bundle is the same as that of a leaf bundle: to allow sections to be dropped into the website and to become functional with no other change in the site. A branch bundle should ideally set its menu entries and provide all the assets referred to in the branch and then be good to go.

## Branch vs Leaf bundles

||Branch|Leaf
-|-|-
Used for|Section|Single web page
Contents|Header and (as part of the theme) a list of pages in the section|Contents of the web page
Index Page|\_index.md|index.md
Template layout page|list.html|single.html


# Images

There are three places to store images: a page bundle and two folders called `static` and `assets`.

The images specific to a page bundle belong to the page bundle. As we create more web pages, we should try to stay in the content folder and rarely update anything outside of this folder. The images used by the theme to render the website do not belong in that folder.

The folders `static` and `assets` are similar in their type of content with just one difference: images in the `assets` folder can be preprocessed and optimized by Hugo. Images in the `static` folder are used as-is.

Ideally, we should move as many images as possible to the `assets` folder to utilize the complete set of Hugo’s image optimization features for these files. The `static` folder, in this case, should only be used for images that are accessed directly from HTML without being processed by Hugo.


# Taxonomies

Taxonomies are higher-level constructs that we can use to group the pages that describe relationships between web pages.

A paradigm of using Hugo (or any JAMstack) is to move the content from the database to the markup files. We gained the ability to represent unstructured content better but lost some of the capabilities to provide structured relationships. Taxonomies attempt to build relationships between web pages.

Taxonomies have two parts: the **lists** and the **terms**. Each taxonomy list is a collection of taxonomy terms. A page can be associated with many taxonomy terms, and a taxonomy term can be associated with many pages.

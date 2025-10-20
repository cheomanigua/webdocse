---
weight: 5050
title: "Basics"
description: ""
icon: "article"
date: "2025-10-20T09:23:34+02:00"
lastmod: "2025-10-20T09:23:34+02:00"
draft: false
toc: true
---

## Installation

### Debian

1. Download the appropiate tarball in [https://go.dev/dl/](https://go.dev/dl/)
2. Extract to `/usr/local/`:
	```bash
	$ sudo tar -C /usr/local -xzf go1.25.3.linux-amd64.tar.gz
	```
3. Add **Go** to your PATH by editing your shell configuration `~/.bashrc`:
	```bash
	$ echo 'export PATH=$PATH:/usr/local/go/bin' >> ~/.bashrc
	$ source ~/.bashrc
	```
4. Verify:
	```bash
	$ go version
	$ which go
	$ echo $PATH
	```



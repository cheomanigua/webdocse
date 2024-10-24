---
weight: 50
title: "Linux"
description: "Several commonly used commands for Linux"
icon: "article"
date: "2024-10-23T14:16:02+02:00"
lastmod: "2024-10-23T14:16:02+02:00"
draft: false
toc: true
---

### Basics

#### Finding text and files

- `grep -R "text"` - Find specific string in current directory and child directories
- `find /usr -iname "wallpaper.jpg"` - Find all files containing the specified string
- `sed -i 's/original/new/g' file.txt` - Replace the specified string in a file


#### How to free a port:

```
sudo lsof -i :8080
kill -9 "PID of the port"
```

### vscode

- Comment:		Ctrl + K Ctrl + C
- Uncomment:		Ctrl + K Ctrl + U
- Show/Hide Explorer	Ctrl + B
- Selection up/down	Ctrl + Shift + up/down
- Selection left/right	Ctrl + Shift + left/right


### ranger

- **y**: copy, yank 
- **p**: paste
- **d**: cut
- **a**: rename
- **cw**: rename
- **c**: search
- **dD**: console delete
- **dT**: console trash
- **f**: find
- **g**: cd
- **h, j, k, l**: navigate
- **m<key>**: bookmark directory
- **`<key>**: go to bookmarked directory
- **o**: chain
- **q**: exit
- **r**: open with ...
- **s**: shell ...
- **t**: tag
- **ut**: untag
- **ud, uy**: uncut
- **w**: task view
- **z**: set
- **space**: mark a file


### Groff

Basic
```
groff -ms test.ms -T pdf > test.pdf
```

UTF8 Characters
```
groff -ms -K utf8 test.ms -T pdf > test.pdf
```

Tables
```
groff -ms -t test.ms -T pdf > test.pdf
```

Mathematical
```
groff -ms -e test.ms -T pdf > test.pdf
```

Graphics
```
grooff -ms -p text.ms -T pdf > test.pdf
```

Flags are cumulative. For instance, if you need to print UTF8 characters and tables:
```
groff -ms -t -K utf8 test.ms -T pdf > test.pdf
```

If you just want to see the changes of your document, you can use:
```
groffer test.ms
```

or if you need special utf8 characters:
```
groffer -Kutf8 test.ms
```


### Wezterm

- **Cmd** + **t**: New tab
- **Ctrl** + **Tab**: Cycle tabs
- **Ctrl** + **Shift** + **Alt** + **"**: Split vertically
- **Ctrl** + **Shift** + **Alt** + **%**: Split horizontally


### Tmux

#### Installation

```
$ sudo apt install tmux
```

#### Color and mouse scroll configuration

```
$ touch tmux.conf
$ echo "set -g default-terminal \"screen-256color\"" >> tmux.conf
$ echo "set -g mouse on" >> tmux.conf
$ sudo chown root:root tmux.conf
$ sudo mv tmux.conf /etc
```


#### Key bindings

- Leader: **Ctrl** + **b**
- New window: **leader** + **c**
- Cycle throug windows: **leader** + **n**
- Select window: **leader** + **number**
- New vertical pane: **leader** + **%**
- New horizontal pane: **leader** + **"**
- Move between panes: **leader** + **arrows**
- Resize pane: **hold leader** + **arrows**
- Detach from Tmux: **leader** + **d**

Bash:
- `$ tmux ls`: List sessions
- `$ tmux attach`: Reattach to tmux

Create new session:
1. Detach from tmux
2. Run tmux

- List sessions: **leader** + **s**
- Change session: ***leader** + **s*** and choose
- List all windows in all sessions: **leader** + **w**
- Change session and/or window: **leader** + **w** and choose



COMMAND MODE
- Enter command mode: **leader** + **:**

Then:

- Rename window: `rename-window` + *your_window_name*
- Rename session: `rename-session` or `rename` + *your_session_name*

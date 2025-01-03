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


### LaTeX

Install
```
$ sudo apt install texlive-latex-base
```

Compile `tex` file to `pdf` file
```
$ pdflatex file.tex
```

### Groff

Groff uses four macro packages: **ms**, **mm**, **me** and **mom**. In this document only **ms** and **mom** macro packages will be explained.

#### Compilation

Compile `ms` file to `pdf` file

```
pdfroff -ms test.ms > test.pdf
```

Compile `mom` file to `pdf` file
```
pdfmon test.mom > test.pdf
```

#### Preprocessors

UTF8 Characters
```
pdfroff -ms -K utf8 test.ms > test.pdf
pdfmom -K utf8 test.mom > test.pdf
```

Tables
```
pdfroff -ms -t test.ms > test.pdf
pdfmom -t test.mom > test.pdf
```

Mathematical
```
pdfroff -ms -e test.ms > test.pdf
pdfmom -e test.mom > test.pdf
```

Images
```
pdfroff -ms -U text.ms > test.pdf

```
Graphics
```
pdfroff -ms -p text.ms > test.pdf
pdfmom -p text.mom > test.pdf
```

Preprocessor flags are cumulative. For instance, if you need to print UTF8 characters and tables:
```
pdfroff -ms -t -K utf8 test.ms > test.pdf
pdfmom -t -K utf8 test.mom > test.pdf
```

If you just want to see the changes of your document, you can use:
```
groffer test.ms
```

or if you need special utf8 characters:
```
groffer -Kutf8 test.ms
```

#### MS Lists

- Bulleted list: `.IP \(bu 2`
- Numbered list: `.IP \n+[step] 4`

#### MOM Lists

- Bulleted list: `.LIST`
- Dash list: `.LIST DASH`
- Numbered list: `.LIST DIGIT`
- Alphabetical list: `.LIST alpha`
- Roman list: `.LIST roman2`


#### MS Custom macro

1. Create macro at the beginning of document
```
.de BL
.IP \(bu
..
```
2. Use macro anywhere in the document:
```
.BL
This is a bullet point
.BL
This is another bullet point
```

#### Images

You can embed PDF and EPS format images only. I recommend embeding PDF images.

**MS**
```
.PDFPIC "myimage.pdf" <width>
```

You can optionally add the width of the image. For instance: `600p`. The height will be automatically calculated.

**MOM**

To embed a PDF image of width 600p, height 250p and scaled down to 50% of its size:
```
.PDF_IMAGE myimage.pdf 600p 250p SCALE 50
```

If you need to convert any other image format to a pdf image format:
```
$ convert myimage.png myimage.pdf
```

If you need to get the width and height of a pdf image:
```
$ pdfinfo myimage.pdf
```

### Markdown

From Markdown to PDF:
```
$ sudo install pandoc groff ghostscript --no-install-recommends
$ pandoc --pdf-engine=pdfroff --toc-depth=3 myfile.md -o myfile.pdf
```


### Wezterm

- **Cmd** + **t**: New tab
- **Ctrl** + **Tab**: Cycle tabs
- **Ctrl** + **Shift** + **Alt** + **2**: Split vertically
- **Ctrl** + **Shift** + **Alt** + **5**: Split horizontally
- **Ctrl** + **Shift** + **Arroy keys**: Move to split pane


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

### awk & grep like SQL query

**awk** and **grep** can be used for data analysis similar to **SQL** queries. In this example, we are using a **.csv** file as database. The fields correspond to: type, name, quantity, school, npc, location, building, canton, city.

```
SHOW * FROM databasemw;
is equivalent to
cat databasemw.csv
awk -F, '{print $0}' databasemw.csv

SHOW * FROM databasemw WHERE city='balmora';
is equivalent to
grep balmora databasemw.csv
awk -F, '/balmora/ {print $0}' databasemw.csv
awk -F, '$9="balmora" {print $0}' databasemw.csv

SHOW * FROM databasemw WHERE type='spell' AND city='balmora';
is equivalent to
grep spell databasemw.csv | grep balmora
awk -F, '/spell/ && /balmora/ {print $0}' databasemw.csv
awk -F, '$1=="spell" && $9=="balmora" {print $0}' databasemw.csv

SHOW * FROM databasemw WHERE type='spell' AND npc='Ranis Athrys' AND city='balmora'
is equivalent to
grep spell databasemw.csv | grep balmora | grep 'Ranis Athrys'
awk -F, '/spell/ && /balmora/ && /Ranis Athrys/ {print $0}' databasemw.csv
awk -F, '$1=="spell" && $9=="balmora" && $5="Ranis Athrys" {print $0}' databasemw.csv

SHOW npc FROM databasemw WHERE type='spell' AND name='soul trap'
awk -F, '/spell/ && /soul trap/ {print $5}' databasemw.csv
awk -F, '$1=="spell" && $2=="soul trap" {print $5}' databasemw.csv

SHOW * FROM databasemw WHERE quantity>4 AND city='vivec'
awk -F, '$3>4 && /vivec/ {print $0}' databasemw.csv
awk -F, '$3>4 && $9=="vivec" {print $0}' databasemw.csv
```

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
- `sed 's/original/new/g' file.txt` - Preview the replacement preserving the file intact


#### How to free a port:

```
sudo lsof -i :8080
kill -9 "PID of the port"
```


### Wezterm

- **Cmd** + **t**: New tab
- **Ctrl** + **Tab**: Cycle tabs
- **Ctrl** + **Shift** + **Alt** + **"**: Split vertically
- **Ctrl** + **Shift** + **Alt** + **%**: Split horizontally
- **Ctrl** + **Shift** + **Arroy keys**: Move to split pane


### Kitty

- **Ctrl** + **Shift** + **T**: New tab
- **Ctrl** + **Tab**: Cycle tabs
- **Ctrl** + **Shift** + **Return**: Split window (1st vertically, 2nd horinzotally)
- **Ctrl** + **Shift** + **[**: Previous window
- **Ctrl** + **Shift** + **]**: Next window
- **Ctrl** + **Shift** + **R**: Enter size mode
- **Ctrl** + **T**: In size mode, increase size
- **Ctrl** + **S**: In size mode, decrease size


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



### VSCodium

##### General

- Comment line or selection: `Ctrl` + `K` + `C`
- Uncomment line or selection: `Ctrl` + `K` + `U`
- Delete word on the left/right of cursor: `Ctrl` + `Backspace/Delete`
- Change theme: `Ctrl` + `K` + `T`
- Toggle word wrap: `Alt` + `Z`
- Find: `Ctrl` + `F`
- Find and Replace: `Ctrl` + `H`
- Auto-format: `Ctrl` + `Shift` + `I`
- Close/Open the Explorer: `Ctrl` + `B`
- Close/Open the Terminal: `Ctrl` + `J`
- Focus on the Terminal: `Ctrl` + `\``

##### Selection

- Select all ocurrences of selection: `Ctrl` + `Shift` + `L`
- Select N number of ocurrences of selection: `Ctrl` + `D` several times
- Select element to jump to: `Ctrl` + `Shift` + `\`
- Select whole word: `Ctrl` + `Shift` + `Left/Right`
- Select line above/below (cumulative): `Shift` + `Up/Down`
- Select line above/below (cumulative both ways): `Ctrl` + `Shift` + `Up/Down`
- Select all above/below cursor: `Ctrl` + `Inicio/Fin`
- Select up/down:	`Ctrl` + `Shift` + `Up/Down`
- Select left/right:	Ctrl + Shift + `Left/Right`

##### Lines

- Cut line: `Ctrl` + `X`
- Copy line: `Ctrl` + `C`
- Delete line: `Ctrl` + `Shift` + `K`
- Insert below: `Ctrl` + `Enter`
- Insert above: `Ctrl` + `Shift` + `Enter`
- Move line up/down: `Alt` + `Up/Down`
- Move selected lines (block) up/down: `Alt` + `Up/Down`
- Duplicate line above/below: `Ctrl` + `Shift` + `Alt` + `Up/Down`
- Go to beginning/end of line: `Inicio/Fin`
- Go to beginning/end of file: `Ctrl` + `Inicio/Fin` or `RePág/AvPág`



### Code::Blocks

#### Installation

##### Not the newest version

```bash
$ sudo apt install codeblocks
```

##### The newest version

```bash
$ sudo add-apt-repository ppa:x-psoud/cbreleases
$ apt policy | grep x-psoud
$ sudo vim /etc/apt/preferences.d/codeblocks-ppa
```
Add this to `/etc/apt/preferences.d/codeblocks-ppa` to give priority to **x-psoud/cbreleases** so codeblocks is not fetched from the default repository:

```
Package: codeblocks*
Pin: release o=LP-PPA-x-psoud-cbreleases
Pin-Priority: 1001
```
```bash
$ sudo apt update
$ sudo apt install codeblocks
```

#### Clang

After installation, if you want to use the Clang compiler, you will probably need to install `clang` and `libstdc++`.

##### Install

```bash
sudo apt install clang
```

To find out which `libstdc++` version Clang needs, run the following command:

```bash
$ clang -v
```

On the results, look for this line:

```
Selected GCC installation: /usr/bin/../lib/gcc/x86_64-linux-gnu/12
```

In above result, the version is 12. In order to install version 12 of libstdc++, run:

```bash
$ sudo apt install libstdc++-12-dev
```

### ranger

- **y**: copy, yank 
- **p**: paste 
- **d**: cut
- **a**: rename
- **cw**: rename |**r**: open with ... |**g**: cd |**w**: task view |
- **c**: search
- **dD**: console delete
- **dT**: console trash 
- **f**: find
- **g**: cd
- **h, j, k, l**: navigate
- **m\<key\>**: bookmark directory
- **`\<key\>**: go to bookmarked directory
- **o**: chain
- **q**: exit
- **r**: open with ...
- **s**: shell ...
- **t**: tag
- **ut**: untag
- **ud, uy**: uncut
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
groff -ms test.ms > test.pdf
```

Compile `mom` file to `pdf` file
```
pdfmon test.mom > test.pdf
```

#### Preprocessors

UTF8 Characters
```
groff -ms -K utf8 test.ms > test.pdf
pdfmom -K utf8 test.mom > test.pdf
```

Tables
```
groff -ms -t test.ms > test.pdf
pdfmom -t test.mom > test.pdf
```

Mathematical
```
groff -ms -e test.ms > test.pdf
pdfmom -e test.mom > test.pdf
```

Images
```
groff -ms -U text.ms > test.pdf
pdfmom -U text.mom > test.pdf

```
Graphics
```
groff -ms -p text.ms > test.pdf
pdfmom -p text.mom > test.pdf
```

Preprocessor flags are cumulative. For instance, if you need to print UTF8 characters and tables:
```
groff -ms -t -K utf8 test.ms > test.pdf
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
$ pandoc --pdf-engine=groff --toc-depth=3 myfile.md -o myfile.pdf
```


### sc spreadsheet

| | |
|-|-|
|**<** : insert string left indented||
|**>** : insert string right indented|**x** : delete cell content|
|**\\** : insert string centered|**dd**, **dr**, **dc** : delete current cell, row, column|
|**=** : insert number, expresion or function (1, 1+1, B1+B2, @sum(B1:B5))|**yy**, **yr**, **yc** : copy current cell, row, column|
|**{**, **\|**, **}** : left justify, center, right justify the string in the current cell|**pp**, **pr**, **pc** : paste cell, row, column|
|**r{**, **r\|**, **r}** : left justify, center, right justify all strings in the specified range|**m** + character = copy content of cell|
|**f** + **h**, **f** + **<**, **f** + **left**: decrease column width|**c** + character = paste content of cell|
|**f** + **l**, **f** + **>**, **f** + **right**: increase column width|**E** : edit cell|
|**f** + **j**, **f** + **down** : decrease column decimal places|**P** + **enter** : quick save|
|**f** + **k**, **f** + **up** : increase column decimal places|**ZZ** : save & exit|


### awk & grep like SQL query

**awk** and **grep** can be used for data analysis similar to **SQL** queries. In this example, we are using a **.csv** file as database. The fields correspond to: type, name, quantity, school, npc, location, building, canton, city.

```
SELECT * FROM databasemw;
is equivalent to
cat databasemw.csv
awk '{print}' databasemw.csv
awk '{print $0}' databasemw.csv

SELECT * FROM databasemw WHERE city='balmora';
is equivalent to
grep balmora databasemw.csv
awk '/balmora/' databasemw.csv
awk '/balmora/ {print}' databasemw.csv
awk '/balmora/ {print $0}' databasemw.csv
awk -F, '$9=="balmora"' databasemw.csv
awk -F, '$9=="balmora" {print}' databasemw.csv
awk -F, '$9=="balmora" {print $0}' databasemw.csv
awk -F, '$9~/balmora/' databasemw.csv
awk -F, '$9~/balmora/ {print}' databasemw.csv
awk -F, '$9~/balmora/ {print $0}' databasemw.csv
```

As you can see from the example above, when printing the whole line (equivalent to SELECT * FROM), we can use {print}, {print $0} or nothing. In the next examples below, we are not using neither {print} or {print $0}.

```
SELECT * FROM databasemw WHERE type='spell' AND city='balmora';
is equivalent to
grep spell databasemw.csv | grep balmora
awk '/spell/ && /balmora/' databasemw.csv
awk -F, '$1=="spell" && $9=="balmora"' databasemw.csv
awk -F, '$1~/spell/ && $9~/balmora/' databasemw.csv

SELECT * FROM databasemw WHERE type='spell' AND npc='Ranis Athrys' AND city='balmora'
is equivalent to
grep spell databasemw.csv | grep balmora | grep 'Ranis Athrys'
awk '/spell/ && /balmora/ && /Ranis Athrys/' databasemw.csv
awk -F, '$1=="spell" && $9=="balmora" && $5=="Ranis Athrys"' databasemw.csv
awk -F, '$1~/spell/ && $9~/balmora/ && $5~/Ranis Athrys/' databasemw.csv

SELECT npc FROM databasemw WHERE type='spell' AND name='soul trap'
awk -F, '/spell/ && /soul trap/ {print $5}' databasemw.csv
awk -F, '$1=="spell" && $2=="soul trap" {print $5}' databasemw.csv
awk -F, '$1~/spell/ && $2~/soul trap/ {print $5}' databasemw.csv

SELECT * FROM databasemw WHERE quantity>4 AND city='vivec'
awk -F, '$3>4 && /vivec/' databasemw.csv
awk -F, '$3>4 && $9=="vivec"' databasemw.csv
awk -F, '$3>4 && $9~/vivec/' databasemw.csv
```

### Adding apps to Launch Menu

1. Add an `png` icon file either in `.local/share/icons` or `/usr/share/icons`
2. Create a `.desktop` file either in `.local/share/applications` or `/usr/share/applications`
3. Where you created the `.desktop` file, execute: `sudo desktop-file-install myapp.desktop`

Example with **VSCodium** application:

1. We add the file `vscodium.png` to `.local/share/icons`
2. We create the file `vscodium.desktop` in `.local/share/applications` with the following content:

    ```
    [Desktop Entry]
    Name=VSCodium
    Comment=Code Editing. Redefined.
    GenericName=Text Editor
    Exec=/usr/share/codium/codium
    Icon=vscodium
    Type=Application
    Categories=TextEditor;Development;IDE;
    ```

3. Within `local/share/applications` directory, we run the command:

    ```
    $ sudo desktop-file-install vscodium.desktop
    ```

**Note:** We can check if the `.desktop` file is correct by executing:

```
$ desktop-file-validate vscodium.desktop
```

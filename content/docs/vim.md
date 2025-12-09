---
weight: 200
title: "Vim"
description: ""
icon: "article"
date: "2024-08-13T11:20:17+02:00"
lastmod: "2024-08-13T11:20:17+02:00"
draft: false
toc: true
---

# 1. Key binding

### Save/Quit

- Save file -\> **:w**
- Save file and quit -\> **:x** or **ZZ**
- Quit -\> **:q**
- Quit discarding changes -\> **:q!** or **ZQ**

### Modes

- Normal -\> **ESC**, **Ctrl** + **c**
- Insert -\> **i**, **I**, **a**, **A**
- Replace -\> **R**
- Visual -\> **v**, **V**, **Ctrl** + **v**
- Command -\> **:**

### Indentation

- Insert Mode:
  - **Ctrl** + **d** : shift left
  - **Ctrl** + **t** : shift right
- Normal mode:
  - **Shift** + **«** : shift current line left
  - **Shift** + **»** : shift current line right
- Visual mode:
  - **<** : shift selection left
  - **>** : shift selection right


### Visual mode multi-line editing
1. Move the cursor exactly where you want to insert the new text
2. `Ctrl + v`
3. Move up or down the lines to select several lines
4. `Shift + i`
5. Type in the text
6. `ESC`

The typed text will replicate to all selected lines


### Moving around

- Search forward and backward once -\> **/** and **?**
- On search: (next hit -\> **n**, previous hit -\> **N**)
- Jump into the next/previous ocurrence of character x -/> **fx**/**Fx**
- Jump up to the next/previous ocurrence of character x -/> **tx**/**Tx**
- Repeat previous **f/F** movement forward/backward -\> **;**/**,**
- Search for next/previous match currently under cursor -\> **asterisk** / **#**
- Find and replace all occurrences of *foo* with *bar* -\> **:%s/foo/bar/g**
- Jump to the older/newer cursor position -/> **Ctrl** + **o** / **Ctrl** + **i**
- Move to the paired parenthesis/curly braces -\> **%**
- Move to the beginning/end of a word (forward) -\> **w** / **e**
- Move to the beginning/end of a word (backward) -\> **b** / **ge**
- Move to beginning/end of line and start editing -\> **I** / **A**
- Move to the beginning/end of the line -\> **0** / **$**
- Move to the first character of the line -\> **_**
- Jump to line 34 -\> **:34**
- Jump forward/backward one line -\> **j** / **k**
- Jump forward/backward one paragraph -\> **}** / **{**
- Jump to the top/middle/bottom of the page -\> **H** / **M** / **L**
- Jump forward/backward one page -\> **Ctrl** + **f**  / **Ctrl** + **b**
- Jump up/down half page -\> **Ctrl** + **u**  / **Ctrl** + **d**
- Jump to next line with same content -\> **Right Alt** + **3**
- Move screen down/up one line (without moving cursor) -\> **Ctrl** + **e** / **Ctrl** + **y**
- Move cursor to the /top/middle/bottom of the page -\> **zt** / **zz** / **zb**
- Move cursor to the beginning/end of document -\> **gg** / **G**
- Move cursor to a specific line number -\> **:10**
- Move cursor X lines above/below -\> **10k**/**10j**
- Jump to the older/newer cursor position -\> **Ctrl** + **o** / **Ctrl** + **i**

### Line numbers
- Activate absolute line numbers -\> **:set nu**
- Activate relative line numbers -\> **:set rnu**
- Toggle absolute line numbers -\> **:set nu!**
- Toggle relative line numbers -\> **:set rnu!**

### Editing

#### Operators
Operators specify which operation to perfom:

- **d** -\> Delete/Cut
- **y** -\> Yank (copy)
- **c** -\> Change (delete and enter insert mode)
- **r** -\> Replace
- **v** -\> Visually select

If you want to copy text and paste outside Vim, you need to visually select the text and press at a time: `"+y`

#### Motion
Motion specifies where the Operators operate in relation to the cursor position:

- **w** -\> word 
- **W** -\> all text between empty spaces 
- **p** -\> paragraph 
- **_** -\> line
- **2j** -\> down 2 lines
- **e** -\> until the end of the word
- **$** -\> until the end of the line
- **0** -\> from the beginning of the line
- **t/T** -> until a specific character
- **f/F** -> until a specific character, including the specific character
- **Ctrl** + **v** -\> Select block
- **i** -\> INNER: inside/between something
  - **iw** -\> "inner word" (works from anywhere in a word)
  - **it** -\> "inner tag" (works within the contents of an HTML tag)
  - **i"** -\> "inner quotes"
  - **i_** -\> "inner line"
  - **is** -\> "inner sentence"
  - **ip** -\> "inner paragraph"
- **a** -\> AROUND: like INNER, but including the tag, quotes, etc
  - **at** -\> "around tag" (works within the content of an HTML tag including the tags)
  - **a"** -\> "around quotes" (works within the content of including the quotes)
  - **as** -\> "around sentence"
  - **ap** -\> "around paragraph"

Examples:

- Copy everything inside parenthesis: `yi(`
- Copy everything around parenthesis: `ya(`
- Copy everything between empty spaces: `yiW`
- Delete everything until the end of the line: `d$`
- Delete everything to the beginning of the line: `d0`
- Delete everything within/closest tag, type: `dit`
- Delete everything until it finds a dot: `dt.`
- Delete everything until it finds a dot, including the dot: `df.`
- Delete everything between paired parenthesis/curly braces: `da%`
- Select the content within/closest parenthesis: `vi(`
- Delete the content within/closest parenthesis: `di(`
- Delete the content within/closest parenthesis and start typing: `ci(`

#### Common commands
- repeat last operation -\> **.**
- copy selection -\> **y**
- copy character-\> **yl**
- copy word -\> **yiw**
- copy line -\> **yy**
- copy paragraph -\> **yap**
- Select paragraph -\> **vip**
- delete/cut selection -\> **d** 
- delete/cut word from cursor position forward -\> **dw**
- delete/cut word from cursor position backward -\> **db**
- delete/cut whole word -\> **diw**
- delete/cut word and insert mode -\> **cw** 
- delete/cut paragraph -\> **dap**
- paste -\> **p**
- undo -\> **u**
- redo -\> **ctrl** + **r**
- delete character or selection (Del) -\> **x**
- delete character or selection (Backspace) -\> **X**
- replace character -\> **r**
- delete character and insert mode -\> **s**
- Switch to 'Insert' mode -\> **i**
- Exit 'Insert' mode -\> **ESC** or **Ctrl** + **c**
- Increase/Decrease the first number in a line: **Ctrl** + **a**/**x**
- Format block of text -\> **V** + *text selection* + **=**
- Repeat command mode last command -\> **@:**


#### Lines

- create new line below -\> **o**
- create a new line above -\> **O**
- copy line -\> **yy**
- copy all lines under the cursor -\> **:.,$y** or **:+,$y**
- paste -\> **p**
- paste line above -\> **P**
- delete/cut line -\> **dd**
- delete/cut the whole line and insert mode -\> **S**
- delete/cut everything from the beginning of the line up to the cursor -\> **d0**
- delete/cut everything from the cursor to the end of the line -\> **D** or **d$**
- change everything from the cursor to the end of the line -\> **C** or **c$**
- yank/copy everything from the cursor to the end of the line -\> **Y** or **y$**
- delete/cut from the current line to the end of the file -\> **:.,$d** or **:+,$d**
- delete/cut from the current line to the beginning of the file -\> **:.,1d**
- delete/cut from line 3 to line 10 -\> **:3,10d**
- delete/cut from line 3 to the end of the file -\> **:3,$d**
- delete/cut the last line-\> **:$d**
- delete/cut all lines-\> **:%d**
- join line below to current line: **J**
- move current line after line 6 -\> **:m 6**
- move line two lines above -\> **:m-2** (note that **:m-1** won't work)
- move line one line below -\> **:m+1**
- move line to beginning/end of document -\> **:m 0** / **:m $**
- move block of lines -\> **:5,7m 12** (move lines 5,6,7 after line 12)
- move block of lines -\> **:5,7m+4** (move lines 5,6,7 four lines below)
- indent current line -\> **<<** or **>>**
- indent current line plus 2 lines below -\> **3<<** or **3>>**
- select a range of lines and indent -\> **:4,17<**
- Delete all lines containing the string “foo”. It also removes line where “foo” is embedded in larger words, such as “football” -\> **:g/foo/d**
- Delete all lines not containing the string “foo” -\> **:g!/foo/d**
- Remove all comments from a Bash script. The pattern ^# means each line beginning with # -\> **:g/^#/d**
- Remove all blank lines. The pattern ^$ matches all empty lines -\> **:g/^$/d**


#### Special operations

- Comment a block of lines:
  1. Select the block of lines with **V**
  2. Enter command mode and type: `norm I#`

- Uncomment a block of lines:
  1. Select the block of lines with **V**
  2. Enter command mode and type: `norm x` 

- Find and replace all occurrences of *foo* with *bar* -\> **:%s/foo/bar/g**

- Find and replace *foo* with *bar* X number of times:
  1. Search for *foo*: `/foo` + **enter**
  2. Type `cgn` + `bar`
  3. **ESC**
  4. Iterate through the results:
    - Press **n** to move to next *foo* instance
    - Press **.** to replace *foo* for *bar*


- Move all lines that start with specific character/s to the end of the document. In this example, the specific characters is `static`:
```
:g/^static/m$`
```

- Move all lines that contain specific character/s to the end of the document. In this example, the specific characters is `static`:
```
:g/static/m$`
```

## File Management

- **Explorer**: Tool to create/open/delete files and directories.
- **Buffer**: Space in memory created automatically when opening a file with Explorer. Each open file will be stored in a different buffer.
- **Window**: View space in a tab where a buffer is rendered at a time. You can create new windows within a tab by using the split command (vertically or horizontally) to accomodate other buffers.
    - Closing a window will not delete the buffer.
    - Opening a new file with Explorer in the same window will not delete the previous buffer.
- **Tab**: A screen. It's the parent container where one or more windows exist. A vim session starts with one tab, but you can create several extra tabs in a session. Each tab can contain different windows. Closing a tab will not delete the buffers.

### Explorer

- Open file explorer -\> **:e .**
- Open file explorer -\> **:Ex**
- Open file explorer -\> **:Explore**
- Open a new 100 characters width window explorer -\> **:100vs .**
- Open new file -\> **:e <filename path\>** (creates a buffer)
- Open/create a file in new window horizontally -\> **sp: filename** (creates a buffer)
- Open/create a file in new window vertically -\> **vsp: filename** (creates a buffer)
- Create a directory within Explorer -\> **d**
- Create a file within Explorer -\> **%**
- Delete a file/directory within Explorer -\> Place the cursor over the file/directory and press **D**
- Create a new file: `:e filename`
- List files in current directory -\> **:e** then press **Space** and **Ctrl**+**d**

### Buffers

- Create a buffer -\> Open a file with Explorer
- List active buffers -\> **:ls**
- Change to specific buffer -\> **:b <buffer-number\>**
- Move to next/previous buffer -\> **:bn** / **:bp**
- Delete current buffer -\> **:bd**
- Delete specific buffer -\> **:bd <buffer-number\>**
- Jump back/forward to last jump location -\> **Ctrl** + **o** / **Ctrl** + **i**

### Windows
- Split screen horizontally -\> **:split** or **:sp** or **Ctrl** + **w** + **s**
- Split screen vertically -\> **:vsplit** or **:vs** or **Ctrl** + **w** + **v**
- Cycle between windows -\> **Ctrl** + **w**  + **w**
- Move to left window -\> **Ctrl** + **h** or **Ctrl** + **w** + **h**
- Move to right window -\> **Ctrl** + **l** or **Ctrl** + **w** + **l**
- Move to previous active window -\> **Ctrl** + **w** + **p**
- Swap windows positions -\> **Ctrl** + **w** + **r**
- Move windows directionally -\> **Ctrl** + **w** + **H/J/K/L**
- Close current window -\> **Ctrl + q** or **:hide**
- Close all windows except current one: -\> **:only**

### Tabs

- New tab -\> **:tabnew**
- Go to next tab -\> **gt**
- Go to previous tab -\> **gT**
- Go to tab number -\> **#gt**
- Go to first tab -\> **:tabr**
- Go to last tab -\> **:tabl**
- Move current tab to the last position -\> **:tabm**
- Move current tab to the # position -\> **:tabm #**
- Close tab -\> **:close**


## Customize Vim

You can customize Vim by editing `~/.vimrc`

```
let mapleader = " " 
set mouse=a
set tabstop=4
set shiftwidth=0
set noexpandtab
set autoindent
set number " enable line numbering
set relativenumber "enable relative line number
syntax enable " syntax highlighting enabled
set listchars=tab:»\ ,trail:·

" enable color themes "
if !has('gui_running')
	set t_Co=256
endif
" enable true colors support "
set termguicolors
" Vim colorscheme "
colorscheme zellner


" KEY MAPPINGS

nmap <leader>ee :vsplit<cr> :Explore<cr> " pressing ,ee will open Explore in a new vertical window
nmap <leader>zz :split $MYVIMRC<cr> " Pressing ,zz will open ~/.vimrc in a new window
nmap <leader>zx :source $MYVIMRC<cr> " Pressing ,zx will source the changes made in ~/.vimrc
vmap <buffer> ;bo "zdi<strong><c-r>z</strong><esc>
```

**Note**: For the changes to take affect, run this command inside Vim:

`:source $MYVIMRC`

  🎁 <b>Gift</b> <br/>
You can download a useful <i>~/.vimrc</i> file by running the command <em>wget https://raw.githubusercontent.com/cheomanigua/config/master/vimrc</em>


### Key mapping

- List keymaps: `:map`
- Remove keymap: `:unmap <keymap>`

### Snippet insertion

1. Create in `~/.vim/templates/` directory the text file containing the snippet
2. Add this lines in `~/.vimrc`:

```
function Rsf() 
 :read ~/.vim/templates/<textfile>
endfunction
```
3. Restart vim: `:so $MYVIMRC`
4. When you need to insert the snippet, type: `:call Rsf()`

**Note**: `Rsf()` is used as an example. You can name your function whatever you want.

<Message variant='important'>
  💡 <b>Tip</b> <br/>
	You can keymap <strong>:call Rsf()</strong> so you won't need to type in the full command.
</Message>

## Macros
```
[move the cursor where you want to start to record the macro]
$ qq
[do all the staff]
$ ESC
$ q
[move the cursor where you want to apply the macro]
$ @q
```

# 2. Plugin Managers


## 2.1 Vim 8

Vim comes with a built-in plugin manager called **Vim 8 packages**.

### Activating the plugin manager

1. First, create a directory structure representing a plugin-group, say `plugins`, as follows:

	```
	$ mkdir -p ~/.vim/pack/plugins/start/
	```

2. Activate the plugin manager:
	```
	$ sed -i -e '$a\'$'\n''filetype plugin indent on' ~/.vimrc
	```

### Installing a plugin

Clone (or alternatively, download the zip, and unzip) the plugin you want to install inside the `start` directory:

```
$ git clone https://github.com/repo/foo.git ~/.vim/pack/plugins/start/foo
```

### Generating helptags for a plugin
```
:helptags ~/.vim/pack/plugins/start/foo
```

### Upgrading a plugin
```
$ cd ~/.vim/pack/plugins/start/foo
$ git pull origin master
```

### Deleting a plugin
```
$ rm -r ~/.vim/pack/plugins/start/foo
```

### Example: installing Vim-go plugin

Vim-go is a plugin that facilitates the Golang programming experience in Vim. This is how to install using the Vim 8 plugin manager:

1. Add the plugin:

	```
	$ git clone https://github.com/fatih/vim-go.git ~/.vim/pack/plugins/start/vim-go
	```

2. Install all necessary binaries:

	```
	:GoInstallBinaries
	```

3. Generate the plugin's help tags:

	```
	:helptags ALL
	```

4. Code completion is enabled by default via `omnifunc`, which you can trigger
   with **Ctrl-X** + **Ctrl-O**


## 2.2 Vim-plug

1. Download <a href="https://github.com/junegunn/vim-plug" target="_blank">vim-plug</a>:

	`$ curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim`

2. Add **vim-plug** in your **~/.vimrc** file:
	```
	" vim-plug
	call plug#begin('~/.vim/plugged')

	" end vim-plug
	call plug#end()
	```

3. Add, for instance, <a href="https://github.com/MaxMEllon/vim-jsx-pretty" target="_blank">vim-jsx-pretty</a>, <a href="https://github.com/mattn/emmet-vim" target="_blank">emmet-vim</a> and <a href="https://github.com/neoclide/coc.nvim" target="_blank">Intellisense</a> plugins in your **~/.vimrc** file:
```
" vim-plug
call plug#begin('~/.vim/plugged')

Plug 'maxmellon/vim-jsx-pretty'
Plug 'mattn/emmet-vim'
Plug 'neoclide/coc.nvim', {'branch': 'release'}

" end vim-plug
call plug#end()
```

4. Save and exit. Launch Vim and run: `:PlugInstall`

That's it. This is how you install plugins in Vim with <a href="https://github.com/junegunn/vim-plug" target="_blank">vim-plug</a>.

You can download a custom [init.lua](https://drive.google.com/file/d/1LxVKnPmmdMlAu3UM1eAwM37DKdBN3MtK/view?usp=drive_link) config file with some basic configuration.

### Deleting a plugin

- Comment out or delete the plugin line in `.vimrc`
- Run: `:PlugClean`


# 3. NeoVim 

## 3.1 Install last version (Ubuntu)

```bash
$ sudo add-apt-repository ppa:neovim-ppa/unstable -y
$ sudo apt update
$ sudo apt install make gcc ripgrep unzip git xclip neovim
```
or
```bash
$ sudo apt-get install neovim
```

### Launch NeoVim

```bash
$ nvim
```

## 3.2 Configuration files

There are two mutually exclusive config files in NeoVim:

- `~/.config/nvim/init.vim`
- `~/.config/nvim/init.lua`

## 3.3  Plugin managers

There are several plugin managers for NeoVim. Below is a list of a few of them:

- [vim-plug](https://github.com/junegunn/vim-plug)
- [lazy.nvim](https://lazy.folke.io/)
- [pckr.nvim](https://github.com/lewis6991/pckr.nvim)
- [Packer.nvim](https://github.com/wbthomason/packer.nvim)

### 3.3.1. vim-plug

#### Installation

`sh -c 'curl -fLo "${XDG_DATA_HOME:-$HOME/.local/share}"/nvim/site/autoload/plug.vim --create-dirs \
       https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim'`

For installing plugins, follow the instructions explained above at **2.2. vim-plug**, but instead of editing the `~/.vimrc` file (Vim), edit the `~/.config/nvim/init.vim` file (NeoVim).

### 3.3.2. lazy.nvim

#### Installation

- Download lazy.vim starter:

`$ git clone https://github.com/LazyVim/starter.git`

- From the downloaded `starter` directory, copy to `~/.config/nvim/` the following: `init.lua`, `stylua.toml` and `lua/`

- Launch NeoVim. Lazy.vim will start installing and configuring plugins automatically.

#### Uninstalling

To uninstall lazy.nvim, you need to remove the following files and directories:

- data: `~/.local/share/nvim/lazy`
- state: `~/.local/state/nvim/lazy`
- lockfile: `~/.config/nvim/lazy-lock.json`

## 3.4 kickstart.nvim

Readme Page: [https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file](https://github.com/nvim-lua/kickstart.nvim?tab=readme-ov-file)

### Installation
```bash
$ git clone https://github.com/nvim-lua/kickstart.nvim.git "${XDG_CONFIG_HOME:-$HOME/.config}"/nvim
$ nvim
```

### Edit config file
Within NeoVim, type: `:e $MYVIMRC`

### Golang support
1. Edit the config file and uncomment this line:
	```lua
	-- gopls = {},
	```

2. Edit the config file and make these lines to look like this:
	```lua
		dependencies = {
		  -- `friendly-snippets` contains a variety of premade snippets.
		  --    See the README about individual language/framework/plugin snippets:
		  --    https://github.com/rafamadriz/friendly-snippets
		  {
		    'rafamadriz/friendly-snippets',
		    config = function()
		      require('luasnip.loaders.from_vscode').lazy_load { include = {"go", "html", "css"}}
		    end,
		  },
		},
	```

3. Edit the config file and add 'go' to this line:
	```lua
	ensure_installed = { 'go', 'bash', 'c', 'diff', 'html', 'lua', 'luadoc', 'markdown', 'markdown_inline', 'query', 'vim', 'vimdoc' },
	```
	Run `:TSUpdate`

4. Restart your computer

### Useful commands
- **Space** + **s** + **g** -\> Search for any word in the whole project
- **Space** + **s** + **w** -\> Search for the word where the cursor is on in the whole project
- **Space** + **s** + **f** -\> Search for files in the project
- **g** + **d** -\> Go to Definition of the function/variable where the cursor is on
- **g** + **D** -\> Go to Declaration of the function/variable where the cursor is on
- **g** + **I** -\> Go to Implementation of the function/variable where the cursor is on
- **Ctl** + **o** -\> Go back to previous window after going to Definition/Declaration/Implementation
- **:e .** -\> Open file explorer



# 4. Tmux

### Installation

```
$ sudo apt install tmux
```

### Color and mouse scroll configuration

```
$ touch tmux.conf
$ echo "set -g default-terminal \"screen-256color\"" >> tmux.conf
$ echo "set -g mouse on" >> tmux.conf
$ sudo chown root:root tmux.conf
$ sudo mv tmux.conf /etc
```


### Key bindings

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

---
title: "Vim / Neovim Complete Cheatsheet"
description: "A practical Vim and Neovim cheatsheet for daily coding, navigation, editing, buffers, windows, macros, search, LSP, terminal, and productivity workflows."
date: 2026-06-17
tags: ["vim", "neovim", "nvim", "cheatsheet", "developer-tools"]
url: /vim
draft: false
---

# Vim / Neovim Complete Cheatsheet

Vim and Neovim are modal editors. Instead of using the mouse for everything, you combine **modes**, **motions**, **operators**, and **text objects** to edit text quickly.

This cheatsheet is designed for daily development work and can be published directly as a blog post.

---

## Table of Contents

- [Core Idea](#core-idea)
- [Modes](#modes)
- [Basic Movement](#basic-movement)
- [Word Movement](#word-movement)
- [Line Movement](#line-movement)
- [Screen Movement](#screen-movement)
- [Search Movement](#search-movement)
- [Character Search: `f`, `F`, `t`, `T`](#character-search-f-f-t-t)
- [Operators](#operators)
- [Operator + Motion Examples](#operator--motion-examples)
- [Text Objects](#text-objects)
- [Insert Mode Shortcuts](#insert-mode-shortcuts)
- [Editing Text](#editing-text)
- [Copy, Cut, and Paste](#copy-cut-and-paste)
- [Registers](#registers)
- [Undo and Redo](#undo-and-redo)
- [Visual Mode](#visual-mode)
- [Indentation](#indentation)
- [Comments](#comments)
- [Search and Replace](#search-and-replace)
- [Global Command](#global-command)
- [Files](#files)
- [Buffers](#buffers)
- [Windows / Splits](#windows--splits)
- [Tabs](#tabs)
- [Marks](#marks)
- [Jumps and Change List](#jumps-and-change-list)
- [Macros](#macros)
- [Folds](#folds)
- [Quickfix and Location List](#quickfix-and-location-list)
- [Diff Mode](#diff-mode)
- [Spell Checking](#spell-checking)
- [Formatting](#formatting)
- [Terminal](#terminal)
- [Neovim LSP](#neovim-lsp)
- [Neovim Diagnostics](#neovim-diagnostics)
- [Useful Ex Commands](#useful-ex-commands)
- [Practical Workflows](#practical-workflows)
- [Recommended Practice Plan](#recommended-practice-plan)
- [Mini Cheat Table](#mini-cheat-table)

---

## Core Idea

Vim commands are usually built like this:

```text
operator + motion
```

Examples:

```vim
dw      " delete word
d$      " delete until end of line
ciw     " change inside word
yap     " yank around paragraph
```

You can also add a count:

```vim
3w      " move forward 3 words
d3w     " delete 3 words
5j      " move down 5 lines
```

The most important mindset:

> Do not memorize everything at once. Learn motions first, then operators, then text objects.

---

## Modes

| Mode | Meaning | How to Enter | How to Exit |
|---|---|---|---|
| Normal | Move and run commands | `Esc` | - |
| Insert | Type text | `i`, `a`, `o` | `Esc` |
| Visual | Select text | `v` | `Esc` |
| Visual Line | Select full lines | `V` | `Esc` |
| Visual Block | Select block/column | `Ctrl-v` | `Esc` |
| Command-line | Run Ex commands | `:` | `Enter` / `Esc` |
| Replace | Replace existing text | `R` | `Esc` |
| Terminal Normal | Control terminal buffer | `Ctrl-\ Ctrl-n` | - |

---

## Basic Movement

| Key | Action |
|---|---|
| `h` | Move left |
| `j` | Move down |
| `k` | Move up |
| `l` | Move right |
| `gj` | Move down by visual/wrapped line |
| `gk` | Move up by visual/wrapped line |

Use counts:

```vim
10j     " move down 10 lines
5l      " move right 5 characters
```

---

## Word Movement

| Key | Action |
|---|---|
| `w` | Jump to start of next word |
| `W` | Jump to start of next WORD, separated by whitespace |
| `e` | Jump to end of current/next word |
| `E` | Jump to end of current/next WORD |
| `b` | Jump backward to start of word |
| `B` | Jump backward to start of WORD |
| `ge` | Jump backward to end of previous word |
| `gE` | Jump backward to end of previous WORD |

Difference between `word` and `WORD`:

```text
hello.world example
```

- `w` treats punctuation as a separator.
- `W` treats only whitespace as a separator.

---

## Line Movement

| Key | Action |
|---|---|
| `0` | Start of line |
| `^` | First non-blank character |
| `$` | End of line |
| `g_` | Last non-blank character |
| `+` | First non-blank character of next line |
| `-` | First non-blank character of previous line |
| `gg` | Start of file |
| `G` | End of file |
| `42G` | Go to line 42 |
| `:42` | Go to line 42 |

---

## Screen Movement

| Key | Action |
|---|---|
| `Ctrl-d` | Move half page down |
| `Ctrl-u` | Move half page up |
| `Ctrl-f` | Move full page down |
| `Ctrl-b` | Move full page up |
| `zt` | Move current line to top |
| `zz` | Move current line to center |
| `zb` | Move current line to bottom |
| `H` | Move to top of screen |
| `M` | Move to middle of screen |
| `L` | Move to bottom of screen |

---

## Search Movement

| Key | Action |
|---|---|
| `/text` | Search forward for `text` |
| `?text` | Search backward for `text` |
| `n` | Next search result |
| `N` | Previous search result |
| `*` | Search word under cursor forward |
| `#` | Search word under cursor backward |
| `g*` | Search partial word under cursor forward |
| `g#` | Search partial word under cursor backward |

Useful options:

```vim
:set ignorecase      " case-insensitive search
:set smartcase       " case-sensitive if uppercase is used
:set hlsearch        " highlight search results
:set nohlsearch      " hide highlights
:noh                 " clear current highlight
```

---

## Character Search: `f`, `F`, `t`, `T`

Character search is very powerful for moving inside a line.

| Key | Action |
|---|---|
| `f<char>` | Move forward onto character |
| `F<char>` | Move backward onto character |
| `t<char>` | Move forward until before character |
| `T<char>` | Move backward until after character |
| `;` | Repeat latest `f`, `F`, `t`, or `T` |
| `,` | Repeat latest `f`, `F`, `t`, or `T` in reverse |

Example line:

```js
const user = { name: "Pian", age: 31 };
```

### Move to `{`

```vim
f{
```

This moves the cursor forward **onto** `{`.

### Move before `{`

```vim
t{
```

This moves the cursor forward **until before** `{`.

### Delete until before `{`

```vim
dt{
```

Before:

```js
const user = { name: "Pian", age: 31 };
```

After:

```js
{ name: "Pian", age: 31 };
```

### Delete including `{`

```vim
df{
```

This deletes from the cursor position through `{`.

### Change text inside braces

```vim
ci{
```

This changes the content inside `{ ... }`.

### Delete around braces

```vim
da{
```

This deletes the braces and their content.

---

## Operators

Operators perform an action. They usually need a motion or text object.

| Operator | Meaning |
|---|---|
| `d` | Delete / cut |
| `c` | Change / delete then enter Insert mode |
| `y` | Yank / copy |
| `v` | Visually select |
| `>` | Indent right |
| `<` | Indent left |
| `=` | Auto-format / reindent |
| `gq` | Format text |
| `gu` | Lowercase |
| `gU` | Uppercase |
| `~` | Toggle case |

Repeated operators usually apply to the current line:

| Command | Action |
|---|---|
| `dd` | Delete line |
| `cc` | Change line |
| `yy` | Yank line |
| `>>` | Indent line right |
| `<<` | Indent line left |
| `==` | Auto-indent line |
| `gUU` | Uppercase line |
| `guu` | Lowercase line |

---

## Operator + Motion Examples

| Command | Action |
|---|---|
| `dw` | Delete word |
| `dW` | Delete WORD |
| `de` | Delete until end of word |
| `db` | Delete backward to start of word |
| `d$` | Delete to end of line |
| `d0` | Delete to start of line |
| `d^` | Delete to first non-blank character |
| `dgg` | Delete from cursor to top of file |
| `dG` | Delete from cursor to end of file |
| `cw` | Change word |
| `c$` | Change to end of line |
| `ciw` | Change inside word |
| `ci"` | Change inside double quotes |
| `ci'` | Change inside single quotes |
| `ci(` | Change inside parentheses |
| `ci{` | Change inside braces |
| `y$` | Yank to end of line |
| `yG` | Yank from cursor to end of file |
| `gUiw` | Uppercase current word |
| `guw` | Lowercase next word |

---

## Text Objects

Text objects let you work with meaningful structures.

```text
operator + i/a + object
```

- `i` means **inside**.
- `a` means **around**, including surrounding characters or whitespace.

| Text Object | Meaning |
|---|---|
| `iw` | Inside word |
| `aw` | Around word |
| `is` | Inside sentence |
| `as` | Around sentence |
| `ip` | Inside paragraph |
| `ap` | Around paragraph |
| `i"` | Inside double quotes |
| `a"` | Around double quotes |
| `i'` | Inside single quotes |
| `a'` | Around single quotes |
| `` i` `` | Inside backticks |
| `` a` `` | Around backticks |
| `i(` or `ib` | Inside parentheses |
| `a(` or `ab` | Around parentheses |
| `i[` | Inside square brackets |
| `a[` | Around square brackets |
| `i{` or `iB` | Inside braces |
| `a{` or `aB` | Around braces |
| `it` | Inside HTML/XML tag |
| `at` | Around HTML/XML tag |

Examples:

```vim
ciw     " change inside word
daw     " delete around word
yi"     " copy inside double quotes
da"     " delete around double quotes
ci{     " change inside braces
ya(     " copy around parentheses
vit     " visually select inside HTML tag
```

Example:

```js
const message = "Hello world";
```

Run:

```vim
ci"
```

Result:

```js
const message = "|";
```

The cursor enters Insert mode between the quotes.

---

## Insert Mode Shortcuts

| Key | Action |
|---|---|
| `i` | Insert before cursor |
| `I` | Insert at first non-blank character |
| `a` | Append after cursor |
| `A` | Append at end of line |
| `o` | Open new line below |
| `O` | Open new line above |
| `s` | Delete character and insert |
| `S` | Delete line and insert |
| `C` | Change to end of line |
| `r<char>` | Replace one character |
| `R` | Enter Replace mode |

Inside Insert mode:

| Key | Action |
|---|---|
| `Ctrl-h` | Backspace |
| `Ctrl-w` | Delete previous word |
| `Ctrl-u` | Delete to start of line |
| `Ctrl-o` | Run one Normal mode command, then return to Insert mode |
| `Ctrl-r <register>` | Insert content from register |
| `Ctrl-n` | Next completion candidate |
| `Ctrl-p` | Previous completion candidate |

Example:

```vim
Ctrl-o zz
```

While in Insert mode, this centers the current line and returns to Insert mode.

---

## Editing Text

| Command | Action |
|---|---|
| `x` | Delete character under cursor |
| `X` | Delete character before cursor |
| `s` | Substitute character and enter Insert mode |
| `S` | Substitute entire line |
| `r<char>` | Replace one character |
| `J` | Join current line with next line |
| `gJ` | Join lines without adding space |
| `.` | Repeat last change |
| `u` | Undo |
| `Ctrl-r` | Redo |

Examples:

```vim
ciwhello<Esc>   " replace current word with hello
A;<Esc>         " add semicolon at end of line
J               " join next line into current line
```

---

## Copy, Cut, and Paste

| Command | Action |
|---|---|
| `y` | Yank / copy with motion |
| `yy` | Yank current line |
| `Y` | Yank to end of line in many Vim configs; in modern Vim/Neovim usually same as `yy` unless remapped |
| `d` | Delete / cut with motion |
| `dd` | Delete current line |
| `p` | Paste after cursor or below line |
| `P` | Paste before cursor or above line |
| `gp` | Paste and move cursor after pasted text |
| `gP` | Paste before and move cursor after pasted text |

Examples:

```vim
yy      " copy line
3yy     " copy 3 lines
dd      " cut line
3dd     " cut 3 lines
p       " paste below
P       " paste above
```

---

## Registers

Registers are storage locations for copied/deleted text.

| Register | Meaning |
|---|---|
| `"` | Default unnamed register |
| `0` | Last yank register |
| `1`-`9` | Delete history registers |
| `+` | System clipboard |
| `*` | Selection clipboard on some systems |
| `_` | Black hole register; delete without saving |
| `%` | Current file name |
| `.` | Last inserted text |
| `:` | Last command-line command |

Use registers:

```vim
"+y     " yank to system clipboard
"+p     " paste from system clipboard
"_d     " delete without affecting default register
"0p     " paste last yanked text
```

Practical examples:

```vim
"+yy    " copy current line to system clipboard
"_dd    " delete line without replacing your copied text
"0p     " paste last yanked value, not last deleted value
```

---

## Undo and Redo

| Command | Action |
|---|---|
| `u` | Undo |
| `Ctrl-r` | Redo |
| `U` | Restore current line to original state before cursor moved away |
| `:earlier 5m` | Go to file state from 5 minutes earlier |
| `:later 5m` | Go to file state from 5 minutes later |

Persistent undo in config:

```vim
set undofile
```

Neovim Lua:

```lua
vim.opt.undofile = true
```

---

## Visual Mode

| Command | Action |
|---|---|
| `v` | Start character-wise visual selection |
| `V` | Start line-wise visual selection |
| `Ctrl-v` | Start block-wise visual selection |
| `o` | Move cursor to other end of selection |
| `gv` | Reselect latest visual selection |
| `y` | Yank selected text |
| `d` | Delete selected text |
| `c` | Change selected text |
| `>` | Indent selected text |
| `<` | Unindent selected text |
| `=` | Auto-indent selected text |

Visual block examples:

```vim
Ctrl-v
j/j/k/k to select lines
I// <Esc>
```

This inserts `//` at the beginning of multiple selected lines.

Append to multiple lines:

```vim
Ctrl-v
select lines
A; <Esc>
```

This appends `;` to multiple selected lines.

---

## Indentation

| Command | Action |
|---|---|
| `>>` | Indent current line right |
| `<<` | Indent current line left |
| `>}` | Indent until next paragraph |
| `<}` | Unindent until next paragraph |
| `=}` | Auto-indent until next paragraph |
| `gg=G` | Auto-indent whole file |

In Visual mode:

```vim
>       " indent selection
<       " unindent selection
=       " auto-indent selection
```

---

## Comments

Vim does not have one universal built-in comment shortcut for all languages by default. Common approaches:

### Built-in manual way

```vim
I// <Esc>       " add // at start of line
```

For multiple lines:

```vim
Ctrl-v
select lines
I// <Esc>
```

### With popular plugins

Many Neovim users use comment plugins such as `numToStr/Comment.nvim` or similar.

Common plugin mappings:

| Command | Action |
|---|---|
| `gcc` | Toggle comment line |
| `gc` in Visual mode | Toggle comment selection |
| `gc{motion}` | Toggle comment by motion |

Examples:

```vim
gcc     " comment/uncomment current line
gcip    " comment/uncomment inside paragraph
```

---

## Search and Replace

Basic substitute:

```vim
:s/old/new/
```

Replace first match on current line.

Replace all matches on current line:

```vim
:s/old/new/g
```

Replace in whole file:

```vim
:%s/old/new/g
```

Replace with confirmation:

```vim
:%s/old/new/gc
```

Replace only selected lines:

```vim
:'<,'>s/old/new/g
```

Common flags:

| Flag | Meaning |
|---|---|
| `g` | Replace all matches in each line |
| `c` | Confirm each replacement |
| `i` | Ignore case |
| `I` | Case-sensitive |
| `n` | Count matches, do not replace |

Examples:

```vim
:%s/console.log/logger.info/g
:%s/\<user\>/customer/gc
:%s/foo/bar/gn
```

Special replacement symbols:

| Symbol | Meaning |
|---|---|
| `&` | Whole matched text |
| `\1`, `\2` | Capture groups |
| `\r` | New line in replacement |
| `\=` | Expression replacement |

Example with capture group:

```vim
:%s/\(first\)_\(name\)/\1Name/g
```

---

## Global Command

The global command runs an Ex command on all matching lines.

```vim
:g/pattern/command
```

Examples:

```vim
:g/TODO/p          " print all lines containing TODO
:g/TODO/d          " delete all lines containing TODO
:g/^$/d            " delete empty lines
:g/import/s/foo/bar/g
:v/TODO/d          " delete all lines that do NOT contain TODO
```

Useful pattern examples:

```vim
:g/^import/normal A;      " add semicolon to all import lines
:g/console.log/d          " delete lines containing console.log
:g/TODO/t$                " copy TODO lines to end of file
```

---

## Files

| Command | Action |
|---|---|
| `:e file` | Edit/open file |
| `:edit file` | Edit/open file |
| `:w` | Save |
| `:w file` | Save as file |
| `:wa` | Save all buffers |
| `:q` | Quit |
| `:q!` | Quit without saving |
| `:wq` | Save and quit |
| `:x` | Save and quit if changed |
| `ZZ` | Save and quit |
| `ZQ` | Quit without saving |
| `:r file` | Read file content into current buffer |
| `:r !cmd` | Insert command output into current buffer |

Examples:

```vim
:e src/index.ts
:w
:q
:wq
:r package.json
:r !date
```

---

## Buffers

A buffer is an open file in memory.

| Command | Action |
|---|---|
| `:ls` or `:buffers` | List buffers |
| `:bnext` or `:bn` | Next buffer |
| `:bprevious` or `:bp` | Previous buffer |
| `:buffer 3` or `:b 3` | Go to buffer number 3 |
| `:b filename` | Go to buffer by name |
| `:bd` | Delete/close buffer |
| `:bd!` | Force delete buffer |
| `:bufdo command` | Run command on all buffers |

Useful mappings you may add:

```vim
nnoremap <leader>bn :bnext<CR>
nnoremap <leader>bp :bprevious<CR>
nnoremap <leader>bd :bd<CR>
```

Neovim Lua:

```lua
vim.keymap.set("n", "<leader>bn", ":bnext<CR>")
vim.keymap.set("n", "<leader>bp", ":bprevious<CR>")
vim.keymap.set("n", "<leader>bd", ":bd<CR>")
```

---

## Windows / Splits

A window is a view into a buffer.

| Command | Action |
|---|---|
| `:split` or `:sp` | Horizontal split |
| `:vsplit` or `:vsp` | Vertical split |
| `Ctrl-w s` | Horizontal split |
| `Ctrl-w v` | Vertical split |
| `Ctrl-w h` | Move to left split |
| `Ctrl-w j` | Move to lower split |
| `Ctrl-w k` | Move to upper split |
| `Ctrl-w l` | Move to right split |
| `Ctrl-w w` | Cycle windows |
| `Ctrl-w q` | Close current window |
| `Ctrl-w o` | Close other windows |
| `Ctrl-w =` | Equalize split sizes |
| `Ctrl-w _` | Maximize height |
| `Ctrl-w |` | Maximize width |
| `Ctrl-w +` | Increase height |
| `Ctrl-w -` | Decrease height |
| `Ctrl-w >` | Increase width |
| `Ctrl-w <` | Decrease width |

Open file in split:

```vim
:sp src/index.ts
:vsp src/index.ts
```

---

## Tabs

In Vim, tabs are layouts of windows, not file tabs like many IDEs.

| Command | Action |
|---|---|
| `:tabnew` | New tab |
| `:tabnew file` | Open file in new tab |
| `:tabnext` or `gt` | Next tab |
| `:tabprevious` or `gT` | Previous tab |
| `:tabclose` | Close current tab |
| `:tabonly` | Close other tabs |
| `:tabs` | List tabs |

---

## Marks

Marks let you jump back to important positions.

| Command | Action |
|---|---|
| `ma` | Set local mark `a` |
| `` `a `` | Jump to exact position of mark `a` |
| `'a` | Jump to line of mark `a` |
| `mA` | Set global mark `A` |
| `` `A `` | Jump to global mark `A` |
| `:marks` | List marks |
| `` `. `` | Jump to last change |
| `` `" `` | Jump to last position in file |
| `` `[ `` | Start of last changed/yanked text |
| `` `] `` | End of last changed/yanked text |

Practical use:

```vim
ma      " save current position as mark a
G       " go to bottom of file
`a      " jump back to exact saved position
```

---

## Jumps and Change List

| Command | Action |
|---|---|
| `Ctrl-o` | Jump backward in jump list |
| `Ctrl-i` | Jump forward in jump list |
| `:jumps` | Show jump list |
| `g;` | Go to previous change |
| `g,` | Go to next change |
| `:changes` | Show change list |

Useful after searching, jumping to definitions, or moving across files.

---

## Macros

Macros record and replay actions.

| Command | Action |
|---|---|
| `qa` | Start recording into register `a` |
| `q` | Stop recording |
| `@a` | Play macro from register `a` |
| `@@` | Repeat latest macro |
| `10@a` | Run macro `a` ten times |

Example: add semicolon to multiple lines.

1. Put cursor on first line.
2. Record macro:

```vim
qaA;<Esc>jq
```

3. Replay 10 times:

```vim
10@a
```

Explanation:

| Part | Meaning |
|---|---|
| `qa` | Record into register `a` |
| `A;` | Append `;` at end of line |
| `<Esc>` | Return to Normal mode |
| `j` | Move to next line |
| `q` | Stop recording |

---

## Folds

Folds hide sections of text.

| Command | Action |
|---|---|
| `za` | Toggle fold |
| `zo` | Open fold |
| `zc` | Close fold |
| `zR` | Open all folds |
| `zM` | Close all folds |
| `zj` | Move to next fold |
| `zk` | Move to previous fold |

Fold methods:

```vim
:set foldmethod=indent
:set foldmethod=syntax
:set foldmethod=marker
```

Marker example:

```js
// {{{ Auth helpers
function login() {}
function logout() {}
// }}}
```

---

## Quickfix and Location List

Quickfix is useful for project-wide errors, grep results, and compiler output.

| Command | Action |
|---|---|
| `:copen` | Open quickfix list |
| `:cclose` | Close quickfix list |
| `:cnext` or `:cn` | Next quickfix item |
| `:cprevious` or `:cp` | Previous quickfix item |
| `:cfirst` | First quickfix item |
| `:clast` | Last quickfix item |
| `:colder` | Older quickfix list |
| `:cnewer` | Newer quickfix list |

Location list is window-local:

| Command | Action |
|---|---|
| `:lopen` | Open location list |
| `:lclose` | Close location list |
| `:lnext` | Next location item |
| `:lprevious` | Previous location item |

Search project with built-in grep:

```vim
:grep TODO **/*.ts
:copen
```

If using ripgrep:

```vim
:set grepprg=rg\ --vimgrep
:grep TODO
:copen
```

---

## Diff Mode

| Command | Action |
|---|---|
| `vimdiff file1 file2` | Open files in diff mode |
| `:diffsplit file` | Diff current file with another file |
| `]c` | Next change |
| `[c` | Previous change |
| `do` | Diff obtain: get change from other window |
| `dp` | Diff put: send change to other window |
| `:diffupdate` | Refresh diff |
| `:diffoff` | Turn off diff mode |

---

## Spell Checking

| Command | Action |
|---|---|
| `:set spell` | Enable spell check |
| `:set nospell` | Disable spell check |
| `]s` | Next spelling mistake |
| `[s` | Previous spelling mistake |
| `z=` | Show suggestions |
| `zg` | Add word to dictionary |
| `zw` | Mark word as wrong |
| `zug` | Undo `zg` |

Set language:

```vim
:set spelllang=en_us
```

---

## Formatting

| Command | Action |
|---|---|
| `=` | Auto-indent operator |
| `==` | Auto-indent current line |
| `gg=G` | Auto-indent whole file |
| `gq` | Format text to text width |
| `gqap` | Format around paragraph |

Set text width:

```vim
:set textwidth=80
```

Format selected text:

```vim
v select text
=
```

Neovim LSP formatting:

```vim
:lua vim.lsp.buf.format()
```

Recommended mapping:

```lua
vim.keymap.set("n", "<leader>f", function()
  vim.lsp.buf.format({ async = true })
end)
```

---

## Terminal

### Vim / Neovim terminal

| Command | Action |
|---|---|
| `:terminal` | Open terminal |
| `:term` | Open terminal |
| `i` | Enter terminal input mode |
| `Ctrl-\ Ctrl-n` | Leave terminal input mode |
| `:bd!` | Close terminal buffer |

Open terminal in split:

```vim
:split | terminal
:vsplit | terminal
```

Neovim Lua mapping example:

```lua
vim.keymap.set("t", "<Esc>", [[<C-\><C-n>]])
```

---

## Neovim LSP

Neovim has built-in LSP support. Your exact keymaps may depend on your config, but these are common actions.

| Action | Lua Command |
|---|---|
| Go to definition | `vim.lsp.buf.definition()` |
| Go to declaration | `vim.lsp.buf.declaration()` |
| Go to implementation | `vim.lsp.buf.implementation()` |
| Go to type definition | `vim.lsp.buf.type_definition()` |
| Show hover docs | `vim.lsp.buf.hover()` |
| Signature help | `vim.lsp.buf.signature_help()` |
| Rename symbol | `vim.lsp.buf.rename()` |
| Code action | `vim.lsp.buf.code_action()` |
| Format | `vim.lsp.buf.format()` |
| References | `vim.lsp.buf.references()` |

Common keymaps:

```lua
vim.keymap.set("n", "gd", vim.lsp.buf.definition)
vim.keymap.set("n", "gD", vim.lsp.buf.declaration)
vim.keymap.set("n", "gi", vim.lsp.buf.implementation)
vim.keymap.set("n", "gr", vim.lsp.buf.references)
vim.keymap.set("n", "K", vim.lsp.buf.hover)
vim.keymap.set("n", "<leader>rn", vim.lsp.buf.rename)
vim.keymap.set("n", "<leader>ca", vim.lsp.buf.code_action)
vim.keymap.set("n", "<leader>f", function()
  vim.lsp.buf.format({ async = true })
end)
```

---

## Neovim Diagnostics

| Action | Lua Command |
|---|---|
| Open diagnostic float | `vim.diagnostic.open_float()` |
| Go to next diagnostic | `vim.diagnostic.goto_next()` |
| Go to previous diagnostic | `vim.diagnostic.goto_prev()` |
| Send diagnostics to location list | `vim.diagnostic.setloclist()` |

Common keymaps:

```lua
vim.keymap.set("n", "<leader>e", vim.diagnostic.open_float)
vim.keymap.set("n", "[d", vim.diagnostic.goto_prev)
vim.keymap.set("n", "]d", vim.diagnostic.goto_next)
vim.keymap.set("n", "<leader>q", vim.diagnostic.setloclist)
```

---

## Useful Ex Commands

| Command | Action |
|---|---|
| `:set number` | Show line numbers |
| `:set relativenumber` | Show relative line numbers |
| `:set nonumber` | Hide line numbers |
| `:set wrap` | Enable line wrap |
| `:set nowrap` | Disable line wrap |
| `:set list` | Show invisible characters |
| `:set nolist` | Hide invisible characters |
| `:set paste` | Enable paste mode in older Vim workflows |
| `:set nopaste` | Disable paste mode |
| `:syntax on` | Enable syntax highlighting |
| `:filetype plugin indent on` | Enable filetype plugins and indent |
| `:checkhealth` | Neovim health check |
| `:messages` | Show messages |
| `:version` | Show version info |
| `:h topic` | Open help for topic |
| `:h key-notation` | Help for key notation |

Help examples:

```vim
:h motion.txt
:h text-objects
:h registers
:h :substitute
:h vim.lsp.buf
```

---

## Practical Workflows

### 1. Rename a variable in one file

```vim
:%s/oldName/newName/gc
```

Use `c` so you can confirm each replacement.

---

### 2. Delete all debug logs

```vim
:g/console.log/d
```

---

### 3. Copy text inside quotes

```vim
yi"
```

---

### 4. Replace text inside braces

```vim
ci{
```

---

### 5. Move to a character quickly

```vim
f{
```

Move forward to `{` on the current line.

```vim
t{
```

Move forward until before `{`.

---

### 6. Delete function arguments

Example:

```js
sendEmail(user, subject, body)
```

Put cursor inside the parentheses and run:

```vim
ci(
```

Result:

```js
sendEmail()
```

Now you are in Insert mode inside the parentheses.

---

### 7. Select inside HTML tag

```html
<div class="card">
  Hello world
</div>
```

Put cursor inside the tag content and run:

```vim
vit
```

Then you can use:

```vim
d       " delete selection
c       " change selection
y       " copy selection
```

---

### 8. Format whole file

```vim
gg=G
```

For Neovim LSP formatting:

```vim
:lua vim.lsp.buf.format()
```

---

### 9. Record repeated edit with macro

```vim
qaA;<Esc>jq
10@a
```

This adds a semicolon to 10 lines.

---

### 10. Delete without replacing clipboard

```vim
"_dd
```

This deletes the line into the black hole register.

---

## Recommended Practice Plan

### Day 1: Movement

Practice:

```vim
h j k l
w b e
0 ^ $
gg G
Ctrl-d Ctrl-u
```

Goal: move without arrow keys.

---

### Day 2: Operators

Practice:

```vim
dw
dd
cw
ciw
yy
p
u
Ctrl-r
```

Goal: edit using operator + motion.

---

### Day 3: Character Search

Practice:

```vim
f{
t{
f,
t)
;
,
```

Goal: move quickly inside a line.

---

### Day 4: Text Objects

Practice:

```vim
ci"
ci'
ci(
ci{
dap
yiw
```

Goal: edit meaningful blocks, not individual characters.

---

### Day 5: Buffers, Splits, and Search

Practice:

```vim
:e file
:ls
:bn
:bp
:vsp
Ctrl-w h/j/k/l
/text
n
N
```

Goal: navigate files like a real development workflow.

---

### Day 6: Search and Replace

Practice:

```vim
:%s/old/new/gc
:g/pattern/d
:noh
```

Goal: perform project cleanup faster.

---

### Day 7: Macros and Registers

Practice:

```vim
qa ... q
@a
@@
"+y
"+p
"_d
```

Goal: automate repetitive editing.

---

## Mini Cheat Table

| Goal | Command |
|---|---|
| Save | `:w` |
| Quit | `:q` |
| Save and quit | `:wq` |
| Force quit | `:q!` |
| Move to next word | `w` |
| Move to previous word | `b` |
| Move to end of line | `$` |
| Move to start of line | `0` |
| Move to first non-blank | `^` |
| Go to top of file | `gg` |
| Go to bottom of file | `G` |
| Search | `/text` |
| Next search result | `n` |
| Previous search result | `N` |
| Clear search highlight | `:noh` |
| Move to `{` | `f{` |
| Move before `{` | `t{` |
| Delete word | `dw` |
| Change word | `cw` |
| Change inside word | `ciw` |
| Change inside quotes | `ci"` |
| Change inside braces | `ci{` |
| Delete line | `dd` |
| Copy line | `yy` |
| Paste below | `p` |
| Paste above | `P` |
| Undo | `u` |
| Redo | `Ctrl-r` |
| Visual select | `v` |
| Visual line | `V` |
| Visual block | `Ctrl-v` |
| Open file | `:e file` |
| List buffers | `:ls` |
| Next buffer | `:bn` |
| Previous buffer | `:bp` |
| Vertical split | `:vsp` |
| Horizontal split | `:sp` |
| Move between splits | `Ctrl-w h/j/k/l` |
| Record macro | `qa` |
| Stop macro | `q` |
| Play macro | `@a` |
| Repeat latest change | `.` |

---

## Final Notes

The commands that give the biggest productivity improvement are:

```vim
f{      " move to character
ciw     " change inside word
ci"     " change inside quotes
ci{     " change inside braces
.       " repeat last change
:%s     " search and replace
:g      " run command on matching lines
qa/@a   " record and replay macro
```

If you are still new to Vim or Neovim, start with this simple daily rule:

> Every time you reach for the mouse or arrow keys, pause and try to find the Vim motion for that action.


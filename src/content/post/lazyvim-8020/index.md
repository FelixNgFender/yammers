---
title: "LazyVim 8020: 80% Features from 20% of Keybindings"
description: "A list of LazyVim keybinds ranked by usefulness after the basics."
publishDate: "3 April 2026"
coverImage:
  src: "lazyvim.png"
  alt: "LazyVim logo"
ogImage: "lazyvim.png"
tags: ["lazyvim", "neovim"]
---

After using LazyVim for a while, you will realize most keybindings never get
used. Here's what actually matters.

:::note

Take it one step at a time. No need to memorize everything at once. Just pick a
few that seem useful and practice them until they become second nature. Then add
more as you go.

:::

## Navigation

Jumping around:

- `*`/`#` - search word under cursor forward/backward
- `<C-o>`/`<C-i>` - jump backward/forward in history (lifesaver when combined
  with `gg`/`gd`)
- `{`/`}` - jump by paragraph
- `(`/`)` - jump by sentence
- `%` - jump between matching brackets
- `<C-^>` - toggle between last two files

Keep your cursor centered while scrolling (`<C-d>`, `<C-u>`, `n`, `N`) and
jumping through search results (`n`, `N`) to avoid losing context:

```lua
-- `~/.config/nvim/lua/config/keymaps.lua`
vim.keymap.set("n", "<C-d>", "<C-d>zz", { noremap = true })
vim.keymap.set("n", "<C-u>", "<C-u>zz", { noremap = true })
vim.keymap.set("n", "n", "nzzzv", { noremap = true })
vim.keymap.set("n", "N", "Nzzzv", { noremap = true })
```

Reposition screen without moving cursor:

- `zt`/`zz`/`zb` - cursor to top/middle/bottom

## `flash.nvim`

This plugin changes everything. Press `s`, type what you want to jump to, hit
enter. Works across splits, multiple lines, whatever's on screen. If there are
multiple matches, just type the label character.

Same goes for `f`/`F` (find) and `t`/`T` (to). `flash.nvim` makes them work
across lines.

## Editing

The basics:

- `.` - repeat last action
- `J` - join lines
- `gi` - jump to last insert position and enter insert mode
- `ge` - go to end of previous word
- `<C-a>`/`<C-x>` - increment/decrement number

Block insert is underrated:

1. `<C-v>` to enter visual block mode
1. Select what you want
1. `I` or `c` to insert/change
1. Type your text
1. Hit `<Esc>` twice

Macros when you need them. The second `q` can be any letter to indicate the
buffer you are recording the macro into:

- `qq` to start recording
- Do your thing
- `q` to stop
- `@q`/`Q` to replay

## Text objects

The pattern is `<verb><context><object>`:

- Context: `i` (inside) or `a` (around)
- Objects: `w` word, `s` sentence, `p` paragraph, `"'` quotes, `()[]{}`
  brackets, `c/f/o` class/function/block, `t` tag, `i` indentation

Examples: `diw` (delete inside word), `ca"` (change around quotes), `vip`
(select inside paragraph)

Special mention for `gn`: selects next search match. Great for targeted
find-and-replace.

Use `S` for Treesitter mode. Figuring out what it does is part of the fun. Hint:
practice `flash.nvim` first.

## Search (`<Space>s`)

- `<Space>sg`/`<Space>sG` - grep in project/cwd
- `<Space>sk` - search keymaps
- `<Space>ss`/`<Space>sS` - search symbols (document/workspace)
- `<Space>st` - search todos
- `<Space>sb` - search current buffer
- `<Space>sr` - search and replace
- `<Space>sR` - resume last search
- `<Space>sn` - search notification history

## Code (`<Space>c`)

- `<Space>ca` - code actions
- `<Space>cr` - rename
- `<Space>cf` - format

LSP shortcuts:

- `gd` - go to definition
- `gr` - go to references
- `K` - hover docs

## Jump patterns

The `[`/`]` prefix is your friend:

- `[b`/`]b` - previous/next buffer
- `[d`/`]d` - diagnostic
- `[w`/`]w` - warning
- `[e`/`]e` - error
- `[m`/`]m` - method start
- `[M`/`]M` - method end
- `[q`/`]q` - quickfix/trouble item
- `[t`/`]t` - todo comment
- `[h`/`]h` - git hunk

## Miscellaneous

- `<Space>fc` - find config files
- `<Space>g` - git/source control
- `<Space>d` - debug (with `dap.core` extra)
- `<Space>t` - test (with `test.core` extra)
- `<Space>a` - AI features
- `gv` - reselect last visual selection. Useful with `gvy`

Enable `ui.treesitter-context` to pin current function/class at the top of the
screen.

That's it. Hope you pick up a few new tricks.

# Kaustav's neovim config based on kickstart.nvim

## Installation

1. Install [Neovim](https://github.com/neovim/neovim/blob/master/INSTALL.md) 0.12+ (check with `nvim --version`).
2. Install dependencies:
   - `git`, `make`, `curl`, `tar`, `unzip`, C compiler
   - [ripgrep](https://github.com/BurntSushi/ripgrep#installation), [fd](https://github.com/sharkdp/fd#installation)
   - [tree-sitter CLI](https://github.com/tree-sitter/tree-sitter/blob/master/crates/cli/README.md) 0.26.1+ (via package manager, not npm)
   - Clipboard provider (Linux: `wl-clipboard` / `xclip` / `xsel`; Windows: `win32yank`). See `:h clipboard`.
   - [Nerd Font](https://www.nerdfonts.com/), set as the terminal font
   - Language toolchains (Mason uses them to install the LSP servers):
     - Rust: [rustup](https://rustup.rs/), then `rustup component add rust-src rustfmt`
     - TypeScript: [Node.js](https://nodejs.org/) with `npm`
     - Go: [Go](https://go.dev/doc/install)
     - Python (optional): `python3`, plus [Node.js](https://nodejs.org/) with `npm` for `pyright`
3. Back up any existing Neovim config and data:
   ```sh
   for d in ~/.config/nvim ~/.local/share/nvim ~/.local/state/nvim ~/.cache/nvim; do
     [ -e "$d" ] && mv "$d" "$d.bak"
   done
   ```
4. Clone this repo (Windows: `%LOCALAPPDATA%\nvim`):
   ```sh
   git clone git@github.com:kaustavdm/nvim-kickstart.git ~/.config/nvim
   ```
5. Python (optional): uncomment `pyright = {}` in `servers` in `init.lua`.
6. Start `nvim` and confirm the plugin install prompt. Plugins install at the revisions in `nvim-pack-lock.json`.
7. Wait for Mason to install `rust-analyzer`, `tsc`, `gopls`, `lua-language-server`, `stylua` (and `pyright`). Track with `:Mason`.
8. Restart with `:restart`.
9. Open a `.rs`, `.ts`, `.go` (and `.py`) file once so their treesitter parsers install.
10. Run `:checkhealth` and fix reported issues. Check attached servers with `:checkhealth vim.lsp`.

### Updating

- Pull config changes: `git -C ~/.config/nvim pull`, then `:restart`.
- Sync plugins to the lockfile: `:lua vim.pack.update(nil, { target = 'lockfile' })`, then `:write` to confirm.
- Update plugins: `:lua vim.pack.update()`, then `:write` to confirm. Commit `nvim-pack-lock.json`.
- Update Mason tools: `:MasonToolsUpdate`.

---

## Keybindings

- `<leader>` and `<localleader>` are `<Space>`
- Mode:
    - `n` normal
    - `i` insert
    - `v` visual (char/line/block)
    - `x` visual (no select)
    - `o` operator-pending
    - `c` command-line
    - `t` terminal
- Entries marked *(buf)* are buffer-local (only when the relevant LSP / plugin is attached).
- `<leader>sk` searches all active keymaps live.

### Movement

#### Cursor

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Left / down / up / right | `h` `j` `k` `l` | n, v, o |
| Down / up by display line (wrapped) | `gj` / `gk` | n, v, o |
| Next / previous word start | `w` / `b` | n, v, o |
| Next / previous WORD start | `W` / `B` | n, v, o |
| Word end / previous word end | `e` / `ge` | n, v, o |
| WORD end / previous WORD end | `E` / `gE` | n, v, o |
| Start of line | `0` | n, v, o |
| First non-blank of line | `^` | n, v, o |
| End of line | `$` | n, v, o |
| Last non-blank of line | `g_` | n, v, o |
| First non-blank, line down / up | `+` / `-` | n, v, o |
| To column N | `N\|` | n, v, o |
| Find char forward / backward in line | `f{c}` / `F{c}` | n, v, o |
| Till char forward / backward in line | `t{c}` / `T{c}` | n, v, o |
| Repeat last `f/F/t/T` / reverse | `;` / `,` | n, v, o |
| Next / previous paragraph | `}` / `{` | n, v, o |
| Next / previous sentence | `)` / `(` | n, v, o |
| Matching bracket | `%` | n, v, o |
| Go to first line / line N | `gg` / `{N}G` | n, v, o |
| Go to last line | `G` | n, v, o |
| Go to N% of file | `{N}%` | n, v, o |
| Top / middle / bottom of window | `H` / `M` / `L` | n, v, o |
| Next / previous unmatched `(` `{` | `])` `]}` / `[(` `[{` | n, v, o |
| Next / previous method start | `]m` / `[m` | n, v, o |
| Next / previous method end | `]M` / `[M` | n, v, o |
| Next / previous section start | `]]` / `[[` | n, v, o |
| Next / previous section end | `][` / `[]` | n, v, o |
| Next / previous misspelled word | `]s` / `[s` | n, v, o |
| Go to definition of local var | `gd` | n |
| Go to definition of global var | `gD` | n |
| Go to file under cursor | `gf` | n |
| Go to last insert position and insert | `gi` | n |
| Go to byte N | `{N}go` | n |

#### Scrolling

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Half page down / up | `<C-d>` / `<C-u>` | n, v |
| Page down / up | `<C-f>` / `<C-b>` | n, v |
| Line down / up | `<C-e>` / `<C-y>` | n, v |
| Cursor line to center / top / bottom | `zz` / `zt` / `zb` | n |
| Center / top / bottom, first non-blank | `z.` / `z<CR>` / `z-` | n |

> `scrolloff` is 10, so the cursor stays 10 lines from the window edge.

#### Jumps, marks, changes

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Older / newer jump-list position | `<C-o>` / `<C-i>` | n |
| Older / newer tag-stack position | `<C-t>` / `<C-]>` | n |
| Older / newer change position | `g;` / `g,` | n |
| Jump to previous cursor position | ` `` ` / `''` | n |
| Set mark `{a-zA-Z}` | `m{a-zA-Z}` | n |
| Jump to mark (exact / line) | `` `{m} `` / `'{m}` | n, v, o |
| Jump to last edit / last insert | `` `. `` / `` `^ `` | n |
| Jump to start / end of last selection | `` `< `` / `` `> `` | n |

#### Text objects

Use after an operator (`d`, `c`, `y`, `v`, …). `i` = inside, `a` = around.

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Word / WORD | `iw` `aw` / `iW` `aW` | o, x |
| Sentence / paragraph | `is` `as` / `ip` `ap` | o, x |
| Parentheses | `i(` `a(` / `i)` `a)` | o, x |
| Braces | `i{` `a{` / `iB` `aB` | o, x |
| Brackets | `i[` `a[` | o, x |
| Angle brackets | `i<` `a<` | o, x |
| Any bracket `()` `[]` `{}` (mini.ai) | `ib` `ab` | o, x |
| Quotes (`"` `'` `` ` ``) | `i"` `a"` `i'` `a'` ``i` `` ``a` `` | o, x |
| HTML/XML tag block | `it` `at` | o, x |
| Any quote (mini.ai) | `iq` `aq` | o, x |
| Function call (mini.ai) | `if` `af` | o, x |
| Argument (mini.ai) | `ia` / `a`, pause, `a` — see note | o, x |
| Next object (mini.ai; remapped from `an`/`in`) | `aa{obj}` / `ii{obj}` | o, x |
| Previous object (mini.ai) | `al{obj}` / `il{obj}` | o, x |
| Move to start / end of object (mini.ai) | `g[{obj}` / `g]{obj}` | n, x, o |
| Git hunk | `ih` | o, x |

> mini.ai: "next" is remapped to `aa`/`ii` (default `an`/`in`) to avoid clashing with built-in
> treesitter selection. So `aa` is always "around next": `daa)` = delete around next `)` block,
> `daaa` = delete around *next* argument. For around the *current* argument, type `da`, wait
> past `timeoutlen` (300ms), then `a`. `ia` (inside argument) is unaffected.

#### Treesitter selection (Neovim 0.12+)

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Select parent / larger node | `an` | x, o |
| Select child / smaller node | `in` | x, o |
| Select next / previous node | `]n` / `[n` | x |
| Expand selection to next / previous node | `]N` / `[N` | x |

### Editing

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Insert before / after cursor | `i` / `a` | n |
| Insert at line start / end | `I` / `A` | n |
| Insert at column 0 | `gI` | n |
| Open line below / above | `o` / `O` | n |
| Delete / change / yank {motion} | `d` / `c` / `y` | n, v |
| Delete / change / yank line | `dd` / `cc` / `yy` | n |
| Delete / change / yank to end of line | `D` / `C` / `Y` | n |
| Delete char under / before cursor | `x` / `X` | n |
| Replace char | `r{c}` | n, v |
| Replace mode | `R` | n |
| Paste after / before | `p` / `P` | n, v |
| Paste, cursor after text | `gp` / `gP` | n |
| Undo / redo | `u` / `<C-r>` | n |
| Undo line | `U` | n |
| Repeat last change | `.` | n |
| Join lines (with / without space) | `J` / `gJ` | n, v |
| Indent / dedent | `>>` / `<<` | n |
| Indent / dedent selection | `>` / `<` | v |
| Auto-indent | `==` | n |
| Auto-indent selection | `=` | v |
| Toggle case char / selection | `~` | n, v |
| Lowercase / uppercase {motion} | `gu` / `gU` | n, v |
| Toggle case {motion} | `g~` | n, v |
| Format {motion} (textwidth) | `gq` | n, v |
| Increment / decrement number | `<C-a>` / `<C-x>` | n |
| Increment / decrement numbers in selection | `g<C-a>` / `g<C-x>` | v |
| Comment line / {motion} | `gcc` / `gc` | n |
| Comment selection | `gc` | x |
| Comment text object | `gc` | o |
| Repeat last macro / record macro | `@@` / `q{r}` … `q` | n |
| Play macro | `@{r}` | n |
| Format buffer (conform) | `<leader>f` | n, v |

#### Registers

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Use register for next op | `"{r}` | n, v |
| System clipboard | `"+` / `"*` (default register is already `unnamedplus`) | n, v |
| Paste register in insert/command mode | `<C-r>{r}` | i, c |

#### Surround (mini.surround)

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Add surrounding (`saiw)` = word) | `sa{motion}{char}` | n |
| Add surrounding to selection | `sa{char}` | v |
| Delete surrounding | `sd{char}` | n |
| Replace surrounding | `sr{old}{new}` | n |
| Find surrounding (right / left) | `sf{char}` / `sF{char}` | n |
| Highlight surrounding | `sh{char}` | n |
| Same, but next / previous match | append `n` / `l` (e.g. `sdn)`, `srl)]`) | n |

### Insert mode

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Exit insert mode | `<Esc>` / `<C-[>` / `<C-c>` | i |
| Delete word / to line start before cursor | `<C-w>` / `<C-u>` | i |
| Delete char before cursor | `<C-h>` | i |
| Indent / dedent line | `<C-t>` / `<C-d>` | i |
| Run one normal command | `<C-o>` | i |
| Insert register | `<C-r>{r}` | i |
| Insert char literally / by code | `<C-v>{c}` / `<C-v>u{hex}` | i |
| Insert digraph | unavailable: `<C-k>` is taken by blink.cmp (signature help) | i |
| Insert text of previous insert | `<C-a>` | i |
| Complete from keywords (built-in) | `<C-x><C-n>` / `<C-x><C-p>` (`<C-n>`/`<C-p>` are taken by blink.cmp) | i |
| Complete file name (built-in) | `<C-x><C-f>` | i |
| Signature help (LSP, built-in) | `<C-s>` | i, s |

#### Completion (blink.cmp, `default` preset)

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Show completion menu | `<C-Space>` | i |
| Accept | `<C-y>` | i |
| Cancel (hide menu, revert selection) | `<C-e>` | i |
| Select next / previous (menu open) | `<C-n>` / `<C-p>` (or `<Down>` / `<Up>`) | i |
| Scroll documentation up / down | `<C-b>` / `<C-f>` | i |
| Toggle documentation | `<C-Space>` (while menu open) | i |
| Snippet: next / previous placeholder | `<Tab>` / `<S-Tab>` | i, s |
| Toggle signature help | `<C-k>` | i, s |

### Diagnostic

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Open diagnostics in location list | `<leader>q` | n |
| Search diagnostics (Telescope) | `<leader>sd` | n |
| Next / previous diagnostic | `]d` / `[d` | n |
| Last / first diagnostic | `]D` / `[D` | n |
| Show diagnostic float | `<C-w>d` / `<C-w><C-d>` | n |

### Buffer

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Find existing buffers (Telescope) | `<leader><leader>` | n |
| Fuzzy search in current buffer | `<leader>/` | n |
| Live grep in open buffers | `<leader>s/` | n |
| Next / previous buffer | `]b` / `[b` | n |
| First / last buffer | `[B` / `]B` | n |
| Alternate buffer | `<C-^>` | n |
| Go to buffer N | `{N}<C-^>` | n |
| List buffers | `:ls` | c |
| Save | `:w` | c |
| Save all / quit all | `:wa` / `:qa` | c |
| Close buffer | `:bd` | c |
| Write and quit / quit | `ZZ` / `ZQ` | n |
| Clear search highlight | `<Esc>` | n |
| File explorer: reveal current file (Neo-tree) | `\` | n |
| File explorer: close window | `\` (inside Neo-tree) | n |
| Toggle markdown render, global | `<leader>tmr` *(buf: markdown)* | n |
| Toggle markdown render, buffer | `<leader>tmb` *(buf: markdown)* | n |
| Format buffer | `<leader>f` | n, v |
| Fold: toggle / open / close | `za` / `zo` / `zc` | n |
| Fold: open / close all | `zR` / `zM` | n |
| Next / previous fold | `zj` / `zk` | n |

#### Neo-tree (inside the tree window)

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Close window | `\` | n |
| Open file / toggle dir | `<CR>` / `<Space>` | n |
| Open in split / vsplit / tab | `S` / `s` / `t` | n |
| Add file / dir | `a` / `A` | n |
| Delete / rename | `d` / `r` | n |
| Copy / cut to clipboard, paste | `y` / `x` / `p` | n |
| Copy / move to path | `c` / `m` | n |
| Refresh | `R` | n |
| Go up / set root | `<BS>` / `.` | n |
| Toggle hidden files | `H` | n |
| Fuzzy find / filter on submit | `/` / `f` | n |
| Show help | `?` | n |
| Close all dirs / preview | `z` / `P` | n |
| Quit | `q` | n |

### Window

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Focus left window | `<C-h>` | n |
| Focus right window | `<C-l>` | n |
| Focus lower window | `<C-j>` | n |
| Focus upper window | `<C-k>` | n |
| Cycle windows | `<C-w>w` / `<C-w>W` | n |
| Previous window | `<C-w>p` | n |
| Split horizontal (new below) | `<C-w>s` | n |
| Split vertical (new right) | `<C-w>v` | n |
| New empty window | `<C-w>n` | n |
| Close window | `<C-w>c` | n |
| Close other windows | `<C-w>o` | n |
| Quit window | `<C-w>q` | n |
| Move window to far left / bottom / top / right | `<C-w>H` / `<C-w>J` / `<C-w>K` / `<C-w>L` | n |
| Rotate windows | `<C-w>r` / `<C-w>R` | n |
| Exchange with next window | `<C-w>x` | n |
| Move window to new tab | `<C-w>T` | n |
| Equalize sizes | `<C-w>=` | n |
| Increase / decrease height | `<C-w>+` / `<C-w>-` | n |
| Increase / decrease width | `<C-w>>` / `<C-w><` | n |
| Maximize height / width | `<C-w>_` / `<C-w>\|` | n |
| Open file under cursor in split | `<C-w>f` | n |
| Tabs: next / previous | `gt` / `gT` | n |
| Tabs: go to N | `{N}gt` | n |
| Mouse resize / focus | (mouse enabled: `mouse=a`) | n, v, i |

### Search

#### In-buffer

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Search forward / backward | `/` / `?` | n, v, o |
| Next / previous match | `n` / `N` | n, v, o |
| Word under cursor forward / backward | `*` / `#` | n, v |
| Partial word under cursor | `g*` / `g#` | n |
| Clear highlight | `<Esc>` | n |
| Substitute (live `inccommand=split` preview) | `:%s/old/new/g` | c |
| Repeat last substitute | `&` / `g&` | n |
| Search history | `q/` / `q?` | n |
| Command history | `q:` | n |

#### Telescope

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Search help | `<leader>sh` | n |
| Search keymaps | `<leader>sk` | n |
| Search files | `<leader>sf` | n |
| Search Telescope pickers | `<leader>ss` | n |
| Search current word / selection | `<leader>sw` | n, v |
| Search by grep | `<leader>sg` | n |
| Search diagnostics | `<leader>sd` | n |
| Resume last search | `<leader>sr` | n |
| Search recent files | `<leader>s.` | n |
| Search commands | `<leader>sc` | n |
| Search Neovim config files | `<leader>sn` | n |
| Fuzzy search in current buffer | `<leader>/` | n |
| Live grep in open files | `<leader>s/` | n |
| Find buffers | `<leader><leader>` | n |

#### Inside a Telescope prompt

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Next / previous result | `<C-n>` / `<C-p>` (or `<Down>` / `<Up>`) | i |
| Next / previous result | `j` / `k` | n |
| Select | `<CR>` | i, n |
| Open in horizontal split / vertical split / tab | `<C-x>` / `<C-v>` / `<C-t>` | i, n |
| Scroll preview up / down | `<C-u>` / `<C-d>` | i, n |
| Scroll preview left / right | `<C-f>` / `<C-k>` | i, n |
| Send to quickfix / selected to quickfix | `<C-q>` / `<M-q>` | i, n |
| Toggle selection and next / previous | `<Tab>` / `<S-Tab>` | i, n |
| Show mappings for this picker | `?` (n) / `<C-/>` (i) | n, i |
| Close | `<Esc>` (n) / `<C-c>` (i) | n, i |

### Git

All Gitsigns maps are buffer-local to files in a git repo.

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Next hunk (or diff change in diff mode) | `]c` | n |
| Previous hunk (or diff change in diff mode) | `[c` | n |
| Stage hunk | `<leader>hs` | n, v |
| Reset hunk | `<leader>hr` | n, v |
| Stage buffer | `<leader>hS` | n |
| Reset buffer | `<leader>hR` | n |
| Preview hunk | `<leader>hp` | n |
| Preview hunk inline | `<leader>hi` | n |
| Blame line (full) | `<leader>hb` | n |
| Diff against index | `<leader>hd` | n |
| Diff against last commit | `<leader>hD` | n |
| Hunk quickfix list (whole repo) | `<leader>hQ` | n |
| Hunk quickfix list (this file) | `<leader>hq` | n |
| Toggle inline line blame | `<leader>tb` | n |
| Toggle intra-line word diff | `<leader>tw` | n |
| Select hunk (text object) | `ih` | o, x |
| Diff mode: obtain / put hunk | `do` / `dp` | n |
| Diff mode: update diff | `:diffupdate` | c |

### LSP

*(buf)* Active only in buffers with an attached language server.

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Rename symbol | `grn` | n |
| Code action | `gra` | n, x |
| Go to references (Telescope) | `grr` | n |
| Go to implementation (Telescope) | `gri` | n |
| Go to definition (Telescope) | `grd` | n |
| Go to declaration | `grD` | n |
| Go to type definition (Telescope) | `grt` | n |
| Document symbols (Telescope) | `gO` | n |
| Workspace symbols (Telescope) | `gW` | n |
| Jump back from definition | `<C-t>` | n |
| Run codelens | `grx` | n |
| Hover documentation | `K` | n |
| Signature help | `<C-s>` | i, s |
| Go to definition (tag) | `<C-]>` | n |
| Format buffer | `<leader>f` | n, v |
| Toggle inlay hints | `<leader>th` | n |

### Toggles

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Toggle inlay hints (LSP) | `<leader>th` | n |
| Toggle git line blame | `<leader>tb` | n |
| Toggle git word diff | `<leader>tw` | n |
| Toggle markdown render (global) | `<leader>tmr` | n |
| Toggle markdown render (buffer) | `<leader>tmb` | n |

### Language-specific

#### Rust

`rust_analyzer` is enabled via LSP; no Rust-specific keymaps. Use the [LSP](#lsp) table.
`<leader>f` formats (uncomment `rustfmt` in `conform` to use it).

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Rename / code action / references / definition | `grn` / `gra` / `grr` / `grd` | n |
| Hover | `K` | n |

#### Markdown

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Toggle render (global) | `<leader>tmr` | n |
| Toggle render (current buffer) | `<leader>tmb` | n |

### Terminal

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Open terminal in current window | `:terminal` | c |
| Exit terminal mode | `<Esc><Esc>` | t |
| Exit terminal mode (built-in) | `<C-\><C-n>` | t |
| Enter terminal mode | `i` / `a` | n |
| Run one normal command | `<C-\><C-o>` | t |
| Send literal `<C-\>` | `<C-\><C-\>` | t |

### Which-key

| Action | Keystrokes | Mode |
| --- | --- | --- |
| Show pending keys after prefix (popup `delay` = 0) | `<leader>` / `g` / `z` / `<C-w>` … | n, x |

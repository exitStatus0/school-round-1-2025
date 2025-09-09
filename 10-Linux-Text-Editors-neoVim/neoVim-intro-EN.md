# Lecture: **Neovim — Modern CLI Editor for Linux (and More)**

> This lecture introduces **Neovim (nvim)** — a modern "Vim-compatible" editor optimized for speed, automation and extensibility. We explain basic modes and commands (similar to Vim), show save/exit, search/replace, navigation, and briefly touch on plugins (LSP, Treesitter, Telescope) and minimal **Lua** configuration. Examples in terminal prompt use `{userName}` instead of specific username.

---

## Learning Objectives
After the lecture you will be able to:
- Explain what **Neovim** is and how it relates to **Vim**.
- Perform basic actions in **normal/insert mode**: editing, saving, exiting.
- Use search, replace, navigation, undo/redo changes.
- Understand basic **buffers/windows/tabs**.
- Launch **minimal Neovim configuration** in Lua and install plugins (via `lazy.nvim`).
- Know typical DevOps/CLI scenarios where Neovim is the most convenient tool (quick config edits, Git commits, SSH sessions on servers).

---

## Lecture Plan
1. What is Neovim and why it
2. Working modes: Normal, Insert, Visual, Command-line
3. Basic file operations: open/create, save, exit
4. Useful Normal mode commands: navigation, deletion, search/replace, undo
5. Buffers, windows, tabs (buffers/splits/tabs)
6. Neovim integrations: built-in terminal, shell command execution
7. Neovim installation (briefly) and minimal Lua configuration
8. Plugins: Treesitter, LSP, Telescope (via `lazy.nvim`)
9. Practical (15–30 min)
10. Common mistakes and tips
11. Control questions
12. "Pianist and synthesizer" analogy
13. Glossary

---

## 1) What is Neovim and Why It

- **Neovim (nvim)** — "Vim-compatible" editor focused on **modernization**: Lua API, asynchronicity, built-in **LSP client**, **Treesitter**, terminal, etc.
- Advantages for daily work:
  - **Speed and efficiency** after mastering gestures/commands.
  - **CLI-oriented**: perfect for SSH and servers without GUI.
  - **Universal**: code editing (Python, Go, YAML, Markdown…), configs, commit messages.
  - **Extensible**: rich plugin ecosystem, **Lua** configuration.
- If you know basic Vim techniques — **Neovim "feels" the same**, but with additional capabilities.

---

## 2) Working Modes

- **Normal (default):** navigation, commands, text manipulation (delete/copy/paste), search, etc.
- **Insert:** text input. Switch from Normal → Insert: press **`i`** (or `a`, `o`, etc.). `-- INSERT --` indicator at bottom.
- **Visual / Visual Line / Visual Block:** character selection (**`v`**), lines (**`V`**), blocks (**`Ctrl+v`**).
- **Command-line:** colon **`:`** for entering commands (e.g., `:w`, `:q`, `:s/.../.../`).  
  Return to Normal — **`Esc`**.

---

## 3) Basic File Operations

Open existing or create new file:
```bash
{userName}@host:~$ nvim config.yaml
```

Modes:
- Normal → Insert: **`i`** (insert before cursor), **`a`** (after), **`o`** (new line below and in Insert).
- Return to Normal: **`Esc`**.

Save/exit (in Command-line, i.e., after `:`):
- **`:w`** — write (save).
- **`:q`** — quit.
- **`:wq`** or **`:x`** — write and quit.
- **`:q!`** — quit **without saving**.

Useful:
- **`:e <path>`** — open another file in current buffer.
- **`:w <new_file>`** — save to new file ("Save As…").
- **`:help`** or **`:h <topic>`** — help (e.g., `:h :s` or `:h motion`).

---

## 4) Useful Normal Mode Commands

**Navigation:**
```text
h j k l      # ← ↓ ↑ → (cursor movement)
0 / ^        # beginning of line
$            # end of line
gg / G       # beginning / end of file
:<number>    # go to N-th line (e.g., :12)
w / b / e    # word forward / backward / end of word
```

**Editing and deletion:**
```text
x            # delete character under cursor
dd           # delete (cut) current line
D / d$       # delete to end of line
cc / S       # change (replace) entire line
u            # undo
Ctrl+r       # redo
p / P        # paste after / before cursor
```

**Search and replace:**
```text
/<pattern>   # search forward
n / N        # next / previous match
:%s/old/new/g         # replace all occurrences in file
:%s/old/new/gc        # same with confirmation
```

Tip: in Neovim **syntax highlighting** is usually available, smart indentation, etc. (by default or with plugins).

---

## 5) Buffers, Windows, Tabs

- **Buffer** — open file in memory.
- **Window (split)** — "panel" showing buffer. Can have multiple windows side by side.
- **Tab** — set of windows.

Commands:
```text
:ls              # list buffers
:b <nr|pattern>  # go to buffer
:sp / :vsp       # horizontal / vertical split
Ctrl+w s / v     # same with keys
Ctrl+w h/j/k/l   # move between windows
:tabnew          # new tab
:tabn / :tabp    # next / previous tab
```

---

## 6) Neovim Integrations

- **Built-in terminal:** `:terminal` or short `:term`. Exit to Normal: `Ctrl+\` then `Ctrl+n`.
- **Shell command execution:** `:!ls`, `:!pytest`, `:!kubectl get pods` — convenient for quick checks.
- **Mode without configs:** `nvim -u NONE` or clean mode `nvim --clean` (for config/plugin diagnostics).

---

## 7) Neovim Installation (Briefly)

```bash
# Debian/Ubuntu
sudo apt update && sudo apt install -y neovim

# Fedora/RHEL
sudo dnf install -y neovim

# Arch
sudo pacman -S neovim
```

> Tip: On work servers often system package is sufficient. On local machine sometimes you want newer version — install from official packages of your distribution or use ready builds (if needed).

---

## 8) Minimal Lua Configuration + Plugins (`lazy.nvim`)

Neovim configuration files (Linux): **`~/.config/nvim/init.lua`**.  
Example of **minimal** configuration with basic settings and **lazy.nvim** plugin manager:

```lua
-- ~/.config/nvim/init.lua

-- 1) Basic options
vim.opt.number = true
vim.opt.relativenumber = true
vim.opt.expandtab = true
vim.opt.shiftwidth = 2
vim.opt.tabstop = 2
vim.opt.clipboard = "unnamedplus"   -- shared clipboard with OS (if supported)

-- 2) Leader key for convenient mappings
vim.g.mapleader = " "

-- 3) Bootstrap lazy.nvim (if not installed yet)
local lazypath = vim.fn.stdpath("data") .. "/lazy/lazy.nvim"
if not vim.loop.fs_stat(lazypath) then
  vim.fn.system({
    "git",
    "clone",
    "--filter=blob:none",
    "https://github.com/folke/lazy.nvim.git",
    "--branch=stable",
    lazypath,
  })
end
vim.opt.rtp:prepend(lazypath)

-- 4) Plugin declarations
require("lazy").setup({
  {"nvim-treesitter/nvim-treesitter", build = ":TSUpdate"},
  {"nvim-lua/plenary.nvim"},
  {"nvim-telescope/telescope.nvim"},
  {"neovim/nvim-lspconfig"},
  {"williamboman/mason.nvim"},
  {"williamboman/mason-lspconfig.nvim"},
  {"hrsh7th/nvim-cmp"},
  {"hrsh7th/cmp-nvim-lsp"},
  {"L3MON4D3/LuaSnip"},
})

-- 5) Simple Treesitter setup
require("nvim-treesitter.configs").setup({
  ensure_installed = { "lua", "bash", "python", "yaml", "markdown", "json" },
  highlight = { enable = true },
  indent = { enable = true },
})

-- 6) LSP (via mason + lspconfig)
require("mason").setup()
require("mason-lspconfig").setup({
  ensure_installed = { "lua_ls", "bashls", "pyright", "yamlls", "jsonls" },
})
local lspconfig = require("lspconfig")
local capabilities = require("cmp_nvim_lsp").default_capabilities()

for _, server in ipairs({ "lua_ls", "bashls", "pyright", "yamlls", "jsonls" }) do
  lspconfig[server].setup({ capabilities = capabilities })
end

-- 7) Autocompletion
local cmp = require("cmp")
cmp.setup({
  mapping = cmp.mapping.preset.insert({
    ["<CR>"] = cmp.mapping.confirm({ select = true }),
    ["<C-Space>"] = cmp.mapping.complete(),
  }),
  sources = {
    { name = "nvim_lsp" },
  },
})
```

> After first `nvim` launch with such configuration `lazy.nvim` will pull plugins itself. For LSP servers `:Mason` will help (install needed ones from list).

---

## 9) Practical (15–30 min)

1. **Launch Neovim** and create file:
   ```bash
   nvim ~/labs/nvim-demo/notes.md
   ```
2. **Modes:** enter text in **Insert** (`i`), return to **Normal** (`Esc`).  
3. **Save/exit:** `:w`, `:q`, `:wq`, `:q!`.
4. **Navigation and edits:** `gg`, `G`, `0`, `$`, `dd`, `p`, `u`, `Ctrl+r`.
5. **Search/replace:** `/TODO`, `n`, `:%s/TODO/DONE/gc`.
6. **Splits and buffers:** `:vsp notes.md`, `Ctrl+w h/l`, `:ls`, `:b <nr>`.
7. **Built-in terminal:** `:term`, execute `ls`, return to Normal (`Ctrl+\`, `Ctrl+n`).

> *Result:* manage files in Neovim, know how to save, search/replace, use splits and terminal, understand Lua configuration basics.

---

## 10) Common Mistakes and Tips

- **Forgot to exit Insert** — press **`Esc`** to return to Normal.
- **`:q` doesn't work** due to unsaved changes — use `:wq` or `:q!`.
- **Configuration breaks** — run `nvim -u NONE` or `--clean` to diagnose.
- **Slow startup** — check plugins; disable unnecessary ones, update `lazy.nvim` and plugins.
- **Copying to system clipboard doesn't work** — check `set clipboard=unnamedplus` and presence of corresponding clipboard dependencies in OS.

---

## 11) Control Questions

1. How does Neovim differ from classic Vim in terms of extensibility?
2. What modes exist in Neovim and how to switch between them?
3. How to save changes, exit file and exit **without saving**?
4. How to perform search and global replace in entire file?
5. What are **buffer**, **split** and **tab** in Neovim?
6. Where is **init.lua** located and what does `lazy.nvim` handle?

---

## 12) Analogy: "Pianist and Synthesizer"

- **Neovim** — powerful "digital synthesizer" in your terminal.
- **Normal** — "setup mode": switch tools, sections, manage structure.
- **Insert** — "performance mode": each press is a "note", you "play" text.
- **Esc / i** — quick transition between setup and performance.
- **`:wq` / `:q!`** — save composition or close without recording.
- **Plugins** — effects and modules that extend "synthesizer" capabilities.

---

## 13) Glossary

- **Neovim (nvim):** modern Vim-compatible editor with Lua API, LSP, Treesitter.
- **Normal/Insert/Visual/Command-line:** main working modes.
- **Buffer/Window/Tab:** file display model in Neovim.
- **LSP (Language Server Protocol):** hints, diagnostics, code navigation.
- **Treesitter:** code parsing for highlighting and structure.
- **Telescope:** fuzzy search/navigation (files, grep, buffers).
- **lazy.nvim:** modern plugin manager.
- **`init.lua`:** main Neovim configuration file.

---

> **Brief summary:** Neovim combines Vim's speed and philosophy with modern capabilities (Lua, LSP, Treesitter). For DevOps and developers it's one of the most efficient tools for working with files and code in terminal.

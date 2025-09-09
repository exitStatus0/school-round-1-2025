# Lecture: **GNU nano — Simple and Fast CLI Editor for Linux**

> This lecture covers **GNU nano** — a minimalist, beginner-friendly text editor for terminal. We explain basic commands (open/save/exit, search/replace, cut/paste), hotkeys, `~/.nanorc` configuration, as well as typical DevOps scenarios (config editing via SSH, quick commit-message edits, etc.).

---

## Learning Objectives
After the lecture you will be able to:
- Open/create files in **nano** and save changes.
- Use **search and replace**, quickly jump to line/column.
- Perform basic editing: **cut/copy/paste**, **undo/redo**.
- Configure useful options in `~/.nanorc` (line numbers, indentation, line wrapping, syntax highlighting).
- Know how to make **nano** the default EDITOR (bash/git).

---

## Lecture Plan
1. What is `nano` and when to use it
2. Installation and launch
3. Basic actions: open/create, save, exit
4. Editing: cut/copy/paste, search/replace, go to line
5. Useful hotkeys and interface hints
6. Configuration: `~/.nanorc` (example)
7. Typical DevOps/CLI scenarios
8. Practical (10–20 min)
9. Common mistakes and tips
10. Control questions
11. Glossary

---

## 1) What is `nano` and When to Use It
- **GNU nano** — simple, console editor that displays **useful hints** at bottom of window (list of main keys).
- Suitable for **quick edits** of configs, notes, Markdown, YAML, scripts — especially **on remote servers** without GUI.
- Lower entry barrier than Vim/Neovim: no need to memorize modes — **type text immediately**.

---

## 2) Installation and Launch
```bash
# Debian/Ubuntu
sudo apt update && sudo apt install -y nano

# Fedora/RHEL
sudo dnf install -y nano

# Arch
sudo pacman -S nano
```

Launch/create file:
```bash
{userName}@host:~$ nano /etc/hosts          # as root: sudo nano /etc/hosts
{userName}@host:~$ nano notes.md            # will create if file doesn't exist
```

Make `nano` default editor:
```bash
echo 'export EDITOR=nano' >> ~/.bashrc
git config --global core.editor "nano"
```

---

## 3) Basic Actions: Open/Save/Exit
In hints at bottom notation is used: `^` = **Ctrl**, `M-` = **Alt/Meta**.

- **Save (Write Out):** `Ctrl+O`, Enter
- **Exit:** `Ctrl+X`
- **Help:** `Ctrl+G` (short reference in nano itself)
- **Undo / Redo:** `Alt+U` / `Alt+E` (on newer versions — Undo/Redo)

> Tip: If there are unsaved changes when exiting, nano will offer to save them.

---

## 4) Editing: Cut/Copy/Paste, Search/Replace, Go To
**Text manipulation**
- **Set/remove mark:** `Ctrl+6` (or `Ctrl+^`)
- **Cut:** `Ctrl+K` (cuts line or selection)
- **Copy:** `Alt+6` (copies selection to buffer)
- **Paste (Uncut):** `Ctrl+U`
- **Justify paragraph:** `Ctrl+J`

**Search and replace**
- **Search:** `Ctrl+W`, then enter text; **next match** — `Alt+W`
- **Replace:** `Ctrl+\` (Replace), enter search pattern and replacement line, confirm prompts

**Go To (Go To Line/Column)**
- **Go to line/column:** `Ctrl+_` (then enter `N` or `N,M`)

---

## 5) Useful Hotkeys and Hints
Most used:
```
Ctrl+O   — save (Write Out)
Ctrl+X   — exit
Ctrl+W   — search (Where Is)
Ctrl+\   — replace (Replace)
Ctrl+K   — cut line/selection
Ctrl+U   — paste
Ctrl+6   — set mark (start selection)
Alt+6    — copy selection
Alt+U / Alt+E — undo / redo
Ctrl+_   — go to line/column
Ctrl+G   — help
```

Other useful:
```
Alt+G    — show/hide line number and cursor position (in some versions — status line)
Alt+N    — toggle line numbers (or via nanorc, see below)
Alt+S    — toggle line wrapping (softwrap) in some builds
```

> **Alt** combinations may differ depending on terminal emulator/locale. If Alt doesn't work, try **Esc** + key sequentially.

---

## 6) Configuration: `~/.nanorc` (Example)
User local configuration: **`~/.nanorc`**  
System: **`/etc/nanorc`** (for all users)

Example useful `~/.nanorc`:
```nanorc
## Basic conveniences
set linenumbers        # show line numbers
set softwrap           # soft wrap long lines
set tabsize 2          # tab size
set tabstospaces       # replace tabs with spaces
set autoindent         # automatic indentation
set indicator          # position bar (if supported)
set mouse              # enable mouse (terminal must support)

## Syntax highlighting (examples in /usr/share/nano/*.nanorc)
include "/usr/share/nano/*.nanorc"

## Constant status display (on newer versions)
set constantshow
```

> After changes in `~/.nanorc` restart nano. List of available themes/syntax depends on your distribution package.

---

## 7) Typical DevOps/CLI Scenarios
- **Config editing**: `sudo nano /etc/nginx/nginx.conf`, `/etc/hosts`, systemd unit files.
- **Git commit messages**: if EDITOR=nano — automatically opens for `git commit`.
- **Quick YAML/ENV edits** for containers/orchestration (Kubernetes, docker-compose).
- **SSH sessions** on servers without GUI: nano is often already installed or easily installable.

---

## 8) Practical (10–20 min)
1. Open/create file:
   ```bash
   nano ~/labs/nano-demo/notes.md
   ```
2. Write several lines of text; **save** `Ctrl+O`, **exit** `Ctrl+X`.
3. Open file again, select fragment: `Ctrl+6` → arrows → **copy** `Alt+6` → **paste** `Ctrl+U`.
4. **Search/replace**: `Ctrl+W` → enter word; **replace** `Ctrl+\` → enter old/new text.
5. **Go to line**: `Ctrl+_` → enter number.
6. Enable line numbers and softwrap in `~/.nanorc`, restart nano and check.

> *Result:* confidently master basic nano techniques for daily terminal tasks.

---

## 9) Common Mistakes and Tips
- **Closed without saving** — get used to immediately pressing `Ctrl+O` before `Ctrl+X`.
- **Alt combinations don't work** — try `Esc` instead of Alt or check terminal settings.
- **Encoding/Windows line endings** — for files with CRLF sometimes useful to convert to LF (`dos2unix` utility).

---

## 10) Control Questions
1. What keys are used for **saving** and **exiting**?
2. How do **cut** (`Ctrl+K`) and **copy** (`Alt+6`) differ? How to set **mark**?
3. How to perform **search** and **replace** in nano?
4. Where is nano user configuration located and how to enable **line numbers**?
5. How to make nano the default **EDITOR** in bash/git?

---

## 11) Glossary
- **GNU nano** — console text editor for Unix-like systems.
- **`~/.nanorc` / `/etc/nanorc`** — user/system configuration files.
- **Write Out** — save file (Ctrl+O).
- **Cut/Copy/Paste** — cut/copy/paste (Ctrl+K / Alt+6 / Ctrl+U).
- **Mark** — selection mode (Ctrl+6).
- **Softwrap** — soft wrapping of long lines.
- **linenumbers** — show line number on the left.

---

> **Brief summary:** **nano** is the perfect choice for quick edits in terminal. Simple interface with hints, understandable hotkeys and easy configuration make it a convenient tool for both beginners and experienced engineers.

# Lecture: **GUI vs CLI in Linux — Practical Comparison of File Operations**

> This lecture practically compares performing typical file system operations in **Graphical User Interface (GUI)** and through **Command Line Interface (CLI)** in Linux. We cover navigation, file/directory management, useful terminal features, system information and working with privileges.

---

## Learning Objectives
After the lecture you will be able to:
- Perform basic **navigation** (`pwd`, `ls`, `cd`) and view structure.
- Create/move/copy/delete **files and directories** (`mkdir`, `touch`, `mv`, `cp`, `rm`).
- Use **autocompletion**, command history, hotkeys.
- View **system information** (`uname -a`, `cat /etc/os-release`, `lscpu`, `free -h`).
- Understand when **superuser rights** are needed (`sudo`, `su -`).

---

## Lecture Plan
1. Navigation and file system overview
2. File and folder management
3. Useful terminal features
4. System information commands
5. Superuser privileges
6. CLI advantages (where it gives profit)
7. Practical (15–25 min), mistakes, control questions, glossary
8. "Construction site" analogy

---

## 1) Navigation and File System Overview

### `pwd` — *Print Working Directory*
Shows **absolute path** to current directory.
```bash
{userName}@workstation:~$ pwd
/home/{userName}
```

### `ls` — *List*
Lists contents of current directory.
```bash
ls
ls -la        # show all, including hidden (.dotfiles)
ls -R         # recursively traverse subdirectories
```

**GUI equivalent:** open directory in file manager and view file list.  
**CLI plus:** filtering/scripts, speed, recursive overview without clicks.

### `cd` — *Change Directory*
```bash
cd projects            # to subdirectory
cd ..                  # one level up (to parent directory)
cd /                   # to file system root
cd ~                   # to current user's home directory
cd /var/log            # absolute path
cd ../src/utils        # relative path
```

---

## 2) File and Folder Management

### `mkdir` — *Make Directory*
```bash
mkdir python_project
mkdir -p apps/api/{conf,logs,data}   # create directory tree at once
```

### `touch`
Create **empty file** or update timestamp of existing one:
```bash
touch main.py README.md
```

### `rm` — *Remove*
```bash
rm notes.txt              # delete file
rm -r old_logs            # recursively delete directory with contents
rm -rf build/             # CAREFUL: forced recursive deletion
```

### `mv` — *Move/Rename*
```bash
mv draft.md notes.md                       # rename
mv notes.md docs/                          # move file
mv src/{app.py,utils.py} src/legacy/       # move multiple
```

### `cp` — *Copy*
```bash
cp config.sample.yml config.yml            # file copy
cp -r templates/ templates.bak/            # recursive directory copy
```

### `cat` — *Concatenate/View*
Show file contents in terminal:
```bash
cat README.md
```

**GUI equivalents:** create/move/delete through context menus and drag-and-drop.  
**CLI plus:** precision, batch operations, automation, indispensable on servers.

---

## 3) Useful Terminal Features

- **Autocompletion (Tab):** type few letters → `Tab` for completion or list of options.
- **Command history:**
  - **Up/Down arrows** — scroll through history.
  - `history` — numbered list of executed commands.
  - `Ctrl + R` — reverse search by substring in history.
  - History is saved in `~/.bash_history` (or corresponding shell file).
- **`clear`** — clear screen.
- **`Ctrl + C`** — interrupt current process.
- **`Ctrl + Shift + V`** — paste from clipboard (in many terminal emulators).

Examples:
```bash
history | tail -n 10
clear
```

---

## 4) System Information Commands

```bash
uname -a              # kernel, architecture, version
cat /etc/os-release   # distribution/OS version
lscpu                 # CPU details
free -h               # RAM in human-readable format
```

GUI equivalents: "About this system", "System settings".  
CLI plus: precise data for scripts/reporting/debugging.

---

## 5) Superuser Privileges

- **`sudo`** (*Super User Do*) — execute one command with admin rights:
  ```bash
  sudo systemctl status ssh
  sudo apt update && sudo apt upgrade -y
  ```
  On first call in session will ask for **current user** `{userName}` password; then briefly remembers authorization.

- **`su - <username>`** — switch to another account (with environment loading):
  ```bash
  su - {userName}
  ```

- **Prompt `$` vs `#`:**
  - `$` — regular user (safer by default).
  - `#` — `root` (full rights, increased risks).

---

## 6) CLI Advantages

- **Efficiency and speed** after mastering.
- **Bulk operations** (bulk actions, scripts, pipelines).
- **Power and control**, including what GUI sometimes can't do.
- **Automation** of repetitive tasks (bash, Python, Ansible, etc.).
- **Server management** without GUI — the only way.

> For visual tasks (image/video processing, web browsing) GUI remains indispensable.

---

## 7) Practical (15–25 min)

> **Goal:** practice complete file/directory workflow through CLI.

1. **Context overview**
   ```bash
   whoami
   hostname
   pwd
   ```
2. **Project structure**
   ```bash
   mkdir -p ~/labs/fs-ops/{src,docs,tests}
   cd ~/labs/fs-ops
   touch README.md src/main.py tests/test_main.py
   ls -la
   ```
3. **Copying/moving/renaming**
   ```bash
   cp README.md docs/overview.md
   mv src/main.py src/app.py
   ```
4. **Recursive actions**
   ```bash
   cp -r tests tests.bak
   rm -r tests.bak
   ```
5. **Viewing and searching**
   ```bash
   cat docs/overview.md
   history | grep "cp -r"
   ```
6. **Admin tasks (if needed)**
   ```bash
   sudo true        # check sudo access
   ```

> *Result:* created structure, managed files, applied history, practiced safe admin steps.

---

## Common Mistakes and Tips

- **`rm -rf` without checking path** — most common disaster. Make sure path is correct (`pwd`, `ls` before deletion).
- **Confusion between absolute/relative paths** — clearly understand where you are (`pwd`).
- **Spaces in names** — use quotes: `mv "My File.txt" "MyFile.txt"`.
- **Forgot `-r` for directories** in `cp`/`rm` — remember about recursion.
- **Copy-pasting commands without understanding** — look at `--help`/`man` and test on test paths.

---

## Control Questions

1. How does `ls -a` differ from `ls -R`?
2. How to go to **home directory** and to **root** of file system?
3. How do `mv` and `cp` differ? When is `-r` flag needed?
4. How to view distribution version and CPU information?
5. When is it appropriate to use `sudo` or `su -`?

---

## Glossary

- **GUI** — graphical user interface (windows/buttons).
- **CLI** — command line (text commands).
- **Terminal emulator** — program for working with CLI.
- **Dotfiles** — hidden files starting with `.` (see `ls -a`).
- **Absolute/relative path** — from `/` or from current directory.
- **Recursion** — traversing subdirectories/contents.

---

## Analogy: "Construction Site"

- **`pwd`** — "Where am I standing now?"
- **`ls`** — "What's nearby: what buildings/materials?"
- **`cd`** — moving between areas ( `cd ..` — step back; `cd /` — to main entrance).
- **`mkdir`** — lay foundation for new building.
- **`touch`** — place clean sheet/plan on table.
- **`rm` / `rm -r`** — demolition; `-r` — dismantle everything inside.
- **`mv`** — move building or rename it.
- **`cp` / `cp -r`** — make copy of building or entire area.
- **`cat`** — read plan/document.
- **`sudo`** — special permit from chief engineer.
- **`Ctrl + C`** — emergency stop of machine.
- **History** — work log for the day.

---

> **Brief summary:** Basic CLI commands allow quick and automated execution of typical file operations, and their combination with history and autocompletion significantly speeds up workflows — especially on servers without GUI.

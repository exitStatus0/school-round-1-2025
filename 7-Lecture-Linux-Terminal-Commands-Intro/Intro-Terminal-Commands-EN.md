# Lecture: **Linux CLI Fundamentals — Terminal, Prompt, Access Rights**

> This lecture transitions from understanding the Linux file system to practice: how to interact with it through the **Command Line Interface (CLI)**. We explain the difference between **GUI and CLI**, anatomy of the **terminal prompt**, roles of regular and administrative users, as well as provide practical exercises and control questions.

---

## Learning Objectives
After the lecture you will be able to:
- Explain the difference between **GUI** (graphical user interface) and **CLI** (command line).
- Recognize components of the **terminal prompt**: username, hostname, `~`, `$` or `#` symbol.
- Understand why **servers usually work without GUI** and why CLI is a fundamental tool for **DevOps**.
- Perform basic actions in terminal and safely interact with the system considering access rights.

---

## Lecture Plan
1. GUI vs CLI: what it is and where it's used
2. Terminal and prompt: what it consists of
3. Regular user (`$`) vs superuser (`#`)
4. Working in CLI: basic interaction and examples
5. "Smart house" analogy
6. Practical (10–20 min)
7. Common mistakes and security tips
8. Control questions
9. Glossary

---

## 1) GUI vs CLI

### GUI (Graphical User Interface)
Visual interface with **icons, windows, menus**. Allows opening programs, installing software, creating directories/files, working with **mouse**.

### CLI (Command-Line Interface)
**Text interface** where you enter **commands** and the system executes them. Everything possible in GUI **can be done in CLI**. The program we work with CLI in is called **terminal**.

### Availability
- **PCs/laptops** usually offer both GUI and CLI.
- **Servers** are mostly supplied **without GUI**, only with CLI — this saves resources and increases reliability.

### Relevance for DevOps
Working with servers inevitably requires **confident CLI mastery**. Often CLI on PC/laptop turns out to be **faster and more convenient** than GUI for complex or repetitive tasks (scripts, automation).

---

## 2) Terminal Window and Prompt

**Terminal** is a program (like "browser window") where you can have multiple tabs/windows with separate sessions. Each session shows a **prompt** — a line with useful context.

A typical prompt might look like this:
```bash
{userName}@web01:~$
```
or for administrator (root):
```bash
root@db01:/etc#
```

Let's break down the components:
- **Username** — who is currently logged into the system (e.g., `{userName}`).
- **Computer name (hostname)** — which machine you're working on (`web01`, `db01`, etc.).
- **`~` (tilde)** — abbreviation for the current user's **home directory**.
- **`$`** — you're working as a **regular user**.
- **`#`** — you're working as **root (superuser)** with full rights.

> **Why is this important?** On servers you might be connected to many hosts. Understanding the prompt helps **not confuse environments** and be aware of **current rights**.

---

## 3) Roles and Rights: `$` vs `#`

- **`$` (regular user)** — safer by default; for administrative actions we add `sudo`.
- **`#` (root)** — full system access; mistakes can be **fatal** (deleting system files, security breach).

Examples:
```bash
# execute command with administrator rights once
sudo systemctl status ssh

# switch to interactive root session (careful!)
sudo -i
```

---

## 4) CLI Interaction: Basic Examples

Instead of clicks in GUI you **enter commands**:
```bash
# create directory
mkdir projects

# navigate to directory
cd projects

# create file
touch README.md

# view file list (detailed, including hidden)
ls -la

# show current path
pwd

# open file in console editor (e.g., nano)
nano README.md
```

> **Note:** in Linux **everything is a file** or close to a file. Commands work in a unified hierarchy from root `/` (see previous lecture on file system).

---

## 5) Analogy: "Smart House"

- **GUI** — like **control panel**: press buttons to turn on lights or open doors — **intuitive and visual**.
- **CLI** — like **voice commands**: "turn on living room lights", "open doors" — initially unusual, but **faster and more efficient** for complex/repetitive tasks.
- **Terminal** — is the **microphone** through which you give commands.
- **Prompt** — your "smart system" that hints: **who you are**, **where you are**, **what authority you have** (`$` or `#`).

---

## 6) Practical (10–20 min)

> **Goal:** learn to read the prompt, determine context and perform basic actions in CLI.

1. **Open terminal** on your system (Linux, macOS, Windows + WSL).
2. **Find in prompt**: username, hostname, `~`, `$` or `#` symbol.
3. **Check your context**:
   ```bash
   whoami
   hostname
   echo $HOME
   pwd
   ```
4. **Create workspace** and basic files:
   ```bash
   mkdir -p ~/labs/cli-basics
   cd ~/labs/cli-basics
   touch notes.txt
   ls -la
   ```
5. **Perform administrative action** (if needed and with caution):
   ```bash
   sudo -k              # "forget" previous sudo privileges
   sudo true            # check that sudo works (will ask for password)
   ```
6. **Exit root session** if you opened one:
   ```bash
   exit
   ```

> *Result:* you read the prompt, understand your rights and perform basic file operations through CLI.

---

## 7) Common Mistakes and Security Tips

- **Working constantly as `root`** — risky. Better `sudo` for individual commands.
- **Copying commands without understanding** — check what each option does (`man`, `--help`).
- **Dangerous commands** like `rm -rf /` — **never** run without full awareness of consequences.
- **Confusion between multiple hosts** — pay attention to **hostname** in prompt.
- **Ignoring logging** — in case of problems check logs (`journalctl`, `/var/log/*`).

---

## 8) Control Questions

1. How do GUI and CLI differ and why is CLI critically important for DevOps?
2. What do the prompt elements mean: `{userName}@web01:~$`?
3. What's the difference between `$` and `#` and when to use `sudo`?
4. What basic commands help create directory/file and check current path?

---

## 9) Glossary

- **GUI** — graphical user interface (windows, buttons, menus).
- **CLI** — command line interface (working through text commands).
- **Terminal** — program for working with CLI (terminal emulator).
- **Prompt** — status line before cursor in terminal.
- **User/Root** — regular user (`$`) / superuser (`#`) with full rights.
- **`sudo`** — execute command with administrator privileges.
- **Hostname** — machine/server name in network.

---

> **Brief summary:** CLI is a universal Linux management tool, essential for DevOps engineers. Understanding the prompt, access rights and basic commands allows working quickly, accurately and safely.

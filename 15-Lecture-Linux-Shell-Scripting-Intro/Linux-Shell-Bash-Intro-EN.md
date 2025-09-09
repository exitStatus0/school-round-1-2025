# Lecture: **Shell in Linux and Bash Scripting Basics**

> In this lecture we'll explore what a **shell** is, what **Bash** is, what other shells exist, and learn to write and execute **Bash scripts** for DevOps task automation.

---

## Learning Objectives
After the lecture you will be able to:
- Explain the role of **shell** as command interpreter and its interaction with kernel.
- Name different shells (sh, **bash**, zsh, etc.) and differences between them.
- Create and run **Bash scripts** with shebang `#!/usr/bin/env bash` or `#!/bin/bash`.
- Use **variables**, **arguments** (`$1`, `$@`), **conditions**, **loops**, **functions**.
- Enable safe settings (`set -euo pipefail`), properly **quote** variables, and **debug** (`set -x`).

---

## Lecture Plan
1. What is shell and Bash
2. Other shells: `sh`, `zsh`, `fish` — and what they're for
3. Writing first Bash script (shebang, permissions, execution)
4. Basic Bash constructs: variables, substitution, quoting
5. Arguments, input reading, `getopts`
6. Conditions, loops, functions
7. Debug and best practices
8. Practical (20–30 min)
9. Common mistakes
10. Control questions
11. "Piano and sheet music" analogy
12. Glossary + cheat sheet

---

## 1) What is Shell and Bash
- **Shell** — command interpreter program that accepts your CLI commands and passes them to OS kernel.
- **Bash (Bourne-Again SHell)** — popular, functional shell and scripting language. In most Linux distributions — default shell.
- When they say "shell script", they usually mean **bash script**, but POSIX-compatible `sh` scripts or scripts for other shells are also possible.

Execution types:
- **Interactive** (you enter commands in terminal).
- **Non-interactive** (executing script from file).
- **Login/non-login** shell — affects which configs are read (`/etc/profile`, `~/.bash_profile`, `~/.bashrc`).

---

## 2) Other Shells: Brief Overview
- **`sh` (Bourne shell)** — basic, POSIX minimum. Almost everywhere available, convenient for maximum portability.
- **`bash`** — superset of `sh`, additional features (arrays, `[[ ]]`, `{..}` braces, extended `case`, etc.).
- **`zsh`** — more convenient interaction (autocompletion, hints), often used by developers.
- **`fish`** — beginner-friendly, but **incompatible** with familiar POSIX/Bash syntax.

Check available shells:
```bash
cat /etc/shells
```

---

## 3) First Bash Script: Shebang, Permissions, Execution

Let's create `setup.sh` in user `{userName}` directory:
```bash
mkdir -p /home/{userName}/scripts
nano /home/{userName}/scripts/setup.sh
```

**Shebang** (in first line of file):
```bash
#!/usr/bin/env bash
# alternatively, less portable: #!/bin/bash
```

Add content (example):
```bash
#!/usr/bin/env bash
set -euo pipefail

echo "🔧 Starting setup..."
DIR="/opt/demo"
if [[ ! -d "$DIR" ]]; then
  sudo mkdir -p "$DIR"
  echo "Created directory: $DIR"
fi

# demonstration of writing to file
echo "created_at=$(date -Is)" | sudo tee "$DIR/meta.env" >/dev/null
echo "✅ Done."
```

Make file executable and run:
```bash
chmod u+x /home/{userName}/scripts/setup.sh
/home/{userName}/scripts/setup.sh         # via shebang
# or explicitly under Bash:
bash /home/{userName}/scripts/setup.sh
```

> If you see `permission denied` — add `+x` (executable bit).

---

## 4) Variables, Substitution, Quoting

Variables and substitution:
```bash
NAME="prod"
echo "Current environment: $NAME"
echo "Date: $(date +"%Y-%m-%d")"      # command substitution
NUM=$((1 + 2))                        # arithmetic
```

Quoting — critically important:
- **Double quotes** `"` allow substitution (`"$VAR"`).
- **Single quotes** `'` — **literal** text (no substitutions).
- Always quote variables: `"$VAR"`, `"$@"` — this protects from spaces and special characters.

Globbing (patterns `*`, `?`, `[]`) — be careful: danger of unexpected expansion. Better to quote.

Exit codes and errors:
```bash
somecmd
echo "$?"      # exit code of previous command (0 = success)
```

---

## 5) Arguments, Input Reading, `getopts`

Script arguments:
```bash
#!/usr/bin/env bash
set -euo pipefail

echo "First argument: ${1:-<none>}"
echo "All arguments: $*"
echo "Safe argument list: $@"
for x in "$@"; do
  echo "arg = $x"
done
```

Reading from keyboard:
```bash
read -rp "Enter service name: " SVC
echo "You entered: $SVC"
```

`getopts` (short options):
```bash
#!/usr/bin/env bash
set -euo pipefail

NAME="world"
DRYRUN=false
while getopts "n:d" opt; do
  case "$opt" in
    n) NAME="$OPTARG" ;;
    d) DRYRUN=true ;;
    *) echo "Usage: $0 [-n name] [-d]"; exit 2 ;;
  esac
done

$DRYRUN && echo "[DRYRUN] Hello, $NAME!" || echo "Hello, $NAME!"
```

---

## 6) Conditions, Loops, Functions

Conditions:
```bash
if [[ -f "/etc/os-release" ]]; then
  echo "File /etc/os-release exists"
elif [[ -d "/etc" ]]; then
  echo "/etc is a directory"
else
  echo "No needed objects"
fi
```

Loops:
```bash
for f in *.log; do
  [[ -e "$f" ]] || continue
  gzip -9 "$f"
done

count=0
while (( count < 3 )); do
  echo "count=$count"
  ((count++))
done
```

Functions and return codes:
```bash
die() { echo "Error: $*" >&2; exit 1; }

ensure_cmd() {
  command -v "$1" >/dev/null 2>&1 || die "Command not found: $1"
}

ensure_cmd curl
```

---

## 7) Debug and Best Practices

- `set -euo pipefail` — stop on error, undefined variables, account for pipeline failures.
- `set -x` — execution tracing (debug). Run: `bash -x script.sh`.
- **Quote** all variables: `"$var"`, `"$@"`.
- Check **dependencies**: `command -v tool || die "..."`.
- Use **`#!/usr/bin/env bash`** for better portability across systems.
- Work with temporary files via `mktemp`, clean them in `trap`:
  ```bash
  TMP="$(mktemp)"; trap 'rm -f "$TMP"' EXIT
  ```
- **PATH**: store scripts in `~/bin` or `/usr/local/bin` and add to PATH.
- Static analysis: **shellcheck** (if available in your system).

---

## 8) Practical (20–30 min)

1. **Hello + arguments**
   - Create `hello.sh` that greets user: `./hello.sh {userName}` → `Hello, {userName}!`
2. **Directory backup**
   - `backup.sh SRC DST` — checks existence of directory **SRC**, creates **DST**, archives `tar -czf` with date in name.
3. **Bulk renaming**
   - `rename.sh "*.txt"` → adds suffix `-old` to each file, carefully working with spaces.
4. **Service check**
   - `check_svc.sh -n nginx [-d]` — if `-d`, only prints actions; otherwise executes `systemctl is-active` and shows logs of last 50 lines.
5. **Log cleanup**
   - Loop compresses all `*.log` older than 7 days in specified directory.

> *Result:* can create, run and debug Bash scripts with real DevOps scenarios.

---

## 9) Common Mistakes
- Missing **quoting**: `rm $FILE` (bad) vs `rm -- "$FILE"` (better).
- Missing `+x` → `permission denied`.
- Using `/bin/bash` on system where Bash is installed elsewhere → better `#!/usr/bin/env bash`.
- Mixing `sh` and `bash` syntax (e.g., `[[ ... ]]` — this is Bash, not available in `sh`).
- Wrong file/directory checks: `-f` (file), `-d` (directory), `-x` (executable), `-s` (non-empty).

---

## 10) Control Questions
1. How does shell differ from OS kernel?
2. Why is **shebang** needed? How do `#!/bin/bash` and `#!/usr/bin/env bash` differ?
3. What does `set -euo pipefail` do?
4. Why is it important to **quote** variables and `"$@"`?
5. How to handle options `-n` and `-d` in script?
6. How to see execution tracing of commands in script?

---

## 11) Analogy: "Piano and Sheet Music"
- Individual CLI commands — **individual notes**.
- Script — **sheet music** for entire piece: computer executes sequence of actions without your participation.
- Shell — **pianist** who "reads notes" and converts them to sound (system calls).
- **Bash** — "virtuoso" who knows complex techniques and speeds up work.
- `chmod +x` — **unfold sheet music and place on music stand** — otherwise can't "play".

---

## 12) Glossary + Cheat Sheet

**File/string tests:**
```bash
[[ -f FILE ]]  [[ -d DIR ]]  [[ -s FILE ]]  [[ -x FILE ]]
[[ -n "$s" ]]  [[ -z "$s" ]]
```

**Loops/conditions:**
```bash
for x in "$@"; do ...; done
while read -r line; do ...; done
if [[ cond ]]; then ...; elif [[ cond2 ]]; then ...; fi
case "$opt" in start) ... ;; stop) ... ;; *) ... ;; esac
```

**Variables/arguments:**
```bash
VAR="value"; export VAR
echo "$VAR"  "$1"  "$@"  "$#"  "$?"  "$$"
NAME="$(cmd)"; N=$((1 + 2))
```

**Streams/pipelines:**
```bash
cmd > out.txt     # overwrite
cmd >> out.txt    # append
cmd 2> err.txt    # errors
cmd | other       # pipeline
set -x            # debug
```

---

> **Conclusion:** Bash is both a shell and a scripting language. With its help you automate repetitive actions, get reproducible configurations and speed up work on servers and locally. Start with simple scripts, always quote variables and enable basic "insurance" (`set -euo pipefail`).

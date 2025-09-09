# Lecture: **Advanced Bash Scripting Concepts: Variables, Conditions, Parameters, `read`, Loops**

> In this lecture we dive deeper into **Bash**: variables and command substitution, conditional constructs `if/elif/else`, script parameters and user input reading, `for` and `while` loops, arithmetic `(( ))`, as well as safe script writing patterns.

---

## Learning Objectives
After the lecture you will be able to:
- Correctly declare, read and pass **variables**, including command substitution `$(...)`.
- Write **conditions** with file checks, numeric and string comparisons.
- Accept **parameters** (`$1`, `$@`, `$#`) and interact with user through **`read`** (including hidden input).
- Use **loops** `for`/`while` for mass operation automation.
- Apply **arithmetic** `(( ))` and `$(())`.
- Know when to use **`[[ ... ]]`** and how it differs from `[ ... ]`.
- Avoid common mistakes (`"$@"` vs `"$*"`, quoting and globbing, exit codes).

---

## Lecture Plan
1. Variables and command substitution
2. Conditions `if/elif/else`: files, numbers, strings
3. Script parameters: `$1`, `$@`, `$#`, default values
4. Interactive input: `read` (+ hidden input `-s`)
5. `for` and `while` loops
6. Arithmetic `(( ))`
7. `[[ ... ]]` vs `[ ... ]`
8. Practical (20–30 min)
9. Common mistakes and tips
10. Control questions
11. "Conductor and orchestra" analogy
12. Cheat sheet

---

## 1) Variables and Command Substitution

Declaration and usage:
```bash
config_file="config.yml"
echo "Using file: $config_file"
```

Command substitution (`$(...)`) and arithmetic substitution:
```bash
now_iso="$(date -Is)"
files_count="$(ls -1 config 2>/dev/null | wc -l)"
sum=$(( 40 + 2 ))     # → 42
echo "at=$now_iso, count=$files_count, sum=$sum"
```

> Variable names are convenient to write as `snake_case` (`file_name`) or `camelCase` (`fileName`).  
> **Always quote** variables when outputting/passing to commands: `"$var"` — this protects from spaces and special characters.

---

## 2) Conditions `if/elif/else`: Files, Numbers, Strings

Basic syntax:
```bash
if [ condition ]; then
  # actions
elif [ other_condition ]; then
  # actions
else
  # fallback option
fi
```

**File/directory** checks:
```bash
if [ -d "/etc" ]; then echo "/etc is a directory"; fi
if [ -f "$config_file" ]; then echo "File $config_file exists"; fi
if [ -r "$config_file" ] && [ -s "$config_file" ]; then echo "Readable and not empty"; fi
```

**Numeric** comparisons: `-eq`, `-ne`, `-lt`, `-gt`, `-le`, `-ge`
```bash
if [ "$files_count" -gt 0 ]; then echo "Files exist"; else echo "Empty"; fi
```

**String** comparisons:
```bash
if [ "$MODE" = "prod" ]; then echo "Production mode"; fi
if [ "$A" != "$B" ]; then echo "Strings are different"; fi
[ -z "$s" ] && echo "Empty string" || echo "Non-empty"
```

---

## 3) Script Parameters: `$1`, `$@`, `$#`

Call: `./setup.sh MyDir admin` → `$1="MyDir"`, `$2="admin"`

Inside script:
```bash
target_dir="${1:-/opt/app}"   # default value
user_group="${2:-appgrp}"
echo "dir=$target_dir, group=$user_group"
echo "parameter count: $#"
```

Difference **`$@` vs `$*`**:
- **`"$@"`** — list of **separate** arguments (correct for loops).
- **`"$*"`** — all arguments as **one line** (rarely needed).

```bash
# Correct and safe:
for arg in "$@"; do
  echo "arg=$arg"
done
```

---

## 4) Interactive Input: `read`

With prompt:
```bash
read -rp "Enter directory name: " target_dir
```

Hidden input (e.g., password):
```bash
read -rsp "Enter password: " user_password
echo    # line break after hidden input
```

Reading lines from file/pipe (working with spaces and backslashes):
```bash
while IFS= read -r line; do
  printf 'line: %s\n' "$line"
done < input.txt
```

---

## 5) `for` and `while` Loops

**for** over parameters or files:
```bash
for p in "$@"; do
  echo "param=$p"
done

for f in *.log; do
  [ -e "$f" ] || continue   # skip if pattern didn't match
  gzip -9 -- "$f"
done
```

**while** with condition/infinite loop:
```bash
count=0
while [ "$count" -lt 3 ]; do
  echo "count=$count"
  count=$((count+1))
done

# monitoring state with pause
while ! systemctl is-active --quiet nginx; do
  echo "Waiting for nginx start..."; sleep 1
done
echo "nginx is active"
```

`break` — immediate exit from loop; `continue` — move to next iteration.

---

## 6) Arithmetic `(( ))`

Arithmetic operations and increment/decrement:
```bash
sum=0
score=7
sum=$(( sum + score ))
(( sum++ ))
(( sum >= 10 )) && echo "Enough" || echo "Not yet"
```

---

## 7) `[[ ... ]]` vs `[ ... ]`

- `[[ ... ]]` — extended Bash syntax (not POSIX). Convenient for **patterns** and safer regarding some expansion cases.
- Patterns in `[[ ... ]]`:
```bash
if [[ "$fname" == *.yml ]]; then echo "YAML"; fi
if [[ "$text" == *"error"* ]]; then echo "Found word 'error'"; fi
```
- In most cases **continue quoting variables**: this is a good habit and avoids surprises.
- For maximum portability (POSIX scripts) use `[ ... ]`.

---

## 8) Practical (20–30 min)

1) **Log filter by level**
```bash
#!/usr/bin/env bash
set -euo pipefail

level="${1:-ERROR}"
read -rp "Log path: " log
[ -f "$log" ] || { echo "No file: $log" >&2; exit 1; }
grep -i "$level" -- "$log" | wc -l
```

2) **Backup directories passed as parameters**
```bash
#!/usr/bin/env bash
set -euo pipefail

dest="${1:-/tmp/backup}"; shift || true
mkdir -p "$dest"
for dir in "$@"; do
  [ -d "$dir" ] || { echo "Skip: $dir — not a directory"; continue; }
  base="$(basename "$dir")"
  tar -czf "$dest/${base}-$(date +%F).tgz" -C "$(dirname "$dir")" "$base"
done
```

3) **Config check**
```bash
#!/usr/bin/env bash
set -euo pipefail

for f in /etc/*.conf; do
  [ -s "$f" ] && echo "OK: $f" || echo "WARN: $f is empty"
done
```

---

## 9) Common Mistakes and Tips

- **`"$@"` vs `"$*"`**: for iterating arguments use **`"$@"`**.
- Missing **quoting** of variables → breaking on spaces/characters.
- Mixing `sh`/`bash`: constructs `[[ ]]`, `(( ))`, `==` — specific to Bash.
- Unhandled errors: enable **`set -euo pipefail`**, add checks `|| exit 1`.
- Wrong tests: `-f` (file), `-d` (directory), `-x` (executable), `-s` (non-empty).
- Reading files in loop: use `IFS= read -r` instead of `for line in $(cat ...)`.
- Locale/sourcing: for numbers use arithmetic `(( ))`, not external `expr`.

---

## 10) Control Questions

1. How to set **default value** for `$1`?
2. How do `"$@"` and `"$*"` differ?
3. What are **file checks** and when are they useful?
4. How to secretly read user password?
5. Give example of **while** loop that waits for service state.
6. What is `[[ ... ]]` used for and what's the difference with `[ ... ]`?

---

## 11) Analogy: "Conductor and Orchestra"

- **Variables** — sheet music parts for instruments: write once — use many times.
- **if/elif/else** — conductor gestures: who plays under which conditions.
- **Parameters** — requests before concert: "today faster/slower".
- **`read`** — rehearsal with questions to musicians (interactivity).
- **for/while** — repeating sections of piece or running through all instruments.
- **Arithmetic** — calculating tempo and beat.
- **`[[ ]]`** — extended tool for checks and "nuances" in score.

---

## 12) Cheat Sheet

```bash
# Variables and substitution
VAR="value"; echo "$VAR"
NOW="$(date -Is)"; N=$((N+1))

# Conditions
[ -f file ] && echo "file" || echo "no"
if [[ "$s" == *pattern* ]]; then ...; fi

# Parameters
echo "argc=$#"; for a in "$@"; do echo "$a"; done
name="${1:-world}"

# read
read -rp "Enter value: " val
read -rsp "Password: " pass; echo

# Loops
for f in *.log; do [ -e "$f" ] || continue; done
while ! ping -c1 host >/dev/null 2>&1; do sleep 1; done

# Arithmetic
(( i=0, i<5, i++ )) || true

# Safety
set -euo pipefail
```

---

> **Conclusion:** These techniques turn Bash scripts into reliable automation tools. Start with small scenarios, follow safe practices (quoting, `set -euo pipefail`), and gradually increase complexity — when needed, supplement Bash with languages like **Python** or configuration tools (**Ansible**).

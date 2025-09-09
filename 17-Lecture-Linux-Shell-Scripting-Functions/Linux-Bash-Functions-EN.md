# Lecture: **Functions in Bash — Structure, Parameters, Return Values**

> The final key element of Bash scripting — **functions**. They structure code, eliminate duplication (DRY) and increase reliability. We'll explore syntax, parameters, boolean values, returning results and practical patterns.

---

## Learning Objectives
After the lecture you will be able to:
- Declare and call **functions** in different styles.
- Pass **parameters** to functions and handle them safely.
- Write **comments**, document function behavior.
- Work with **boolean logic** (`true`/`false`, exit codes).
- **Return values**: numeric codes (`return`) and data via `echo/printf`.
- Build libraries of small, **reusable** functions in DevOps scripts.

---

## Lecture Plan
1. Why functions: readability and DRY
2. Declaration and call: syntax and style
3. Function parameters: `$1`, `$@`, `shift`, default values
4. Comments and self-documentation
5. Boolean logic in Bash: `true/false`, exit codes
6. Returning values: `return` (0–255) vs `echo/printf`
7. Examples and DevOps patterns
8. Practical (20–30 min)
9. Common mistakes and tips
10. Control questions
11. "Chef and recipes" analogy
12. Cheat sheet

---

## 1) Why Functions
- **Readability**: Break long scripts into understandable blocks with names (`setup_user`, `deploy_app`).
- **DRY**: Logic is written once and reused.
- **Testability and maintenance**: Easier to localize errors and change individual parts.

---

## 2) Declaration and Call

Two equivalent declaration styles:
```bash
mysay_1() {
  printf 'Hello, %s\n' "${1:-world}"
}

function mysay_2 {
  printf 'Hello, %s\n' "${1:-world}"
}
```

> Style recommendations:
> - Use **`name()`** (more concise).
> - Inside function for temporary variables — **`local`**.
> - Single exit — `return` or end of function body.

Function call — simply by name:
```bash
mysay_1 "Bash"    # → Hello, Bash
mysay_2           # → Hello, world
```

---

## 3) Function Parameters

Inside function available:
- `$1`, `$2`, … — positional parameters
- `$#` — parameter count
- `"$@"` — **argument list** (each separately, preserves spaces)
- `"$*"` — all arguments as **one line** (used rarely)

Safe processing pattern:
```bash
create_file() {
  local path="${1:-}"
  local mode="${2:-644}"   # default value
  [ -n "$path" ] || { echo "path is required" >&2; return 2; }
  : > "$path" || return 3
  chmod "$mode" "$path"
}
```

Optionally can "shift" processed parameters:
```bash
myfn() {
  local opt="${1:-default}"; shift || true
  # now "$@" — remaining parameters
}
```

> Recommendation: **<= 5 parameters** per function. If more — break into smaller functions or use `getopts` options.

---

## 4) Comments and Self-Documentation

Single-line comments:
```bash
# Create empty file and apply permissions
create_file "/tmp/demo.txt" 600
```

Pseudo "block" comments (heredoc to `:`):
```bash
: <<'DOC'
This block is not executed, convenient for temporary notes
or detailed algorithm explanation.
DOC
```

Docstring pattern in function:
```bash
backup_dir() {
  # usage: backup_dir <src_dir> <dest_dir>
  :
}
```

---

## 5) Boolean Logic: `true`/`false`, Exit Codes

In Bash truth is determined by **exit code**: `0` — **true/success**, `!=0` — **false/error**.
```bash
is_readable() {
  [[ -r "$1" ]]
}

if is_readable "/etc/hosts"; then
  echo "OK"
else
  echo "NO"
fi
```

Commands `true` and `false` simply return 0 or 1:
```bash
true   # success
false  # error
```

---

## 6) Returning Values

- `return <num>` — returns **numeric code** (0–255). Suitable for "yes/no" or error classification.
- To return **data** (string/number of any size) — **output** them (`echo`/`printf`) and read in call via command substitution `$(...)`.

Example: **success code** and **string value**:
```bash
sum2() {           # returns "data" via stdout
  local a="${1:-0}" b="${2:-0}"
  printf '%s' "$((a + b))"
}

add_user_safe() {  # returns success/error code
  local name="${1:-}"
  [ -n "$name" ] || return 2
  id -u "$name" >/dev/null 2>&1 && return 0
  sudo adduser --disabled-password --gecos '' "$name"
}

res="$(sum2 3 9)"           # res="12"
if add_user_safe "svcapp"; then
  echo "user ok"
else
  echo "user failed with $?"   # error code
fi
```

> Remember: `return` is limited to 0–255; larger values will be truncated. For complex results — print JSON/text and parse.

---

## 7) Examples and DevOps Patterns

**Universal logger:**
```bash
log() { printf '%s [%s] %s\n' "$(date -Is)" "${1:-INFO}" "${2:-}"; }

log INFO  "Deploy start"
log WARN  "Config missing — using default values"
log ERROR "Database connection failure"
```

**Dependency check:**
```bash
require_cmd() { command -v "$1" >/dev/null 2>&1 || { echo "need $1" >&2; return 127; }; }
require_cmd tar
require_cmd curl
```

**Waiting for port/service availability:**
```bash
wait_for_tcp() { # usage: wait_for_tcp <host> <port> [timeout_s]
  local h="$1" p="$2" t="${3:-30}" start end
  start=$(date +%s)
  while ! (echo >"/dev/tcp/$h/$p") >/dev/null 2>&1; do
    sleep 1
    end=$(date +%s)
    (( end - start >= t )) && { echo "timeout $h:$p" >&2; return 1; }
  done
}
```

**Idempotent directory creation with permissions:**
```bash
ensure_dir() {
  local d="$1" mode="${2:-750}"
  [ -d "$d" ] || sudo mkdir -p -- "$d" || return
  sudo chmod "$mode" -- "$d"
}
```

---

## 8) Practical (20–30 min)

1) **Function `farewell` and `try`**
```bash
farewell() { echo "Error: $*" >&2; exit 1; }
try() { "$@" || farewell "failed: $*"; }
```

2) **Directory backup**
```bash
backup_dir() { # usage: backup_dir <src> <dest_dir>
  local src="$1" dest="$2" base ts
  [ -d "$src" ] || { echo "no src: $src" >&2; return 2; }
  mkdir -p -- "$dest"
  base="$(basename "$src")"; ts="$(date +%F-%H%M%S)"
  tar -czf "$dest/${base}-${ts}.tgz" -C "$(dirname "$src")" "$base"
}
```

3) **Config validation**
```bash
check_conf() { # usage: check_conf <file>
  local f="$1"
  [[ -s "$f" && -r "$f" ]] || { echo "bad conf: $f" >&2; return 3; }
}
```

4) **Metrics collection**
```bash
mem_free() { awk '/MemFree/ {print $2}' /proc/meminfo; }   # kB
cpu_cores() { nproc; }
echo "free_kb=$(mem_free) cores=$(cpu_cores)"
```

---

## 9) Common Mistakes and Tips

- **Parameters not quoted** in functions → breaking on spaces/special characters. Always `"$1"`, `"$@"`.
- Confusing **`return` (code)** with data return → for data use `echo/printf`.
- Missing `local` → global variables "leak" into script.
- Too **many parameters** → break into smaller functions or use `getopts`.
- Error handling inside pipelines: enable `set -euo pipefail` at script start.
- Overusing `sudo` in functions → delegate privileges precisely or check `EUID`.
- Missing **usage lines** and examples → hard to maintain after a month.

---

## 10) Control Questions

1. How do the two function declaration styles in Bash differ?
2. How to safely read function parameters and set default values?
3. What's the difference between **`return`** and **`echo/printf`** in "return value" context?
4. How to check function **truth** in `if ...; then` expression?
5. Why is the **`local`** keyword needed inside functions?
6. What advantages do comments and usage blocks provide for script maintenance?

---

## 11) Analogy: "Chef and Recipes"

- **Function** — separate **recipe** (sauce, marinade).
- **Definition** of recipe — writing on card; **call** — start cooking.
- **Parameters** — recipe ingredients (tomatoes/basil).
- **Comments** — margin notes ("add salt to taste").
- **Return** — ready component (or signal "something went wrong" by code).

---

## 12) Cheat Sheet

```bash
# Declaration
myfn() { :; }             # minimal function
function other { :; }      # alternative style

# Parameters
myfn "arg1" "arg 2"
myfn() { local a="${1:-}"; shift || true; for x in "$@"; do echo "$x"; done; }

# Boolean logic
is_dir() { [[ -d "$1" ]]; }  # returns 0/1
if is_dir "/tmp"; then echo OK; fi

# Return
retcode() { return 5; }      # code → $?
value()   { printf '%s' "data"; }  # data → stdout
v="$(value)" ; retcode; rc=$?

# Comments
# this is comment
: <<'NOTE'
block "comment" via heredoc
NOTE
```

---

> **Conclusion:** Functions are the foundation of structured Bash scripts. Use short, focused functions with clear parameters, document them, return codes for flow control and output data via stdout — this way your scripts become clean, reliable and easy to maintain.

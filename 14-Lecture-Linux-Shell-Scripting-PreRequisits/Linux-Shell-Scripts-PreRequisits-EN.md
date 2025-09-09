# Lecture: **Piping and Redirection in Linux**

> In this lecture we'll explore how to connect commands with **pipes** (`|`) and how to **redirect** input/output streams (`>`, `>>`, `2>`, `2>&1`). These are basic techniques for efficient terminal work, log analysis and automation.

---

## Learning Objectives
After the lecture you will be able to:
- Explain the difference between **stdin**, **stdout**, **stderr**.
- Build **pipelines** from multiple commands (`cmd1 | cmd2 | cmd3`).
- Use **redirection** to file: `>`, `>>`, `2>`, `2>>`, `2>&1`, `&>`.
- Combine filtering (`grep`, `less`, `sort`, `uniq`, `wc`) with pipelines.
- Execute **multiple commands** in one line (`;`, `&&`, `||`).
- Apply practical patterns for log analysis and debugging.
  
---

## Lecture Plan
1. Piping: `|` — pass stdout to next command
2. Redirection: `>`, `>>` — write to file
3. Standard streams: **stdin/stdout/stderr** and their management
4. Multiple commands in one line: `;`, `&&`, `||`
5. Practical (15–25 min)
6. Common mistakes and tips
7. Control questions
8. "Factory and conveyor" analogy
9. Operator cheat sheet

---

## 1) Piping (`|`) — Connecting Commands

Each command has **input (stdin)** and **output (stdout)**. The `|` operator passes **stdout** of previous command to **stdin** of next command.

### Examples
**Paging through large volumes (`less`):**
```bash
cat /var/log/syslog | less
ls /usr/bin | less
history | less
```
`less`: **Space** — forward, **B** — back, **Q** — exit, **/** — search, **N**/**n** — previous/next match.

**Filtering lines (`grep`):**
```bash
history | grep sudo
history | grep "sudo chmod"
ls /usr/bin | grep -i java
cat config.yml | grep port
```
Combining filtering and pagination:
```bash
history | grep "sudo chmod" | less
```

**Typical useful chains:**
```bash
# Top most used commands from history
history | awk '{print $2}' | sort | uniq -c | sort -nr | head

# Live viewing of errors in service logs
journalctl -u nginx -f | grep -i "error"
```

> Tip: Many utilities read from **stdin** if you specify `-` instead of filename (where supported).

---

## 2) Redirection (`>`, `>>`) — Writing to File

- `>` — **overwrite** file (create or clear).
- `>>` — **append** to end, without erasing existing content.

Examples:
```bash
history | grep sudo > sudo_commands.txt
history | grep rm   >> sudo_commands.txt  # add, not overwriting
date > /tmp/run_at.txt                     # simple example
```

Useful to save intermediate pipeline results for further analysis.

**Write to file and see on screen simultaneously — `tee`:**
```bash
journalctl -u nginx | grep -i error | tee "logs_errors.txt"
# append instead of overwrite:
journalctl -u nginx | grep -i error | tee -a "logs_errors.txt"
```

---

## 3) Standard Streams: **stdin / stdout / stderr**

- **stdin (0):** where command reads from (typically — keyboard or previous command in pipeline).
- **stdout (1):** where command writes normal output (typically — screen).
- **stderr (2):** where command writes **errors** (typically — screen, **doesn't go** to pipeline unless redirected).

### Error Management Examples
```bash
# Only errors to file
ls /root 2> errors.log

# Normal output to one file, errors — to another
grep -R "TODO" . > out.log 2> err.log

# Merge stderr into stdout (common stream → to file)
mycmd > all.log 2>&1
# short in bash: mycmd &> all.log

# Hide everything (both stdout and stderr)
mycmd > /dev/null 2>&1
```

> Note: `cmd1 | cmd2` passes **stdout** of `cmd1`. If `cmd1` prints errors to **stderr**, they **won't reach** `cmd2` until you do `2>&1`.

---

## 4) Multiple Commands in One Line

- `;` — execute **sequentially**, regardless of previous success:
  ```bash
  clear; sleep 1; echo "Commands executed"
  ```
- `&&` — execute **next only if previous successful** (code 0):
  ```bash
  make build && echo "OK"
  ```
- `||` — execute **next if previous failed** (code ≠ 0):
  ```bash
  make build || echo "Build error"
  ```

> Tip (advanced): for scripts useful `set -o pipefail` — then failure of **any** command in pipeline will cause error of entire pipeline.

---

## 5) Practical (15–25 min)

1. **Paging large logs**
   ```bash
   cat /var/log/syslog | less
   ```
2. **Filtering history**
   ```bash
   history | grep "sudo" | less
   history | grep -i "apt install" > installs.txt
   ```
3. **Counting matches**
   ```bash
   dmesg | grep -i "usb" | wc -l
   ```
4. **Write and view simultaneously**
   ```bash
   journalctl -u ssh | grep -i "fail" | tee -a ssh_failures.log
   ```
5. **Separate normal output and errors**
   ```bash
   grep -R "TODO" /etc > found.txt 2> errors.txt
   ```
6. **Conditional execution**
   ```bash
   ls /root > /dev/null 2>&1 && echo "Access granted" || echo "Access denied"
   ```

> *Result:* confidently build pipelines, manage streams and write results to files for analysis.

---

## 6) Common Mistakes and Tips

- **Confused `>` and `>>`** → accidental overwrite. If worried — first use `>>`.
- **Errors don't pass through `|`** → add `2>&1` if need to process them in pipeline.
- **Missing quotes** around search phrase with spaces → empty or wrong result.
- **Pipes require "streaming" tools**: some commands expect files, but many can read `stdin` with `-`.
- **Performance**: complex pipelines over very large data can load CPU/disk — add filters at early stages (`grep | grep -v ... | head`).

---

## 7) Control Questions

1. How do **stdout** and **stderr** differ? How to direct errors to pipeline?
2. When to use `>` and when `>>`?
3. What is `tee` for and how does `tee -a` differ?
4. What does `;` do compared to `&&` and `||`?
5. How to count number of lines with "error" in service journal from last run? (example command chain)

---

## 8) Analogy: "Factory and Conveyor"

- **Piping (`|`)** — conveyor belt between machines: result of **history** goes to **grep** processing, then to sorting/viewing **less**.
- **Redirection (`>`, `>>`)** — diverting conveyor to **warehouse (file)**: single `>` — clear warehouse, double `>>` — carefully **add**.
- **stdout** — "product without defects", **stderr** — "defects".
- **`;`/`&&`/`||`** — set of machine instructions: execute sequentially, only on success or on failure.

---

## 9) Operator Cheat Sheet

```
|        # pipe: stdout of left → stdin of right
> FILE   # overwrite file
>> FILE  # append to end
2> FILE  # errors (stderr) to file
2>> FILE # append errors to end
&> FILE  # stdout and stderr to file (bash)
2>&1     # merge stderr into stdout
< FILE   # feed file as stdin
tee F    # show on screen and write to file
;        # execute next command always
&&       # execute next on success
||       # execute next on error
```

---

> **Brief summary:** Pipes allow building **processing streams** right in terminal, and redirection — **save or separate** output streams. Master `|`, `>`, `>>`, `2>`, `2>&1`, `tee` — and your daily Linux tasks will become many times faster.

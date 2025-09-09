# Lecture: **Environment Variables in Linux (ENV): What They Are, How to View and Manage**

> In this lecture we'll explore **environment variables** — a key mechanism for user and system configuration. We'll learn to view them, create them, make them persistent, modify `PATH` and safely use them in applications and scripts.

---

## Learning Objectives
After the lecture you will be able to:
- Explain what **ENV variables** are and how they differ from regular bash variables.
- View and filter environment: `printenv`, `env`, `grep`, `less`.
- Create temporary and **persistent** variables (`~/.bashrc`, `/etc/environment`) and **delete** them (`unset`).
- Understand **inheritance** of environment to child processes.
- Properly modify **`PATH`**, add custom directories with scripts.
- Use ENV variables for application configuration (secrets, URLs, modes).

---

## Lecture Plan
1. What are environment variables
2. How to view and access variables
3. Common variables: `USER`, `HOME`, `SHELL`, `EDITOR`, `PATH`, `LANG`
4. Custom variables: creation, export, deletion
5. **Persistent** variables: `~/.bashrc`, `~/.profile`, `/etc/environment`
6. `PATH`: command search, order, risks and tips
7. Environment inheritance and running commands with modified ENV
8. Practical (15–25 min)
9. Common mistakes and security
10. Control questions
11. "Office and notes" analogy
12. Cheat sheet

---

## 1) What Are Environment Variables

- **ENV variables** — **key=value** pairs that describe user/system settings.
- Available **throughout the process tree** of current shell (inherited by child processes).
- Names usually in **UPPERCASE** with underscores: `HOME`, `DB_USERNAME`.
- **Dynamic**: OS sets basic values, user/scripts can modify them for themselves.

Difference from simple bash variable: regular variable is **local to shell**; ENV variable — **exported** and available to child processes.

---

## 2) How to View and Access Variables

View **entire** environment:
```bash
printenv | less
env | less
```

Search/filter:
```bash
printenv | grep -i "path"
```

Specific variable:
```bash
printenv USER
echo "$HOME"
```

Usage in commands/scripts — like regular bash variables: `"$VAR"`.

---

## 3) Common Variables

- **`USER`** — current username.
- **`HOME`** — home directory (e.g., `/home/{userName}`).
- **`SHELL`** — default shell (e.g., `/bin/bash`, `/usr/bin/zsh`).
- **`EDITOR`** — default editor (e.g., `vim`, `nano`).
- **`PATH`** — list of directories (separated by `:`) where executable files are searched.
- **`LANG`/`LC_*`** — language/locale; affect sorting, date format, etc.
- **`PWD`** — current directory; **`OLDPWD`** — previous.

---

## 4) Custom Variables: Creation, Export, Deletion

### Temporarily (only in current session)
```bash
DB_USERNAME="my_app_user"         # regular shell variable
export DB_USERNAME                # make it "environment"
# or in one line:
export DB_PASSWORD="s3cr3t"
```

> After closing terminal these values will disappear.

### Delete/revoke
```bash
unset DB_PASSWORD                 # removes variable from current session
export -n DB_USERNAME             # revoke "export" (keep as shell variable)
```

### Reassign
```bash
DB_NAME="app_db_new"
export DB_NAME
```

---

## 5) Persistent Variables

### For **user** (bash)
Add to `~/.bashrc`:
```bash
export DB_USERNAME="my_user"
export DB_URL="postgres://localhost:5432/app"
```
Then reload config:
```bash
source ~/.bashrc    # or open new terminal
```

> For `zsh` — use `~/.zshrc`.  
> **Login vs interactive shell**: in some systems variables are useful to set in `~/.profile`/`~/.bash_profile` (for login sessions).

### For **entire system** (all users)
- File **`/etc/environment`** (only `KEY=VALUE`, **without** bash syntax and substitutions):
  ```
  DB_HOST=db.internal
  APP_ENV=prod
  ```
- Or scripts in `/etc/profile.d/*.sh` (executed for login shell).

> For **systemd** services: in unit files `Environment=` or `EnvironmentFile=`.

---

## 6) `PATH`: Mechanics and Tips

`PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:...`  
Search goes **left to right** — first found command is executed.

Add custom directory with user scripts:
```bash
mkdir -p "$HOME/bin"
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

> Security tips:
> - Don't put **`.`** (current directory) at beginning of `PATH` — risk of command substitution.
> - Control directory order: put desired paths **to the left**.
> - Check which command you're calling: `type -a cmd`, `command -v cmd`.

---

## 7) Inheritance and Running with Modified Environment

- Child processes **inherit** ENV from parent shell; reverse — no.
- One-time for single command:
  ```bash
  APP_ENV=prod DB_URL="postgres://..." ./run.sh
  ```
- Run with **clean** environment:
  ```bash
  env -i PATH="/usr/bin:/bin" ./script.sh
  ```

---

## 8) Practical (15–25 min)

1) **Review environment**
```bash
printenv | less
printenv | grep -E '^(USER|HOME|SHELL|PATH)='
```

2) **Create and use custom variables**
```bash
export API_TOKEN="tok_123"
echo "My token: $API_TOKEN"
unset API_TOKEN
```

3) **Make persistent**
```bash
echo 'export FAVORITE_EDITOR="nano"' >> ~/.bashrc
source ~/.bashrc
echo "$FAVORITE_EDITOR"
```

4) **Add script to PATH**
```bash
mkdir -p "$HOME/bin"
cat > "$HOME/bin/hello" <<'SH'
#!/usr/bin/env bash
echo "Hello from $0"
SH
chmod +x "$HOME/bin/hello"
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
hello
```

---

## 9) Common Mistakes and Security

- **Confusing** regular variable and ENV: without `export` child processes **don't see** it.
- Editing **`/etc/environment`** with shell syntax (e.g., `"$HOME"`): there works **only** `KEY=VALUE`.
- Storing **secrets in bash history**/repository: use secret managers or `.env` files with proper permissions (`chmod 600`) and **don't add them to Git**.
- Wrong `PATH`: duplicates, "dot" at beginning, accidental overwrite `PATH="..."` without old values.
- Changes in `~/.bashrc` without `source` → not applied to current session.

---

## 10) Control Questions

1. How does ENV variable differ from regular bash variable?
2. How to view value of `HOME` and full list of ENV variables?
3. How to make variable **persistent** for user? For entire system?
4. How to **one-time** pass variables only for single command?
5. What is `PATH` and how to safely add `$HOME/bin` to beginning of search?
6. Why is it dangerous to have `.` at beginning of `PATH`?

---

## 11) Analogy: "Office and Notes"

- **ENV variables** — your **personal notes** and workspace settings.
- `printenv` — "quick desk overview".
- Temporary `export` — sticky notes on monitor (work until end of day/session).
- `~/.bashrc` — **diary/organizer** (remains after reboot).
- `source ~/.bashrc` — "reread diary in the morning".
- `PATH` — **office map** with tool locations; don't let suspicious things be first in list.

---

## 12) Cheat Sheet

```bash
# Viewing
printenv                # or: env
printenv VAR            # value of specific variable

# Creation (session)
VAR=value
export VAR              # make "environment"
export VAR=value        # short

# Deletion / revoke export
unset VAR
export -n VAR

# Persistent (bash)
echo 'export VAR="value"' >> ~/.bashrc
source ~/.bashrc

# PATH
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc

# One-time for command
APP_ENV=prod DB_URL=postgres://... ./run.sh

# Clean environment
env -i PATH="/usr/bin:/bin" ./script.sh
```

---

> **Conclusion:** Environment variables are a universal way to manage configurations and secrets of different environments (dev/test/prod). Master `printenv`, `export`, `unset`, `PATH` and **persistence** rules — and settings will become predictable, secure and portable.

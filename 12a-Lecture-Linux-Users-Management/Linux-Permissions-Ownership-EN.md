# Lecture: **File Permissions and Ownership in Linux (rwx, chown, chmod)**

> Everything in Linux is **files**. Here we'll explore how **owners** (user/group) and **permissions** (`rwx`) work for files and directories, how to view them (`ls -l`) and change them (`chmod`, `chown`, `chgrp`). All examples use user **Harry** and group **griffindor**.

---

## Learning Objectives
After the lecture you will be able to:
- Read `ls -l` output and understand **types** and **permissions** of files/directories.
- Explain the difference between **owner-user** and **owner-group**.
- Change ownership (`chown`, `chgrp`) and permissions (`chmod`) in **symbolic** and **numeric** formats.
- Know specifics of permissions for **directories** (what `x` means, etc.).
- Practically apply secure permission patterns (`640`, `750`, etc.).

---

## Lecture Plan
1. Viewing permissions: `ls -l`, hidden files `-a`
2. File ownership: user and group
3. Changing ownership: `chown`, `chgrp`
4. Permission blocks and file type: `-`/`d` + `rwx`
5. Categories: **user / group / others**
6. Changing permissions: `chmod` (symbolic and numeric modes)
7. Practical (10–20 min)
8. Common mistakes and tips
9. Control questions
10. "House and rooms" analogy
11. Quick reference

---

## 1) Viewing Permissions (`ls -l`)

Show details (type, permissions, owners, size, date):
```bash
ls -l
```

Show **including hidden** (`.` at beginning):  
```bash
ls -al        # or: ls -l -a
```

Example output line:
```
-rw-r----- 1 Harry griffindor 1234 Sep  1 10:00 notes.txt
drwxr-x--- 2 Harry griffindor 4096 Sep  1 10:00 reports
^--------^   ^^^^ ^^^^^^^^^^
permissions  user  group
```

---

## 2) File Ownership

Each object has **two owners**:
- **Owner-user** — usually the one who created the file (e.g., **Harry**).
- **Owner-group** — primary group of author during creation (e.g., **griffindor**).

System files often belong to `root:root` — created by the system.

---

## 3) Changing Ownership

> Requires administrator rights (`sudo`).

**Change user and group at once:**
```bash
sudo chown Harry:griffindor test.txt
```

**Change only user:**
```bash
sudo chown Harry test.txt
```

**Change only group:**
```bash
sudo chgrp griffindor test.txt
```

**Recursively for directory:**
```bash
sudo chown -R Harry:griffindor /srv/project
sudo chgrp -R griffindor /srv/project
```

---

## 4) Permission Blocks and Types (`rwx`)

First character — **type**:
- `-` — regular file,
- `d` — directory,
- (others: `l` — symlink, `c/b` — device special files, etc.).

Then three triplets: `rwx` for **user** / **group** / **others**.  
- `r` (*read*) — read file / **list** directory,
- `w` (*write*) — modify file / **create/delete** in directory,
- `x` (*execute*) — execute file / **enter** directory (`cd`).

> For **directory** `x` means "allow entry/traversal", and `r` — "see content list". For full directory work usually need **`r+x`** (and often `w`).

---

## 5) User Categories

- **user** — owner-user (here: **Harry**),
- **group** — owner-group (here: **griffindor**),
- **others** — all others in the system.

Example:  
`-rw-r-----` → `rw-` (user) / `r--` (group) / `---` (others).

---

## 6) Changing Permissions (`chmod`)

### a) **Symbolic Mode**
Who we change: `u` (user), `g` (group), `o` (others), `a` (all).  
Action: `+` (add), `-` (remove), `=` (set exactly).

```bash
sudo chmod -x script.sh          # remove 'x' from all (equiv. a-x)
sudo chmod g-w report.pdf        # remove 'w' from group (griffindor)
sudo chmod u+x script.sh         # add execution for owner (Harry)
sudo chmod o+r readme.txt        # read for others
sudo chmod g=rwx shared          # for group exactly rwx (overwrite)
sudo chmod ug+rw,o-r secrets.txt # combinations for multiple categories
```

### b) **Numeric (Octal) Mode**
Encoding: `r=4`, `w=2`, `x=1`. Sum for **user / group / others**.

```bash
sudo chmod 777 file      # rwx rwx rwx (NOT recommended for production)
sudo chmod 750 app/      # rwx r-x --- (owner full; group read/execute; others — no)
sudo chmod 644 notes.txt # rw- r-- r-- (typical for text files)
```

### c) **Full Block Overwrite**
```bash
chmod g=rwx shared_dir
```

> Tip: For shared work directories useful:
```bash
sudo chown -R Harry:griffindor /srv/shared
sudo chmod -R 770 /srv/shared
sudo chmod g+s /srv/shared        # setgid: new files/dirs inherit directory group
```

---

## 7) Practical (10–20 min)

1. **Create structure and check permissions**
   ```bash
   mkdir -p ~/perm-demo/{docs,bin}
   touch ~/perm-demo/docs/notes.txt
   touch ~/perm-demo/bin/run.sh
   ls -l ~/perm-demo ~/perm-demo/docs ~/perm-demo/bin
   ```
2. **Set ownership to yourself/group**
   ```bash
   sudo chown -R Harry:griffindor ~/perm-demo
   ls -l ~/perm-demo/docs/notes.txt
   ```
3. **Make script executable and directories accessible for group**
   ```bash
   chmod u+x ~/perm-demo/bin/run.sh
   chmod 750 ~/perm-demo ~/perm-demo/docs ~/perm-demo/bin
   ```
4. **Set policy for group collaboration**
   ```bash
   chmod -R 770 ~/perm-demo/docs
   chmod g+s ~/perm-demo/docs
   ```
5. **Check summary**
   ```bash
   ls -ld ~/perm-demo ~/perm-demo/docs
   ls -l  ~/perm-demo/bin/run.sh
   ```

> *Result:* understand `ls -l` output, can change owners and permissions, know differences for files/directories.

---

## 8) Common Mistakes and Tips

- **Missing `x` on directory** → even with `r` can't enter (`cd`). Add `x`.
- **Too open permissions (`777`)** → security risks. Use minimally necessary.
- **Forgot `-R`** for trees → permissions/ownership not applied recursively.
- **Confusing file and directory permissions** — remember difference in `r/w/x` meanings.
- **Wrong owner/group** → service won't start or write logs. Check `ls -l` and `journalctl`.

---

## 9) Control Questions

1. What do `r/w/x` mean for **file** and for **directory**?
2. Who are **user/group/others** in `ls -l` output?
3. How does `chown` differ from `chgrp`?
4. How to add execution right **only** for file owner (Harry)?
5. What's the difference between `chmod 644` and `chmod 640`?
6. Why `g+s` on shared **griffindor** directory?

---

## 10) Analogy: "House and Rooms"

- `ls -l` — sign by doors with owners and rules.
- **Owner-user** — you (**Harry**), **owner-group** — your "family" (**griffindor**).
- **Others** — all visitors.
- `r` — can **peek**, `w` — **rearrange furniture**, `x` — **enter inside** (for folder) or "run device" (for file).
- `chmod` — changing rules; `chown/chgrp` — changing owners.

---

## 11) Quick Reference

```bash
# Viewing
ls -l            # long format
ls -al           # with hidden

# Ownership
sudo chown Harry:griffindor path
sudo chgrp griffindor path
sudo chown -R Harry:griffindor dir/

# Permissions (symbolic)
chmod u+x file
chmod g-w file
chmod a-rwx file
chmod ug+rw,o-r file

# Permissions (numeric)
chmod 644 file   # rw- r-- r--
chmod 750 dir    # rwx r-x ---
chmod 770 dir    # rwx rwx ---

# Directories for group
chmod g+s shared_dir   # setgid: inherit group
```

---

> **Brief summary:** Read `ls -l`, distinguish **owner-user** and **owner-group**, manage permissions through `chmod` (symbolic/numeric). For directories don't forget about `x` meaning. Use **griffindor** group for collaboration — and proper permissions will make access secure and predictable.

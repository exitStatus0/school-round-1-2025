# Lecture: **Users, Groups and Permissions in Linux**

> This lecture covers user categories (root, regular, service), groups, access control model (owner/group/others and `rwx`), as well as practice of creating users/groups, delegating rights through `sudo`, changing owners and access modes. Examples use placeholder `{userName}` for user home directory.

---

## Learning Objectives
After the lecture you will be able to:
- Explain the difference between **root**, **regular** and **service** users.
- Create/delete **users** and **groups**, manage membership (`adduser`, `addgroup`, `usermod`, `gpasswd`).
- Understand and apply **permission model** (`rwx` for owner/group/others) through `chmod`, `chown`, `chgrp`.
- Delegate administrative actions through **`sudo`** and safely manage access.
- Use **groups** as the recommended way to manage permissions in teams.
- Practically check what groups a user has and what permissions a file/directory has.

---

## Lecture Plan
1. User categories in Linux
2. Why multiple accounts are important (especially on servers)
3. Windows vs Linux approach comparison (account centralization)
4. Permission management: directly vs through groups
5. Practice: creating users/groups and basic commands
6. File and directory access permission model
7. Delegation through `sudo`
8. Practical (20–30 min)
9. Common mistakes and tips
10. Control questions
11. "Organization and departments" analogy
12. Glossary

---

## 1) User Categories in Linux

- **Root (superuser)** — UID `0`, full control over system. Used **only** for admin tasks.
- **Regular users** — intended for daily work. Home directories in **`/home/<name>`**, e.g.: `/home/{userName}`.
- **Service users** — separate accounts under which services (DB, web servers, etc.) run for **isolation** and security. **Never** run services as `root` without need.

> On one machine — one `root`, but many regular and service users.

---

## 2) Why Multiple Accounts Are Important (Especially on Servers)?

- **Security:** don't share `root` password. Work under regular user and elevate rights **precisely** through `sudo`.
- **Permission manageability:** flexible roles for different people/teams.
- **Traceability:** logging actions by specific user simplifies auditing and incident investigation.

---

## 3) Windows vs Linux: Account Centralization

- **Windows (Active Directory/domains)** — centralized user management, roaming profiles.
- **Linux (typically)** — local users on each machine. Centralization possible through LDAP/SSSD/FreeIPA/AD-integration, but this is **not built-in by default** in distributions.

---

## 4) Permission Management: Directly vs Through Groups

- Can assign rights **directly to users**, but **recommended** to do this through **groups**.
- **Group advantages:** convenience, scalability, flexibility. User can be member of **multiple groups** and inherit their permissions.

---

## 5) Practice: Users and Groups

### Where Accounts Are Stored
```bash
cat /etc/passwd     # user list
cat /etc/group      # group list
cat /etc/shadow     # password hashes (accessible only to root)
```
`/etc/passwd` (fields separated by `:`): **username:x:UID:GID:description:home:shell**. UID `0` — `root`.

### Creating User
```bash
sudo adduser harry                # interactive creation, password, /home/harry, group harry
sudo passwd harry                 # change (or set) password
```
> `adduser` (in Ubuntu/Debian) — friendly frontend to `useradd`. For scripts: `useradd` with needed options.

### Switching Users
```bash
su - harry       # login as harry (loads environment), needs harry password
su -             # login as root (needs root password, if allowed)
exit             # exit current user session
```

### Creating/Checking Groups
```bash
sudo addgroup griffindor
grep griffindor /etc/group
```

### Add User to Groups
```bash
sudo usermod -aG griffindor harry # add to secondary group (correct: -aG)
groups harry                      # check membership
```
> **Careful:** `usermod -G` **without** `-a` **overwrites** entire secondary group list.

### Change Primary Group/Delete Group
```bash
sudo usermod -g griffindor harry  # change primary group
sudo gpasswd -d harry griffindor  # remove harry from griffindor group
sudo delgroup griffindor          # delete group (if empty)
```

### User Deletion
```bash
sudo deluser harry
# or with home directory:
sudo deluser --remove-home harry
```

### Service User (System)
```bash
sudo useradd --system --home /var/lib/myapp --shell /usr/sbin/nologin --user-group myapp
```

---

## 6) File and Directory Access Permission Model

Each object has **owner (user)**, **group** and permissions for **others**. Permissions are `r` (read), `w` (write), `x` (execute).

View permissions:
```bash
ls -l
# -rw-r----- 1 harry griffindor  1234 Sep  1 10:00 notes.txt
# drwxr-x--- 2 harry griffindor  4096 Sep  1 10:00 reports/
```

Change owner/group:
```bash
sudo chown harry:griffindor notes.txt
sudo chgrp griffindor reports/
sudo chown -R harry:griffindor /srv/project
```

Change permissions (`chmod`):
```bash
chmod u+rw,g+r,o- notes.txt      # symbolic notation
chmod 640 notes.txt              # numeric (u=6,g=4,o=0)
chmod -R 750 reports/            # recursively for directory
```

Additional bits:
```bash
chmod g+s shared/    # setgid on directory: new files inherit directory group
chmod +t /tmp        # sticky bit: only owners can delete files (typical on /tmp)
# setuid/setgid on executable files used carefully for security reasons
```

`umask` defines **default mask** for new files/directories (typically 022/002 depending on policies).

---

## 7) Delegation Through `sudo`

Add user to `sudo` group (Ubuntu) or `wheel` (RHEL/Fedora):
```bash
sudo usermod -aG sudo harry  # or wheel
```

Edit policies safely through **`visudo`** (checks syntax) and fragments in `/etc/sudoers.d/`:
```
harry ALL=(ALL) NOPASSWD: /usr/bin/systemctl restart nginx
```
> Allow **only needed commands**; avoid blanket rights like `ALL` without need.

---

## 8) Practical (20–30 min)

1. **Overview of system users/groups**
   ```bash
   head -n 5 /etc/passwd
   head -n 5 /etc/group
   ```
2. **Create user and group**
   ```bash
   sudo adduser {userName}
   sudo addgroup griffindor
   sudo usermod -aG griffindor {userName}
   groups {userName}
   ```
3. **Configure project directory permissions**
   ```bash
   sudo mkdir -p /srv/app/{conf,logs}
   sudo chown -R {userName}:griffindor /srv/app
   sudo chmod -R 750 /srv/app
   sudo chmod g+s /srv/app/logs
   ```
4. **Check access**
   ```bash
   ls -l /srv/app
   sudo -u {userName} touch /srv/app/logs/test.log
   ```
5. **Delegate action through sudoers (demo)**
   ```bash
   sudo visudo   # add rule for passwordless service restart (on test machine)
   ```

> *Result:* know how to create users/groups, assign file/directory permissions and safely delegate admin actions.

---

## 9) Common Mistakes and Tips

- Using `usermod -G` without `-a` → **loss** of previous groups.
- Running services as `root` without need → **security risk**.
- Excessive rules in `sudoers` (`ALL` without restrictions) → **privilege escalation**.
- Forgot recursion `-R` for directories in `chown/chmod`.
- Wrong permissions on directories: directories need `x`, otherwise can't "enter" inside.
- Correctness of owner/group of service logs/data — common cause of failures after deployment.

---

## 10) Control Questions

1. How do root, regular and service users differ?
2. Why are permissions better managed through **groups**?
3. What does `usermod -aG` do and why can't we forget `-a`?
4. How to change file owner/group and permissions? Give example.
5. What are `setgid` on directory and `sticky bit` on `/tmp` for?
6. How to safely grant privileges through `sudo`? Where are rules edited?

---

## 11) Analogy: "Organization and Departments"

- **Director (root)** has full authority.
- **Employees (regular users)** work in their "offices" — home directories.
- **Service work (service users)** perform narrow tasks only with needed rights.
- **Departments (groups)** get access to resources; adding/removing employee from department instantly changes their permissions.
- **`sudo`** — temporary director's signature for specific action.

---

## 12) Glossary

- **UID/GID** — user/group identifiers.
- **`/etc/passwd`, `/etc/group`, `/etc/shadow`** — basic account files.
- **`adduser`/`useradd`, `addgroup`/`groupadd`** — creation tools.
- **`usermod`, `gpasswd`** — membership/property management.
- **`chown`, `chgrp`, `chmod`** — owners/groups/permissions.
- **`sudo`, `visudo`, `sudoers`** — privilege delegation.
- **`setgid`, `sticky bit`, `umask`** — additional access control mechanisms.

---

> **Brief summary:** Use **groups** for scalable permission management, **`sudo`** — for precise admin actions, and service users with minimal rights increase security. Understanding `rwx` model and basic commands — foundation of Linux administration.

# Lecture: **Linux File System — Structure, Directories and Comparison with Windows**

> This lecture covers the Linux file system hierarchy, purpose of key directories, differences from Windows, and practical navigation and troubleshooting techniques.

---

## Learning Objectives
After the lecture you will be able to:
- Explain the tree-like Linux hierarchy with **single root `/`** and differences from Windows disk model (**`C:\`**, **`D:\`**).
- Navigate **key system directories**: `/bin`, `/sbin`, `/lib`, `/usr`, `/usr/local`, `/opt`, `/etc`, `/var`, `/dev`, `/boot`, `/tmp`, `/media`, `/mnt`.
- Understand the purpose of **home directories** and **hidden files** (dotfiles).
- Know where **system programs** are installed and how package managers distribute files.
- Apply basic commands for exploring the file system and troubleshooting issues.

---

## Lecture Plan
1. Overview: Linux vs Windows file system structure
2. User home directory (`/home`)
3. System binaries and libraries: `/bin`, `/sbin`, `/lib`
4. `/usr` directory and its subdirectories
5. Where system installations live: `/usr/local` and `/opt`
6. Other important directories: `/boot`, `/etc`, `/dev`, `/var`, `/tmp`, `/media`, `/mnt`
7. File system interaction and role of package managers
8. Hidden files (dotfiles)
9. "Large library" analogy
10. Practical (15–25 min), common mistakes, control questions, glossary

---

## 1) Linux File System Structure

**Linux** has a **hierarchical tree-like structure** with a **single root directory `/`** from which all other directories and files originate.

**Windows** uses **multiple roots** represented by disk letters (**`C:\`**, **`D:\`** etc.) — historically this is related to the era of removable media.

| Platform | Root | Path Example |
|---|---|---|
| Linux | `/` | `/home/exitStatus0/Documents/report.txt` |
| Windows | `C:\`, `D:\` | `C:\Users\exitStatus0\Documents\report.txt` |

> **Conclusion:** in Linux **everything is under `/`**, regardless of physical or logical partitions: they simply "mount" at certain points in the tree.

---

## 2) Home Directory

- Each user has their own directory in **`/home`**: for example, **`/home/exitStatus0`**.
- This provides **user isolation** in multi-user systems (settings, programs, personal files).
- **User `root`** has a separate **home directory `/root`** (not in `/home`).  
- Typical subdirectories: `Desktop`, `Documents`, `Downloads`, `Pictures`, `Videos`, `Projects`, etc.

> **Tip:** convenient to use environment variable **`$HOME`** or `cd ~` command.

```bash
whoami
echo $HOME
cd ~
pwd
ls -la
```

---

## 3) System Binaries and Libraries

### `/bin` — **Binary**
Basic system commands available to all users and the system itself.  
Examples: `cat`, `cp`, `mv`, `echo`, `ls`, `mkdir`, `rm`.

### `/sbin` — **System Binary**
Critically important commands for administration; often require superuser privileges.  
Examples: `adduser`, `passwd`, `iptables` (or modern `nft`/`ip`), `shutdown`.

### `/lib` — **Library**
Dynamic libraries that executable files from `/bin`, `/sbin` etc. depend on.  
Components of one program can be separated: binary file in `/bin`/`/sbin`, and libraries in `/lib`.

> **Note:** In modern distributions, unification is often used where `/bin` and `/sbin` are links to corresponding ones in `/usr` (see below).

---

## 4) `/usr` Directory

Historically `/usr` meant **user**, and it contained user data before `/home` appeared. Today it's a large tree of **system** files in "user space": programs, libraries, documentation.

Common subdirectories:
- **`/usr/bin`** — most user commands (e.g., `ls`, `cp`, `grep`, `python`, `node`).
- **`/usr/sbin`** — system commands for administration.
- **`/usr/lib`** — libraries for programs from `/usr/bin` and `/usr/sbin`.
- **`/usr/share`** — architecture-independent data (documentation, locales, manuals, icons, etc.).
- **`/usr/include`** — headers for compilation (C/C++).

> Historically, duplication of `/bin` vs `/usr/bin`, `/sbin` vs `/usr/sbin` arose due to disk space limitations; today commands often physically live in `/usr/bin` etc.

---

## 5) System Program Installations: `/usr/local` and `/opt`

- **`/usr/local`** — place for **third-party programs** installed **system-wide** by administrator (Docker, Java, Python, Git).  
  Common paths:  
  - `/usr/local/bin` — binaries,
  - `/usr/local/lib` — libraries,
  - `/usr/local/share` — shared data/documentation.
  Such programs are usually available to **all users**.

- **`/opt`** — for **self-contained** applications that don't spread across multiple directories but install as a **single tree** (IDEs, browsers, tools with their own updater).  
  Example structure: `/opt/SomeApp/bin`, `/opt/SomeApp/lib`, `/opt/SomeApp/resources`.

- **Local (per-user) installations** are usually placed inside the user's **home directory** (e.g., `~/.local/bin`).

---

## 6) Other Important Directories

- **`/boot`** — files for system boot (kernel, initramfs, bootloader). **Don't modify** unless you understand the consequences.
- **`/etc`** — configuration files for system and services: network, users, passwords, `nginx`, `MySQL`, `ssh` configs, etc. Usually **text** and **editable**.
- **`/dev`** — file interfaces to devices (disks, tty, USB, audio/video). OS interacts with "hardware" through these **device files**.
- **`/var`** — variable data: logs (**`/var/log`**), caches (**`/var/cache`**), spools (**`/var/spool`**), databases of some services.
- **`/tmp`** — temporary files for programs. Usually cleaned automatically.
- **`/media`** — **automatic** mounting of external media (USB, external HDD, network shares).
- **`/mnt`** — **manual** mounting of file systems (administrator for temporary/one-time tasks).

> **Tip:** additionally useful `/proc` (virtual FS with process/kernel information) and `/run` (state of system services after boot).

---

## 7) File System Interaction

Most system folders **don't require direct user interaction**. File management for programs is usually handled by **package managers** or installers that:
- distribute files to correct locations,
- track them for **updates** and **removal**,
- coordinate **dependencies**.

```bash
# Debian/Ubuntu
apt show curl
apt install curl
dpkg -L curl           # where exactly package files are distributed

# RHEL/CentOS/Fedora
dnf info curl
dnf install curl
rpm -ql curl
```

---

## 8) Hidden Files (dotfiles)

- **Hidden files/directories** start with a dot: **`.name`** (e.g., `~/.bashrc`, `~/.config`, `~/.mozilla`).  
- Used for **configurations**, **scripts**, **caches**, profile settings, etc.
- Created automatically by programs or manually by user.
- Similar concept exists in Windows/macOS, but in Linux it's a **de facto standard** for user configs.

```bash
ls -A ~          # show all (including hidden)
grep -n "alias" ~/.bashrc
mkdir -p ~/.config/mytool
```

---

## 9) Analogy: "Large Library"

- **`/`** — **entrance** to the entire library.
- Each directory — **separate department/shelf** with clear purpose:  
  - `/home` — "reading rooms" for each user,  
  - `/bin` — "universal instructions" available to everyone,  
  - `/etc` — "administrative rules and settings".
- **Hidden files** — "librarian's service cards/notes" that don't bother visitors.

---

## 10) Practical (15–25 min)

> **Goal:** learn to quickly navigate the file system, find binaries, configs, and logs.

1. **Navigation and overview**
   ```bash
   pwd
   tree -L 1 /           # if no tree: sudo apt install tree
   ls -l /bin | head
   ls -l /usr/bin | head
   ls -l /sbin | head
   ```

2. **Finding commands and libraries**
   ```bash
   which ls
   whereis ls
   ldd "$(which ls)"     # show library dependencies of executable file
   ```

3. **Configurations and logs**
   ```bash
   ls -l /etc | head
   sudo ls -l /var/log | head
   sudo tail -n 100 /var/log/syslog  # on RHEL/centos: /var/log/messages
   ```

4. **Devices, mounting, temporary files**
   ```bash
   ls -l /dev | head
   lsblk
   mount | head
   df -h
   ls -l /tmp | head
   ```

5. **Home directory and dotfiles**
   ```bash
   cd ~
   ls -A | head
   grep -n "PATH" ~/.profile ~/.bashrc 2>/dev/null
   ```

> *Result:* you quickly navigate key places in Linux FS and know where to look for programs, configs, libraries, and logs.

---

## Common Mistakes and Tips

- **Editing `/boot` or system configs without understanding consequences** — can make system unbootable. Make **backups**.
- **Confusion between `/bin` and `/usr/bin`** — in modern systems these are often the same location due to unification. Look at `which`, `readlink -f`.
- **Mixing system and local installations** — agree on policy: **`/usr/local`** for custom system installations, **`/opt`** for monolithic applications, **`~/.local`** for user.
- **Ignoring logs** — problems often "speak" in **`/var/log`**; use `journalctl` on systems with systemd.

---

## Control Questions

1. Why does Linux have a **single root directory `/`**, while Windows has multiple disk roots?
2. How do **`/bin`** and **`/sbin`** differ?
3. What are **`/usr/local`** and **`/opt`** for and how do they differ?
4. Where to look for service **configs** and where for **logs**?
5. What are **dotfiles** and how to view them?

---

## Glossary

- **Root (`/`)** — top of Linux file system tree.
- **Home directory** — user's personal space: `/home/<name>`; for `root` — `/root`.
- **Binary file** — executable program file.
- **Library** — shared components used by programs.
- **Package manager** — tool for installing/updating/removing software (apt, dnf, pacman, etc.).
- **Mounting** — attaching file system/device to directory tree.
- **Dotfiles** — hidden files/directories starting with a dot (.).

---

> **Brief summary:** Linux file system is a logical tree with a single root. Understanding the purpose of key directories helps install programs faster, find configs, read logs, and effectively troubleshoot issues.

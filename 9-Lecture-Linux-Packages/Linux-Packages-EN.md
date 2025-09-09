# Lecture: **Software Installation in Linux — Package Managers, Repositories, apt/snap/PPA (Comparison with Windows)**

> This lecture covers the philosophy of software installation in Linux, the role of **package managers**, working with **repositories**, basic **apt** commands, alternatives like **snap** and **PPA**, as well as an overview of package managers in other distributions. We compare with the "wizard" (installers) approach in Windows.

---

## Learning Objectives
After the lecture you will be able to:
- Explain **why Linux prefers package managers** over separate installers.
- Describe what a **package** and **dependencies** are, and why package managers solve them.
- Perform basic **apt** operations: `update`, `search`, `install`, `remove`, system updates.
- Understand the purpose of **repositories** (`/etc/apt/sources.list` and `sources.list.d`).
- Evaluate when it's appropriate to use **snap** or **PPA**, and what risks this carries.
- Know what tools other distribution families use (Debian/Ubuntu vs Red Hat/Fedora, etc.).

---

## Lecture Plan
1. Philosophy of software installation in Linux
2. What is a software package and dependencies
3. Role of package managers
4. **apt** for Debian/Ubuntu: main commands
5. Repositories: where packages come from
6. When **apt** is not enough: **snap** and **PPA**
7. Recommended installation hierarchy
8. Other distributions and their package managers
9. Practical (15–25 min)
10. Common mistakes and tips
11. Control questions
12. "City and warehouses" analogy
13. Glossary

---

## 1) Philosophy of Software Installation in Linux

Unlike Windows, where you usually **download .exe/.msi** and go through a wizard, in Linux the typical path is **package managers**.  
Direct downloading of archives/binaries is possible, but for system installations this is **not recommended**: package managers better handle dependencies, security and updates.

---

## 2) What is a Software Package?

**Package** is a compressed archive with **executable files, libraries, configs, metadata**. Almost all programs have **dependencies** (other packages/libraries needed for operation).

---

## 3) Role of Package Managers

Package managers:
- **Automatically resolve dependencies.** For example, when installing Firefox it will pull all needed libraries (even if they have their own dependencies).
- **Distribute files** to correct places in the file system (binaries, libraries, configs, data).
- **Cleanly remove** software from all places (what's hard and risky to do manually).
- **Update** packages without manual reinstallation.
- Provide **centralized management point**: search, install, update, remove, check status.

---

## 4) `apt` (Advanced Package Tool) — Debian/Ubuntu

Most distributions have a built-in manager. In Ubuntu (based on Debian) — it's **`apt`**.

Main commands:
```bash
sudo apt update                       # update repository indexes
apt search <package_name>              # search for package
sudo apt install <package_name>        # install
sudo apt remove <package_name>         # remove (without configs)
sudo apt purge <package_name>          # remove with configs
sudo apt upgrade                      # update installed packages
sudo apt full-upgrade                 # update with possible dependency changes
sudo apt autoremove                   # remove unnecessary dependencies
```

> **`apt` vs `apt-get`:** both available; `apt` provides **more convenient output** and includes `search`. In daily work usually `apt` is sufficient.

---

## 5) Software Repositories: Where Packages Come From

- Repositories are **online "warehouses"** with hundreds/thousands of packages.
- Configured in: **`/etc/apt/sources.list`** and files in **`/etc/apt/sources.list.d/`**.
- The vast majority of software for your system **comes from official repositories**.
- Before installations do:
  ```bash
  sudo apt update
  ```

Useful to know:
```bash
apt-cache policy <package>   # source, versions, priorities
```

---

## 6) When `apt` is Not Enough: **GUI "Software"**, **snap**, **PPA**

### GUI "Software" (Ubuntu Software, etc.)
Graphical search/installation. Under the hood may use `apt`, **snap** (or other technologies).

### **snap** Package Manager
- **Snap** — newer format of **self-contained packages**: all dependencies in one "snap".
- Suitable for different distributions; **automatic updates**.
- Example:
  ```bash
  sudo snap install <package_name>
  ```
- **`apt` vs `snap`:** `apt` usually better for system integration (smaller downloads, shared dependencies). **Snap packages are larger** in size because they contain everything inside.

### Additional Repositories (**PPA** — Personal Package Archives)
- Add new package sources for `apt`. Convenient for **newer versions** or **niche software**.
- Example:
  ```bash
  sudo add-apt-repository ppa:<owner>/<archive>
  sudo apt update
  sudo apt install <package>
  ```
- **Warning:** PPA are **not verified** by distribution; check author reputation, signing and source.

> **Tip:** If package is not in official repositories and snap is unavailable — evaluate **PPA** risks. For development tools sometimes alternatives like official tar archives or language-level managers (e.g., `pipx`, `npm`, `asdf`) are convenient.

---

## 7) Recommended Installation Hierarchy

1. **`apt` from official repositories** (≈ 80–90% of cases).
2. **`snap`**, if `apt` doesn't provide needed version and snap is available.
3. **PPA** or other additional repositories — carefully, when needed.

---

## 8) Package Managers in Different Distribution Families

- **Debian family (Ubuntu, Debian, Linux Mint):** `apt` / `apt-get`.
- **Red Hat family (RHEL, CentOS, Fedora):** traditionally `yum`, in modern releases — **`dnf`** (often compatible with `yum`).  
- **Other examples (for erudition):** Arch — `pacman`, openSUSE — `zypper`, Alpine — `apk`.

> Although different tools have different syntax, **concept is the same**: take packages from configured repositories and **resolve dependencies**.

---

## 9) Practical (15–25 min)

> **Goal:** Practice complete `apt` workflow and try alternatives.

1. **Update indexes and view updates**
   ```bash
   sudo apt update
   apt list --upgradable
   ```

2. **Search and install package**
   ```bash
   apt search htop
   sudo apt install htop
   which htop && htop --version
   ```

3. **Removal and cleanup**
   ```bash
   sudo apt remove htop
   sudo apt autoremove
   ```

4. **Check sources**
   ```bash
   cat /etc/apt/sources.list | sed -n '1,20p'
   ls -1 /etc/apt/sources.list.d/
   ```

5. **Try snap (if available)**
   ```bash
   snap list
   sudo snap install btop   # or other available package
   ```

6. **Add/remove PPA (on test machine!)**
   ```bash
   sudo add-apt-repository ppa:<owner>/<archive>
   sudo apt update
   # ... package installation ...
   sudo add-apt-repository -r ppa:<owner>/<archive>
   sudo apt update
   ```

> *Result:* understand the cycle "update → install → update/remove", package sources and alternatives.

---

## 10) Common Mistakes and Tips

- **Skipping `sudo apt update`** before search/installation — you see outdated information.
- **Uncontrolled PPA addition** — security risks/version conflicts.
- **Confusion between `apt` vs `snap`** — clarify which tool is used (GUI "Software" may install snap).
- **Mixing sources** (official + questionable PPA) — may break dependency updates.
- **Ignoring command output** — `apt` clearly shows what exactly will be installed/removed.

---

## 11) Control Questions

1. Why are package managers the recommended way to install software in Linux?
2. What are **dependencies** and who manages them?
3. Tell about the difference between `apt install`, `apt remove`, `apt purge`, `apt autoremove`.
4. Why are **repositories** needed and where are they configured?
5. When is it appropriate to use **snap** or **PPA**? What risks?
6. What package managers do **Debian/Ubuntu** and **RHEL/Fedora** use?

---

## 12) Analogy: "Linux — City, Repositories — Warehouses"

- **Package manager (apt/yum/dnf)** — "new mail": finds delivery (package) in **warehouses (repositories)**, checks **completeness (dependencies)**, installs in needed "rooms" and **cleans without trace** when removing.
- **Official repositories** — large verified city warehouses.
- **`apt update`** — check **new arrivals** and updates.
- **`snap`** — "universal boxes" with everything inside: convenient, but heavier and takes more space.
- **PPA** — private warehouses of craftsmen/designers: **potentially useful**, but requires **caution**.
- **Different distributions** — different cities with their own delivery service, but **working principle is the same**.

---

## 13) Glossary

- **Package** — archive with program files, metadata and dependencies.
- **Dependencies** — other packages needed for program operation.
- **Package manager** — tool for installing/updating/removing (apt, yum/dnf, pacman, zypper, apk).
- **Repository** — package source for manager (online "warehouses").
- **`apt`** — Debian/Ubuntu package manager.
- **`snap`** — self-contained package format (with auto-updates).
- **`PPA`** — additional repository for Ubuntu/Debian.
- **`yum` / `dnf`** — Red Hat family package managers.

---

> **Brief summary:** Package managers are the standard and safe way to install software in Linux. Start with **`apt`** and official repositories; use **`snap`** or **PPA** only when needed, weighing size, security and maintainability.

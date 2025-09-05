# Lecture: Installing Linux Mint in VirtualBox

## Why is this needed?

In DevOps training, we need an environment where we can safely experiment with Linux without risking damage to the main operating system.  
For this, we use **VirtualBox** — a free hypervisor that allows creating virtual machines.  
And for the virtual OS, we'll choose **Linux Mint** — a simple and convenient distribution for starting work with Linux.

**Main goals:**
- Get a test environment for practicing Linux commands.
- Learn to work with virtual machines.
- Use a separate Linux for further DevOps tasks (Docker, Kubernetes, CI/CD).

---

## Required tools

1. [VirtualBox](https://www.virtualbox.org/) (hypervisor for creating virtual machines).
2. [Linux Mint ISO](https://linuxmint.com/download.php) (operating system image).
3. Computer with at least **8 GB RAM** and **50+ GB free space** on disk.

---

## Step-by-step installation guide

### 1. Download
- Download VirtualBox from the official website.
- Download Linux Mint ISO image (recommended version **Cinnamon** 64-bit).

### 2. Creating a virtual machine
1. Open VirtualBox → **New** → enter name (e.g., `LinuxMint-DevOps`).
2. Type: **Linux**, version: **Ubuntu (64-bit)**.
3. Allocate resources:
   - RAM: 4096 MB (recommended).
   - CPU: 2 cores.
4. Disk: create new **VDI**, dynamic size, 30–50 GB.

### 3. Connecting ISO
- In VM settings → **Storage** → connect ISO to **Optical Drive**.

### 4. Installing Linux Mint
1. Start VM.
2. In menu select **Start Linux Mint**.
3. Choose "Install Linux Mint".
4. During installation:
   - Language: English or Ukrainian.
   - Keyboard: US / UA.
   - Partitioning: "Erase disk and install Linux Mint" (used only within VM, won't affect your main system).
   - Username and password (e.g.: `devopsAdmin` / `devopsAdmin20250904`).

### 5. First launch
- After installation, eject ISO (Devices → Optical Drives → Remove disk).
- Restart VM.
- Login: enter with created user.

---

## What to pay attention to

- **Guest Additions**: install VirtualBox Guest Additions for better performance (screen resizing, clipboard).
- **Network**: check settings (NAT or Bridged). For DevOps tasks, **Bridged** is better to have a local IP address.
- **Snapshots**: use system snapshots before important experiments.
- **Resources**: don't allocate too little RAM/CPU, otherwise VM will work slowly.

---

## Summary

- We created a separate Linux environment for learning without harming the main OS.
- Linux Mint — convenient starter distribution for practicing DevOps.
- VirtualBox gives the ability to experiment, restore system from snapshots, and configure environment for our tasks.

> ✅ From the next lecture, we'll already be using the installed Linux for practicing commands, tools, and DevOps scenarios.

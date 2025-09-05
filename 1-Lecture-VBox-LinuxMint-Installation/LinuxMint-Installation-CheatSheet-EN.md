# 🐧 Linux Mint in VirtualBox — Cheat Sheet

> **⚡ Quick guide for installing Linux Mint in VirtualBox**

---

## 📋 1. Preparation

### Downloading ISO
- **Official website:** [https://linuxmint.com/download.php](https://linuxmint.com/download.php)
- **Recommended version:** Linux Mint Cinnamon 64-bit
- **File size:** ~2.5 GB

### System Requirements
- ✅ **VirtualBox** (already installed)
- ✅ **Free space:** min. 20 GB (recommended 40+ GB)
- ✅ **RAM:** minimum 2 GB (recommended 4+ GB)
- ✅ **CPU:** 2+ cores

---

## 🖥️ 2. Creating Virtual Machine

### Step 1: Basic Configuration
1. Open **VirtualBox** → click **New**
2. Fill in fields:
   - **Name:** `LinuxMint-DevOps`
   - **Type:** `Linux`
   - **Version:** `Ubuntu (64-bit)`

### Step 2: Resources
- **RAM:** `4096 MB` (or more if available)
- **CPU:** min. `2 cores` (recommended 4)

### Step 3: Disk
- **Type:** VDI (VirtualBox Disk Image)
- **Mode:** Dynamically allocated
- **Size:** `30–50 GB`

---

## 💿 3. Connecting ISO

### Storage Settings
1. Select created machine → **Settings** → **Storage**
2. In **Controller: IDE** section add ISO:
   - Click on "Empty" → select downloaded Linux Mint ISO
3. Check **Boot Order:**
   - 1️⃣ **Optical (ISO)**
   - 2️⃣ **Hard Disk**

---

## 🚀 4. Installing Linux Mint

### Launch and Installation
1. **Start VM** (click **Start** button)
2. **In window select** `Start Linux Mint`
3. **On desktop** click **Install Linux Mint**

### Installation Steps
- **Language:** `English` or `Ukrainian`
- **Keyboard layout:** `English (US)` / `Ukrainian`
- **Install multimedia codecs:** ✅ (recommended)
- **Installation type:** `Erase disk and install Linux Mint`
  > ⚠️ **Safe!** This only affects VM, won't touch main system
- **Create user:**
  - **Name:** `devops` or `admin`
  - **Password:** `devops123` (or more complex)
  - **Hostname:** `linuxmint-devops`

---

## 🔄 5. First Launch

### After Installation
1. **VM will restart** automatically
2. **Eject ISO:**
   - Settings → Storage → remove ISO from Optical Disk
3. **Login** to your system

---

## ✅ 6. Verification

### System Testing
Run in terminal:

```bash
# Check system version
lsb_release -a

# Check IP address (network works)
ip a

# Check updates
sudo apt update && sudo apt upgrade -y

# Check internet access
ping -c 4 google.com

# Check free space
df -h
```

### Expected Results
- ✅ System shows Linux Mint version
- ✅ IP address is displayed (e.g.: 192.168.1.100)
- ✅ Updates completed without errors
- ✅ Ping to google.com works
- ✅ Free disk space is shown

---

## 🛠️ 7. Additional Settings

### VirtualBox Guest Additions
```bash
# Install Guest Additions for better performance
sudo apt update
sudo apt install -y build-essential dkms linux-headers-$(uname -r)
# Then: Devices → Insert Guest Additions CD image
```

### Useful Packages
```bash
# Install basic tools
sudo apt install -y curl wget git vim nano htop tree
```

---

## 🆘 If Something Doesn't Work

### Common Problems
- **VM won't start:** check if virtualization is enabled in BIOS
- **Slow performance:** increase RAM to 4+ GB
- **No internet:** check network settings (NAT/Bridged)
- **Installation fails:** check if ISO is properly connected

### Recovery
- **Create Snapshot** before experiments
- **Restore from Snapshot** if something breaks

---

## 🎯 Done!

**Now you have:**
- ✅ Working Linux Mint in VirtualBox
- ✅ Safe environment for experiments
- ✅ Preparation for DevOps learning

> 🚀 **Next step:** Install Docker and practice Linux commands!

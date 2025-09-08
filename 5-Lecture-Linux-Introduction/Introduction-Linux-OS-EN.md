# 🐧 Operating Systems: A Clear Guide

> **This file** — a compact but complete summary of what an operating system (OS) is, what it consists of and what it does.

---

## 📋 Table of Contents
- [🚀 TL;DR](#tldr)
- [🎯 Key Topics](#key-topics)
- [❓ What is an Operating System?](#what-is-an-operating-system)
- [⚙️ Main OS Tasks](#main-os-tasks)
  - [🔄 Process Management (CPU)](#process-management-cpu)
  - [💾 Memory Management (RAM and Swapping)](#memory-management-ram-and-swapping)
  - [💿 Storage Management (Disk, File System)](#storage-management-disk-file-system)
  - [🖱️ Input/Output Devices (I/O)](#inputoutput-devices-io)
  - [🔒 Security and Network](#security-and-network)
- [🏗️ OS Architecture: Kernel and User Layer](#os-architecture-kernel-and-user-layer)
  - [⚙️ Kernel and Device Drivers](#kernel-and-device-drivers)
  - [👤 User Layer and Distributions](#user-layer-and-distributions)
- [💻 Examples of Operating Systems](#examples-of-operating-systems)
  - [🐧 Linux Distributions and Android](#linux-distributions-and-android)
  - [🍎 macOS and iOS](#macos-and-ios)
  - [🪟 Windows and Server OS](#windows-and-server-os)
- [🔗 Unix, POSIX and Linux Similarity to macOS](#unix-posix-and-linux-similarity-to-macos)
- [🚀 Why DevOps/IT Professionals Need Linux](#why-devopsit-professionals-need-linux)
- [📊 OS Levels Diagram](#os-levels-diagram)
- [📚 Glossary](#glossary)

---

## 🚀 TL;DR

**Operating System** — is a "mediator" between programs and hardware (CPU, memory, disk, I/O). It:

- 🔄 **distributes** processor time between processes
- 💾 **allocates/releases** RAM and performs swapping when needed
- 💿 **organizes** data storage on disk as files and folders
- 🖱️ **manages** devices (monitor, keyboard, mouse, printer, USB) through drivers
- 🔒 **handles** users, access rights, network, IP and ports

> **💡 Key Points:**
> - **Kernel** — the heart of the OS
> - **User Layer** — GUI/CLI, utilities, package managers
> - **Linux and macOS** have much in common thanks to POSIX standards and Unix heritage
> - **For DevOps** Linux knowledge is a must-have
> - **In examples** below we focus on Ubuntu

---

## 🎯 Key Topics

### 🔧 Architecture Basics
- **OS as a mediator layer** between applications and "hardware"
- **Resource allocation**: CPU, RAM, disk, I/O
- **OS Architecture**: kernel vs user layer

### ⚡ Processes and Performance
- **Processes, multitasking** and multi-core
- **Memory and swapping** — why the system "slows down"
- **Storage**: file systems, directory tree, configuration and data storage

### 🛠️ Technical Aspects
- **Device drivers**, security, network settings
- **Linux distributions**, Android, macOS/iOS (Darwin)
- **Client (desktop) vs server** OS

### 🔗 Standards and Compatibility
- **Unix, POSIX** and macOS similarity to Linux
- **Why Linux** — essential skill for DevOps
- **Ubuntu** as a learning platform

---

## ❓ What is an Operating System?

### 🖥️ Hardware Components
Every computer has hardware components:
- **Processor (CPU)** — computational center
- **Random Access Memory (RAM)** — fast data access
- **Persistent memory/storage (disk)** — long-term storage
- **Input/Output devices** — screen, keyboard, mouse, etc.

### 🤔 Interaction Problem
Programs **cannot** directly "talk" to all this equipment. It would be inefficient to embed support for every device in each application.

### 🔧 Solution: Operating System

**Operating System (OS)** — is an intermediate layer that:

- 📥 **accepts requests** from programs (e.g., "allocate memory", "open file", "draw window")
- 🔄 **translates them** into hardware actions
- ⚖️ **fairly distributes** resources between applications and isolates them from each other

### 💡 Practical Example
When you launch a browser, the OS:
1. 🖱️ **interprets** mouse click
2. 🚀 **starts** the process
3. 💾 **allocates** CPU and memory to it
4. 🖼️ **manages** display on screen
5. ⌨️ **processes** key presses

---

## ⚙️ Main OS Tasks

### 🔄 Process Management (CPU)

**Process** — is a unit of program execution. An open browser tab, IDE window or terminal — all these are processes. Each process has an **isolated execution space** so as not to interfere with others.

#### 🧠 How Multitasking Works
- If you have **one** physical processor, only **one** process actually runs at a time
- The OS very quickly switches context and creates an **illusion of parallelism**
- Modern computers are **multi-core**:
  - *dual-core* (2 cores)
  - *quad-core* (4 cores)
  - *octa-core* (8 cores) etc.

> **💡 Rule:** More cores = more processes can actually run in parallel

The OS plans **which process and when gets CPU** to keep the system responsive and stable.

### 💾 Memory Management (RAM and Swapping)

Every running process needs RAM. RAM is limited. When it's insufficient, the OS uses **swapping**:

#### 🔄 Swapping Process
1. **Frees** part of RAM by "unloading" inactive process data to disk
2. **Loads** active process data into RAM

#### ⚠️ Swapping Problems
- Swapping **slows down** the system because disk operations are slower than RAM access
- Many open programs + switching between them = intensive swapping and noticeable "braking"

### 💿 Storage Management (Disk, File System)

Disk — **persistent memory** for long-term storage: project files, application settings, images, videos, etc.

#### 📁 OS Functions for Storage
- **Organizes** data into files and directories (hierarchical structure — "tree")
- **Allocates** space for data, reads/writes it
- **Provides** access with rights and security

#### 🌳 Differences Between Systems
- **Unix-like systems** (Linux, macOS): single root tree `/` with subdirectories
- **Windows**: usually multiple "roots" (e.g., `C:`, `D:`), but also directory hierarchy

### 🖱️ Input/Output Devices (I/O)

Monitor, keyboard, mouse/trackpad, printers, USB drives, etc.

The OS through **drivers** "introduces" the device to the system and provides program interaction with these devices (input, output, settings).

### 🔒 Security and Network

#### 👥 Users and Rights
- **Multiple users**, passwords, roles
- **Administrator** has extended rights
- **Regular users** — limited

#### 🌐 Network
- **IP addresses**, ports
- **Basic network** settings and access policies

---

## 🏗️ OS Architecture: Kernel and User Layer

```
🖥️ USER APPLICATIONS
          ↓
👤 USER LAYER
   (GUI/CLI, utilities, package managers)
          ↓
⚙️ KERNEL
   (scheduler, memory, file systems, drivers)
          ↓
🔧 HARDWARE
   (CPU, RAM, disk, I/O)
```

### ⚙️ Kernel and Device Drivers

**Kernel** — loads first during computer startup. It manages:
- 🔄 **CPU** — process scheduling
- 💾 **Memory** — allocation and deallocation
- 🖱️ **I/O** — device interaction
- 📁 **Files** — file systems
- 🔧 **Processes** — creation and termination

#### 🔌 Device Drivers
**Drivers** — kernel modules or components that provide external device operation (printer, USB drive, etc.).

> **💡 Process:** When you connect a device, the kernel "brings it up" through a driver — and applications can use it

### 👤 User Layer and Distributions

Above the kernel — everything the user interacts with:
- 🖼️ **Graphical shell** (GUI)
- ⌨️ **Command line** (CLI)
- 🛠️ **System utilities**
- 📦 **Package managers**
- 📱 **Pre-installed programs**

#### 🐧 Linux Distributions
In the Linux world, different **distributions** use the same Linux kernel but differ in the user layer:

| Distribution | Features |
|-------------|----------|
| **Ubuntu** | Most popular, beginner-friendly |
| **Linux Mint** | Windows-like, stable |
| **CentOS** | Server, corporate |
| **Debian** | Stable, minimalistic |

---

## 💻 Examples of Operating Systems

### 🐧 Linux Distributions and Android

#### 🖥️ Linux Distributions
- **Ubuntu, Mint, CentOS, Debian** etc.
- **Structure**: Linux kernel + set of user components
- **Advantages**: free, open source, flexible

#### 📱 Android
- **Kernel**: Linux (but modified)
- **User layer**: completely different (for mobile devices)
- **Features**: optimized for touch screens

### 🍎 macOS and iOS

#### 🖥️ macOS
- **Kernel**: Darwin (with components from Unix systems)
- **Purpose**: PCs/laptops
- **Features**: integration with Apple ecosystem

#### 📱 iOS
- **Kernel**: Darwin (shared with macOS)
- **Purpose**: mobile devices
- **Features**: closed source, strict security

> **📝 Historical Note:** macOS was previously written as **OS X** — this is the evolution of the same system

### 🪟 Windows and Server OS

#### 🖥️ Desktop OS
- **Windows 10/11** — with GUI and conveniences for daily use
- **macOS** — for Apple computers
- **Linux distributions** — for various needs

#### 🖥️ Server OS
- **Server** OS (often Linux-based) — lighter, without graphical shell
- **Focus** on performance and management through **CLI**
- **Statistics**: despite Windows Server existence, **over half of world's servers** run on Linux

#### 📊 Server OS Comparison

| OS | Advantages | Disadvantages | Usage |
|----|------------|---------------|-------|
| **Linux** | Free, stable, flexible | Requires skills | 70%+ servers |
| **Windows Server** | Convenient GUI, Windows integration | Expensive, resource-intensive | Corporate sector |

---

## 🔗 Unix, POSIX and Linux Similarity to macOS

### 📜 Historical Context
Historically **Unix** was the base for many OS. To maintain compatibility, standards were created, particularly **POSIX** (Portable Operating System Interface) — a set of APIs/requirements for Unix-like systems.

### 🍎 macOS and Unix
- **macOS** comes from Unix family
- **Darwin kernel** contains code from Unix systems
- **Heritage**: multi-year development history

### 🐧 Linux and Unix-likeness
- **Linux** was created independently from Unix
- **Philosophy**: Unix-like (similar to Unix)
- **Result**: Linux and macOS are **POSIX-compatible**

### 🔄 Practical Implications
Due to POSIX compatibility:
- **Command line** in macOS and Linux are very similar
- **Utility behavior** is practically identical
- **File structure** has much in common
- **Scripts** often work on both systems

---

## 🚀 Why DevOps/IT Professionals Need Linux

### 🌐 Linux as Standard
Linux — **de facto standard** in the world of servers and clouds:
- **70%+ servers** run on Linux
- **Cloud platforms** (AWS, Google Cloud, Azure) are based on Linux
- **Containers** (Docker) were created for Linux

### 🛠️ DevOps Tools
Most modern tools and platforms initially appeared in Linux environment:

| Tool | Purpose | Linux Dependency |
|------|---------|------------------|
| **Docker** | Containerization | High |
| **Kubernetes** | Orchestration | High |
| **Ansible** | Automation | High |
| **Terraform** | IaC | High |
| **Jenkins** | CI/CD | High |

### 💼 Career Prospects
Linux knowledge is **essential** for:
- **DevOps Engineer**
- **Platform Engineer**
- **System Administrator**
- **SRE (Site Reliability Engineer)**

### 🎯 Learning Strategy
In this course we focus on **Ubuntu** as the most popular distribution:
- **Working experience** in terminal easily transfers to other distributions
- **Basic utilities** work the same way
- **Concepts** are universal

---

## 📊 OS Levels Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│    USER APPLICATIONS                                            │
│    browsers, IDEs, editors, games, server applications          │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│    USER LAYER                                                   │
│    CLI, GUI, utilities, package managers, application programs  │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│    OS KERNEL                                                    │
│    process scheduling, memory management, file systems, I/O     │
│    device drivers, system calls, API (POSIX)                    │
└─────────────────────────────────────────────────────────────────┘
                                ↓
┌─────────────────────────────────────────────────────────────────┐
│    HARDWARE                                                     │
│    CPU, RAM, Storage, I/O                                       │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 Interaction Flow
1. **Applications** → requests to user layer
2. **User layer** → system calls to kernel
3. **Kernel** → hardware management through drivers
4. **Hardware** → command execution

---

## 📚 Glossary

### 🔧 Basic Concepts
- **OS (Operating System)** — software layer between programs and hardware
- **Kernel** — central part of OS, manages processes, memory, devices, files
- **Device driver** — component that allows OS to work with specific hardware device

### ⚡ Processes and Memory
- **Process** — instance of program execution with its own isolated space
- **Multi-core** — presence of multiple CPU cores that execute processes in parallel
- **RAM (Random Access Memory)** — fast memory for active process data
- **Swapping** — temporary "unloading" of data from RAM to disk when RAM is insufficient

### 💾 Storage and Files
- **Storage/Disk (secondary memory)** — long-term data storage (files, settings)
- **File system** — way of organizing files and directories on disk (tree)
- **I/O (input/output)** — input/output: interaction with devices and external world

### 🔗 Standards and Distributions
- **POSIX** — interface standard for Unix-like systems that ensures software portability
- **Linux distribution** — specific "set" of user layer on top of Linux kernel (Ubuntu, Debian, etc.)

---

## 🎯 Next Steps

> **📖 Next in learning:**
> - 🐧 Working with Ubuntu
> - ⌨️ Command line
> - 📁 File hierarchy
> - 🛠️ Basic utilities
> - 🔧 System administration

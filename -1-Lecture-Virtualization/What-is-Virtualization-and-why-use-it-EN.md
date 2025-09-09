# Lecture: **Virtualization Fundamentals**

> This lecture explains key virtualization concepts (VMs, isolation, Type 1/2 hypervisors), practical use cases, and provides a step-by-step mini-practical. The material is suitable for beginners and practitioners (DevOps, system administrators, developers, cloud engineers).

---

## Learning Objectives
After this lecture you will be able to:
- Explain what virtualization and virtual machines (VMs) are.
- Distinguish between Type 1 (bare metal) and Type 2 (hosted) hypervisors.
- Name key advantages and typical virtualization use cases.
- Operate with concepts of **isolation**, **VM image**, **snapshot**.
- Perform basic VM workflow: create → configure → launch → snapshot → restore.

---

## Lecture Plan
1. Motivation and definitions
2. Key concepts: VMs, isolation, hypervisor (Type 1/2)
3. Advantages and use cases
4. VM images and snapshots
5. Analogy with "apartment building"
6. Practical: creating a test VM
7. Tips, common mistakes
8. Summary and control questions
9. Glossary

---

## 1) What is Virtualization?

**Virtualization** is a very important concept in the IT world that allows running **multiple operating systems** on one physical computer. This is achieved using **virtual machines (VMs)** — "virtual computers" that work independently from each other and from the host.

---

## 2) Key Concepts

### 2.1. Virtual Machines (VMs)
**VM** is a software computer that **simulates physical hardware**: processor, RAM, disks, network, etc. Virtual resources are "borrowed" from the physical host's hardware. Any **guest OS** can be installed on a VM (e.g., Linux on Windows, Windows on Windows, etc.).

### 2.2. Isolation
Each VM runs in its **own isolated environment**, unaware of other VMs or the host OS. Thanks to this:
- failures or compromise inside one VM **do not affect** other systems;
- VMs are easy to **delete, recreate**, migrate between hosts.

### 2.3. Hypervisors
**Hypervisor** is special software for creating and managing VMs. There are two main types:

| Characteristic | Type 2 (hosted) | Type 1 (bare metal) |
|---|---|---|
| Where installed | On top of existing host OS | Directly on "hardware" |
| Examples | Oracle VirtualBox, VMware Workstation | VMware ESXi, Microsoft Hyper‑V |
| Hardware control | Through host OS | Directly |
| Typical use cases | Learning, testing on PC | Servers, data centers, clouds |
| Pros | Easy start, cross-platform | Higher performance, reliability |
| Cons | Host OS overhead | Requires dedicated "hardware" |

> **Summary:** Type 2 — convenient on personal computer; Type 1 — standard for servers and clouds.

---

## 3) Advantages and Use Cases

- **Learning and experimentation.** Safely try new OS/software without risk to the main system. Quickly create/delete VMs.
- **Application testing.** Running and testing software in different OS/browsers/environments (e.g., Linux + Firefox, Windows + IE/Edge).
- **Efficient hardware utilization.** One powerful server is divided into many smaller VMs with needed CPU/RAM/SSD quotas.
- **Abstraction and portability.** The entire guest OS with programs and data is packaged into a **VM image** — portable file.
- **Backup and recovery.** Snapshots are easy to copy and migrate between hosts; fast DR (disaster recovery).
- **Cloud foundation.** Type 1 hypervisors build AWS, Google Cloud, DigitalOcean, etc. — isolated scalable instances for millions of users.

---

## 4) VM Images and Snapshots

- **VM Image** — file(s) containing OS, programs, configurations, and VM data. Can be copied/archived/migrated.
- **Snapshot** — "momentary state" of VM at a specific time. Allows **quick rollback** to previous working state in case of error.

```text
[Current VM state] --(Snapshot A)--> [Experiments] --(Rollback)--> [State from Snapshot A]
```

---

## 5) Analogy with Apartment Building

- **Physical computer** — is a **large building**.
- **Virtualization** — is **sound-isolated, self-contained mini-apartments** (VMs) inside the building.
- **Hypervisor** — is the **building manager** who distributes resources (electricity/water/internet) between apartments.
- **Type 2** — manager who works **through the main resident** (host OS) of the penthouse.
- **Type 1** — manager who **directly owns and manages** the entire building.
- **Each VM — separate tenant**, doesn't see others; flood in one apartment **doesn't affect** the rest.
- **Images/snapshots** — **blueprints** or **ready apartment templates** that are easy to copy/migrate/restore.

---

## 6) Practical: Creating a Test VM (10–20 min)

> **Goal:** create a VM on Type 2 hypervisor, install guest OS, make snapshot and rollback.

1. **Install Type 2 hypervisor** (choose one): Oracle VirtualBox / VMware Workstation Player / Hyper‑V (Windows Pro).
2. **Prepare ISO image** of guest OS (e.g., Ubuntu Desktop/Server).
3. **Create new VM:**
   - Name: `lab-virtualization-101`
   - CPU: 2 virtual cores (or 1 if low resources)
   - RAM: 2–4 GB (minimum 2 GB for GUI desktops)
   - Disk: 20–40 GB (dynamically expandable)
   - Network: NAT (default) or Bridged (if visible IP in network needed)
4. **Attach ISO** to VM's optical drive and **launch** it.
5. **Install OS** following installation wizard.
6. After first system login, **create Snapshot** named `clean-install`.
7. **Conduct experiment** (install package/update), then **Rollback** to `clean-install` and verify everything rolled back.

> *Result:* you've mastered the complete lifecycle of a simple VM.

---

## 7) Tips and Common Mistakes

- **Don't allocate too much RAM/CPU** to one VM — leave resources for host OS.
- **Use snapshots** before risky changes.
- **Store images/snapshots** on reliable storage (backups!).
- **Plan network**: NAT for simplicity, Bridged — if visibility in local network needed.
- **Monitor disk**: dynamic disks grow over time; clean unnecessary files/snapshots.

---

## 8) Summary and Control Questions

**Summary.** Virtualization is a fundamental technology of modern infrastructure. It provides isolation, portability, resource utilization efficiency and is the foundation of cloud platforms.

**Test yourself:**
1. How does **Type 1** hypervisor differ from **Type 2**?
2. What is a **VM image** and what is **snapshot** used for?
3. Why is VM **isolation** important for security?
4. What are **typical use cases** for VMs?
5. When is it better to choose **NAT**, and when **Bridged** network mode?

---

## 9) Glossary

- **Virtual Machine (VM)** — software simulation of a computer that runs guest OS.
- **Guest OS** — OS installed inside a VM.
- **Host OS** — base OS of physical computer on which Type 2 hypervisor runs.
- **Hypervisor** — software for creating/managing VMs (Type 1/Type 2).
- **VM Image** — file(s) with complete state of guest OS and disks.
- **Snapshot** — saved momentary state of VM for quick rollback.
- **NAT/Bridged** — network connection modes for VM network access.

---

> **Conclusion:** Virtualization is a powerful and ubiquitous technology in IT. It's necessary for everyone who works as a cloud engineer, system administrator, developer, or DevOps engineer.

# Lecture: **SSH (Secure Shell) — Secure Remote Access and File Transfer**

> In this lecture we explore **SSH**: why it's needed, how authentication works (password vs keys), what port 22 is and firewall rules, how to connect to remote server (using DigitalOcean example), copy files via **SCP**, and secure production environment settings.

---

## Learning Objectives
After the lecture you will be able to:
- Explain **why SSH is needed** and how it's secure.
- Connect to servers with password and **SSH keys**.
- Understand role of **port 22** and **firewall** for SSH access.
- Generate keys, add public key to **authorized_keys** and perform **passwordless** login.
- Copy files with **scp** and run scripts on remote machine.
- Apply practical security tips (IP allowlist, `PermitRootLogin`, `PasswordAuthentication`, `ssh-agent`).

---

## Lecture Plan
1. Why SSH is needed
2. Authentication methods: password and SSH keys
3. SSH service, port 22 and firewall
4. Practice: connecting to DigitalOcean Droplet
5. File copying: `scp` (and alternatives)
6. Running scripts/commands on remote server
7. DigitalOcean cloud-firewall and server deletion
8. Practical (15–25 min)
9. Security: best practices and common mistakes
10. Control questions
11. "Cabin and private tunnel" analogy
12. Cheat sheet

---

## 1) Why SSH is Needed

- **Remote server administration**: software installation, deployment, diagnostics.
- Mass copying scripts to **multiple nodes** and running them.
- Working **only through CLI** where GUI is absent (typical for servers).
- **Secure channel** (encryption + authentication) over unreliable network/Internet.

---

## 2) Authentication Methods

### a) Username and Password
- You login as existing **server user** (e.g. `root` or other account created by administrator).
- Simple start, but less secure for permanent work.

### b) **SSH Key Pair** (recommended)
- **Private** and **public** keys are created.
- **Private key** is stored **only** on your local machine and can be protected with **passphrase**.
- **Public key** is added to server in `~/.ssh/authorized_keys` file of target user.
- During connection server checks if private key matches public key.

> Recommended modern algorithm:
```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
# (if needed for compatibility) ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
Keys are typically stored as `~/.ssh/id_ed25519` (or `id_rsa`) and `~/.ssh/id_ed25519.pub` (or `id_rsa.pub`).

**CI services** (Jenkins, CI): generate their own key pair; CI public key is added to target server's `authorized_keys`.

---

## 3) SSH Service, Port 22 and Firewall

- **SSH daemon** (`sshd`) by default listens on **TCP port 22**.
- To have external access, **firewall** needs to **allow** inbound traffic to 22/tcp.
- Better to restrict access with **IP allowlist** of administrators/Office/VPN.

> Examples for Linux host:
```bash
# UFW
sudo ufw allow from 203.0.113.10 to any port 22 proto tcp
sudo ufw enable

# firewalld
sudo firewall-cmd --add-rich-rule='rule family=ipv4 source address=203.0.113.10/32 port port=22 protocol=tcp accept' --permanent
sudo firewall-cmd --reload
```

---

## 4) Example: Connecting to DigitalOcean Droplet

1) **Create** Droplet (e.g. Frankfurt region, Ubuntu).  
2) **Initial password access** (if no keys yet):
```bash
ssh root@<SERVER_IP_ADDRESS>
# on first login confirm host (yes) and enter password
```
After login you'll have prompt with `root@<hostname>` and directory `/root`.

3) **Generate keys** locally (see above) and **transfer public key** to server.
Easiest — `ssh-copy-id`:
```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@<SERVER_IP_ADDRESS>
```
Or manually:
```bash
# on server
mkdir -p ~/.ssh && chmod 700 ~/.ssh
vim ~/.ssh/authorized_keys     # paste content of local *.pub
chmod 600 ~/.ssh/authorized_keys
```

4) **Reconnect without password**:
```bash
ssh root@<SERVER_IP_ADDRESS>
# if key is different or in different location:
ssh -i ~/.ssh/id_ed25519 root@<SERVER_IP_ADDRESS>
```

> Tip: add entry to `~/.ssh/config` for convenience:
```bash
Host prod-do
  HostName <SERVER_IP_ADDRESS>
  User root
  IdentityFile ~/.ssh/id_ed25519
```
Then just: `ssh prod-do`.

---

## 5) File Copying: `scp`

Transfer **local → server**:
```bash
scp test.sh root@<SERVER_IP_ADDRESS>:/root/
# or with explicit key:
scp -i ~/.ssh/id_ed25519 test.sh root@<SERVER_IP_ADDRESS>:/root/
```

Server → local:
```bash
scp root@<SERVER_IP_ADDRESS>:/root/test.sh ./
```

Folder recursively:
```bash
scp -r ./app root@<SERVER_IP_ADDRESS>:/opt/app
```

> Alternative for large/frequent transfers: **`rsync`**
```bash
rsync -avz -e "ssh -i ~/.ssh/id_ed25519" ./app/ root@<SERVER_IP_ADDRESS>:/opt/app/
```

---

## 6) Running Scripts/Commands on Remote Server

Interactively:
```bash
ssh root@<SERVER_IP_ADDRESS>
ls && chmod u+x test.sh && ./test.sh
```

One-time command **without entering shell**:
```bash
ssh root@<SERVER_IP_ADDRESS> 'chmod u+x /root/test.sh && /root/test.sh'
```

Transfer and **execute local script** on remote machine "on the fly":
```bash
ssh root@<SERVER_IP_ADDRESS> 'bash -s' < ./setup.sh
```

---

## 7) DigitalOcean Cloud-Firewall and Server Deletion

- By default Droplet **has no** attached cloud-firewall → traffic is regulated only by firewall **on server itself** (if configured).  
- In DigitalOcean you can create **Firewall** (via UI/API), block everything and open only needed ports (22/tcp for SSH, 80/443 for web, etc.), restrict sources.
- After finishing work **destroy Droplet** to **not pay** for resources.

---

## 8) Practical (15–25 min)

1) **First connection** to new Droplet with password, then add key and reconnect **without password**.  
2) **Create** `test.sh` locally, copy to server with `scp` and execute.  
3) Configure `~/.ssh/config` and test short connection command.  
4) Enable **UFW** on server: allow only `22/tcp` from your IP; test connection.  
5) (Optional) Try `rsync` for directory synchronization.

---

## 9) Security: Best Practices and Common Mistakes

- **Disable password login** after setting up keys:
  ```bash
  sudo vim /etc/ssh/sshd_config
  PasswordAuthentication no
  ChallengeResponseAuthentication no
  UsePAM yes
  # (preferably) PermitRootLogin prohibit-password or no, and use separate user + sudo
  sudo systemctl reload sshd
  ```
- **Key and `.ssh` permissions**:
  ```bash
  chmod 700 ~/.ssh
  chmod 600 ~/.ssh/id_ed25519 ~/.ssh/authorized_keys
  ```
- **`ssh-agent` and `ssh-add`** for convenient work with passphrase-protected keys.
- **IP allowlist** in firewall and (if possible) access through **VPN**/Bastion.
- **Host verification**: on first login check **fingerprint**; record is stored in `~/.ssh/known_hosts`.
- **Don't leave private keys in repositories** and don't share them.
- **Changing SSH port** is not protection, but reduces scanner noise; use together with keys/Firewall/Fail2Ban.
- Common mistakes: "Permission denied (publickey)" (wrong permissions or key not in `authorized_keys`), "Host key verification failed" (fingerprint changed — check why).

---

## 10) Control Questions

1. How do password authentication and key authentication differ?
2. Where to store public key on server and what permissions should files/folders have?
3. Why is it important to open **22/tcp** only from allowed IPs?
4. How to connect with key with non-standard name/location?
5. How to transfer local script and execute it on server in one line?
6. Why should `PasswordAuthentication` be disabled in production?

---

## 11) Analogy: "Cabin and Private Tunnel"

- **SSH** — secure **tunnel** between your home (local machine) and **cabin** (server).
- **Login+password** — regular door key.
- **Key pair** — **personal seal** (private key) + **imprint** on door (public).
- **Port 22** — dedicated door to tunnel.
- **Firewall** — security that lets in only allowed and only through needed doors.
- **SCP** — secure courier that carries "packages" (files) through tunnel.

---

## 12) Cheat Sheet

```bash
# Key generation
ssh-keygen -t ed25519 -C "you@example.com"

# Add key to server
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@host

# Connection
ssh user@host
ssh -i ~/.ssh/custom_key user@host

# Config (command shortcuts)
cat >> ~/.ssh/config <<'CFG'
Host prod
  HostName 203.0.113.10
  User ubuntu
  IdentityFile ~/.ssh/id_ed25519
CFG

# File copying
scp -i ~/.ssh/id_ed25519 ./test.sh user@prod:/home/user/
rsync -avz -e "ssh -i ~/.ssh/id_ed25519" ./app/ user@prod:/opt/app/

# Permissions
chmod 700 ~/.ssh
chmod 600 ~/.ssh/id_ed25519 ~/.ssh/authorized_keys

# Restart sshd after changes
sudo systemctl reload sshd
```

---

> **Conclusion:** SSH is your main tool for managing Linux servers: secure access, file transfer and automation. Configure keys, restrict access with firewall and follow best practices — and remote work will become fast and secure.

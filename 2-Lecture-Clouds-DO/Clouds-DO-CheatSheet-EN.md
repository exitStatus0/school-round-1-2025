# DigitalOcean Droplet (minimum) — create, login and check port 8080 (EN)

> Goal: **create an affordable Droplet with Ubuntu through web interface**, access it and **verify that port 8080 is accessible from the internet via HTTP**. Without DO CLI.

---

## 0) Prerequisites (including SSH key)
- **DigitalOcean** account with added **credit card**.
- **Recommended:** have **SSH key** (public key) for secure access. If you don't want to now — in the Droplet creation step you can choose **Password** (less secure).
- DigitalOcean **doesn't generate** SSH keys for you. Below — how to create a key for your OS.

### Create SSH key (ed25519, recommended)
> **ed25519** algorithm — modern and compact. If not supported on very old systems — use RSA (`-t rsa -b 4096`). Recommended to set **passphrase**.

#### macOS or Linux
```bash
# 1) Create key
ssh-keygen -t ed25519 -C "you@example.com"

# 2) Default path: ~/.ssh/id_ed25519 (private), ~/.ssh/id_ed25519.pub (public)
#    Confirm path with Enter and set passphrase (optional).

# 3) Show and copy PUBLIC key (to paste in DO)
cat ~/.ssh/id_ed25519.pub
```

#### Windows 10/11 (PowerShell with built-in OpenSSH)
```powershell
# 1) Check that ssh is available
ssh -V

# 2) Create key (ed25519)
ssh-keygen -t ed25519 -C "you@example.com"

# 3) Typical path: C:\Users\<YourUser>\.ssh\id_ed25519.pub
#    Show and copy PUBLIC key:
type $env:USERPROFILE\.ssh\id_ed25519.pub
```

#### Windows (Git Bash or WSL)
```bash
# In Git Bash/WSL commands are the same as on Linux/macOS
ssh-keygen -t ed25519 -C "you@example.com"
cat ~/.ssh/id_ed25519.pub
```

> In **DigitalOcean → Create → Droplets → Authentication** click **New SSH Key** and **paste the content of .pub** file. Exactly the **public** key (line starting with `ssh-ed25519 ...`).

---

## 1) Creating Droplet through web UI
1. Go to DigitalOcean → click **Create** → **Droplets**.
2. **Image:** select **Ubuntu 24.04 LTS (x64)**.
3. **Region:** `fra1` (Frankfurt) — close to PL/EU.
4. **Size (minimum for demo):** `s-1vcpu-1gb` (1 CPU / 1 GB RAM / 25 GB SSD).
5. **Authentication:** choose **one** option:
   - **SSH Key (recommended):** **New SSH Key** → paste content of your `.../id_ed25519.pub`.
   - **Password:** set **strong password** for `root` (less secure).
6. (Optional) Tags/monitoring — not required.
7. Click **Create Droplet** and wait for **Public IPv4** to appear (write down the address).

---

## 2) Server login
### Option A — browser **Droplet Console** (always works)
- In Droplets list open your instance → **Console** → login as `root` (password or key — depending on what you chose).

### Option B — **SSH** from your computer
```bash
# If you added SSH key (keys are automatically used by your SSH agent)
ssh root@<PUBLIC_IP>

# If created with Password and want SSH with password (sometimes forbidden by policy; then use Console)
ssh root@<PUBLIC_IP>
# (will ask for password)
```

> After first login system may ask to **change temporary password** (if you chose Password).

---

## 3) Start simple HTTP server on 8080 and open port
Execute commands **on the server** (through Console or SSH):

```bash
# Update package index
apt update

# Make sure python3 is available (usually present in Ubuntu 24.04)
apt -y install python3 > /dev/null 2>&1 || true

# Create simple page
mkdir -p /opt/test && cd /opt/test
echo "OK: $(hostname) is up on port 8080" > index.html

# (Optional) Enable UFW and open 8080 + SSH
ufw allow OpenSSH || true
ufw allow 8080/tcp || true
yes | ufw enable || true
ufw status

# Start temporary HTTP server on 8080 (in this session)
python3 -m http.server 8080 --bind 0.0.0.0
```

> Note: server will run while this session is open (CTRL+C — stop). This is a **temporary test**, not production.

---

## 4) External accessibility check
On your local computer (or just in browser):
- Open: `http://<PUBLIC_IP>:8080/` — should show text **OK: …**  
- Or with command:
```bash
curl -I http://<PUBLIC_IP>:8080/
```
Expected to see status `HTTP/1.0 200 OK`.

---

## 5) Done
- You **created** an affordable Droplet with Ubuntu, **accessed** it and **verified** that port **8080** is accessible from the internet via HTTP.
- For permanent service use **systemd** or reverse proxy — this is **beyond the scope** of this minimal cheat sheet.

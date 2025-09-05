# 🚀 Cheat Sheet: Deploy Quarkus *getting-started* on DigitalOcean (Ubuntu) from Linux Mint (VirtualBox)

> **What we do:** from Linux Mint we SSH into a new Ubuntu droplet in DigitalOcean, install Java and other packages, pull `quarkus-quickstarts/getting-started`, build and run it on port **8080**, verify it, then **delete the droplet** to avoid charges.

---

## 🏗️ Demo Architecture

### Text diagram of setup
```
┌─────────────────────────────────────────────────────────────────┐
│                    Local Machine (Host)                         │
│  ┌─────────────────┐    ┌─────────────────┐                     │
│  │   Windows/Mac   │    │   VirtualBox    │                     │
│  │                 │    │                 │                     │
│  │  ┌─────────────┐│    │  ┌─────────────┐│                     │
│  │  │ Linux Mint  ││    │  │ Linux Mint  ││                     │
│  │  │ (Dev Env)   ││    │  │ (Dev Env)   ││                     │
│  │  └─────────────┘│    │  └─────────────┘│                     │
│  └─────────────────┘    └─────────────────┘                     │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ SSH Connection
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    DigitalOcean Cloud                           │
│  ┌─────────────────┐    ┌─────────────────┐                     │
│  │   Ubuntu 24.04  │    │   Quarkus App   │                     │
│  │   Droplet       │    │   Port 8080     │                     │
│  │                 │    │                 │                     │
│  │  ┌─────────────┐│    │  ┌─────────────┐│                     │
│  │  │ Java 21     ││    │  │ HTTP Server ││                     │
│  │  │ Maven       ││    │  │ 0.0.0.0:8080││                     │
│  │  │ Git         ││    │  │             ││                     │
│  │  └─────────────┘│    │  └─────────────┘│                     │
│  └─────────────────┘    └─────────────────┘                     │
└─────────────────────────────────────────────────────────────────┘
                                │
                                │ HTTP Request
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                    User (Browser)                               │
│  ┌─────────────────┐                                            │
│  │   Web Browser   │                                            │
│  │ http://IP:8080  │                                            │
│  └─────────────────┘                                            │
└─────────────────────────────────────────────────────────────────┘
```

### 🔄 Execution Flow
1. **Preparation** — create SSH key and droplet
2. **Connection** — SSH from Linux Mint to Ubuntu droplet
3. **Setup** — install Java, Maven, Git
4. **Code** — clone Quarkus quickstart repository
5. **Build** — compile and run application
6. **Testing** — verify through browser
7. **Cleanup** — delete droplet to save costs

---

## 🔑 0) Prerequisites (locally in Linux Mint)
- **SSH key** (preferred): 
  ```bash
  # if you don't have a key yet
  ssh-keygen -t ed25519 -C "you@example.com"
  # public key will show like this
  cat ~/.ssh/id_ed25519.pub
  ```
  Add the public key to DigitalOcean: **Settings → Security → SSH Keys**.

- **Create droplet** in DO (recommended: Ubuntu 24.04 LTS, smallest plan). Write down its public IP address as `DROPLET_IP`.

---

## 🔌 1) SSH login to droplet
```bash
# (key variant)
ssh -o StrictHostKeyChecking=accept-new root@DROPLET_IP

# (if password login — DO will send password to email; change it on first login)
ssh root@DROPLET_IP
```

> Next all commands are executed **on the droplet** (as root or via sudo).

---

## ⚙️ 2) Basic Ubuntu preparation
```bash
apt update && apt -y upgrade

# useful packages
apt -y install git curl unzip

# Java (choose one variant)
apt -y install openjdk-21-jdk   # modern LTS
# or
# apt -y install openjdk-17-jdk  # will also work
java -version

# Maven (needed for building Quarkus quickstart)
apt -y install maven
mvn -version
```

### (Optional) UFW firewall
> In DO ports are usually open, but it's better to restrict manually.
```bash
apt -y install ufw
ufw default deny incoming
ufw default allow outgoing
ufw allow 22/tcp           # SSH
ufw allow 8080/tcp         # our application
ufw --force enable
ufw status verbose
```

---

## 📥 3) Cloning Quarkus quickstarts repository (only getting-started example)
```bash
cd /opt
git clone https://github.com/quarkusio/quarkus-quickstarts.git
cd quarkus-quickstarts/getting-started
```

> Example structure — Maven project. Next we'll build and run it.

---

## 🚀 4) Build and run on 0.0.0.0:8080
> By default Quarkus may bind to localhost. For external access we specify host **0.0.0.0**.

### Option A: **quick dev mode** (for demonstration)
```bash
# dev mode (hot reload, convenient for quick demo)
./mvnw quarkus:dev -Dquarkus.http.host=0.0.0.0 -Dquarkus.http.port=8080
# if ./mvnw is not executable yet: chmod +x mvnw
```

### Option B: **production run with JAR**
```bash
# build
mvn -DskipTests package

# run fast-jar (with bind to all interfaces)
java -Dquarkus.http.host=0.0.0.0 -Dquarkus.http.port=8080 \
     -jar target/quarkus-app/quarkus-run.jar
```

> Make sure port 8080 is not occupied:
```bash
ss -tulpn | grep 8080 || true
```

---

## ✅ 5) Verification
- On **local machine** (Linux Mint) open in browser:  
  `http://DROPLET_IP:8080/`
- Or from droplet/locally:
  ```bash
  curl -i http://127.0.0.1:8080/
  curl -i http://DROPLET_IP:8080/
  ```

Expected response like `200 OK` and JSON or HTML depending on endpoint.

---

## ⏹️ 6) Stop application
- If running in **dev mode** or as **jar** — press `Ctrl + C` in that console.
- Check that port 8080 is free:
  ```bash
  ss -tulpn | grep 8080 || echo "8080 is free"
  ```

---

## 🗑️ 7) Cleanup and **delete droplet** to avoid charges
> **Important:** Just turning off the server is **not enough**. Need to **Destroy** in DO.

### Through DigitalOcean web interface
1. Go to **DigitalOcean → Droplets**.
2. Select your droplet → **Destroy** → confirm.

### (Optional) through `doctl` from local machine
```bash
# install doctl (if needed): https://docs.digitalocean.com/reference/doctl/how-to/install/
# authorization
doctl auth init
# check droplet list
doctl compute droplet list
# delete by ID
doctl compute droplet delete <DROPLET_ID> --force
```

---

## Common issues and solutions
- **Port 8080 not accessible externally**  
  – Make sure of `-Dquarkus.http.host=0.0.0.0`.  
  – Open port in UFW/DO Firewall.  
  – Check IP (sometimes need to wait 10–30 sec after app start).

- **Java/Maven not found**  
  – Check `java -version` and `mvn -version`; if missing — install packages.  
  – Sometimes enough `apt update` and retry `apt install`.

- **`permission denied` for `mvnw`**  
  – Execute `chmod +x mvnw` in Maven project root.

- **Port 8080 occupied**  
  – Find PID: `ss -tulpn | grep 8080` → `kill -9 <PID>` or change port: `-Dquarkus.http.port=9090` and open it in firewall.

---

## Quick demo plan (for screen recording)
1. SSH login to droplet.  
2. `apt update && apt upgrade`, install Java/Maven/Git.  
3. Clone repo `quarkus-quickstarts`, go to `getting-started`.  
4. **Run:** `./mvnw quarkus:dev -Dquarkus.http.host=0.0.0.0`  
5. Open `http://DROPLET_IP:8080/` in browser → show response.  
6. Stop application (`Ctrl+C`).  
7. Delete droplet in DO (**Destroy**).

---

### Done!
This is a minimal but realistic demonstration of DevOps flow: raised infrastructure (droplet), prepared environment, built and ran application, opened network, verified result and turned off resources.

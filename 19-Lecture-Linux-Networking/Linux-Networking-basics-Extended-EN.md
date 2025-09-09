# Extended Lecture: **Linux Networking Basics — Simple and Practical**

> Goal: provide clear, simple and practical understanding of networking in Linux with command examples and short exercises after each block. Explanations are "human-friendly", without excessive metaphors, focusing on **how to look and check** on your own system.

---

## What You'll Learn to Do
- Read your IP address, routes and DNS configuration.
- Understand ports, **TCP** and **UDP** protocols and find **which process listens on a port**.
- Use **ping**, **traceroute/mtr**, **dig/nslookup**, **ss/lsof**.
- Distinguish **loopback (127.0.0.1)** and external addresses.
- Safely check ICMP performance (**ping flood only locally**).
- Quickly diagnose common network problems on Linux host.

> **Code notation**: `$` symbol means command in terminal executed as regular user; `#` — as root or with `sudo`.

---

## 0) Quick Conventions
- Modern alternatives: `ip` (instead of `ifconfig`), `ss` (instead of `netstat`), `dig` (along with `nslookup`).
- Output examples may slightly differ on your system — this is normal.
- When you see `sudo`, administrator rights will be needed.

---

## 1) IP Addressing (IPv4) — How to Find Out "Where You Are"
**Idea:** each network interface has an address. Most often you need: IP, mask (CIDR) and default route.

```bash
$ ip -br addr         # compact overview of interfaces and addresses
$ ip addr show eth0   # detailed for specific interface
$ ip route            # routing table; look for "default via ..."
```

Example output fragment:
```
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500
    inet 192.168.1.50/24 brd 192.168.1.255 scope global eth0
```
Here IP is `192.168.1.50`, mask is `/24` (i.e. 255.255.255.0).

**Mini-exercise:**  
1) Run `ip -br addr` and write down your IPv4 address.  
2) Run `ip route | sed -n '1p'` — what does your "default via …" look like?

---

## 2) Loopback (127.0.0.1) — "Myself"
**Idea:** address 127.0.0.1 (or name `localhost`) always points to **this same** computer. Convenient for development and testing.

```bash
$ ping -c 3 127.0.0.1
$ curl -I http://127.0.0.1:80   # if you have local web server
```

**Why:** programs can interact with each other "over network" without going to external network.

**Mini-exercise:**  
Ping `127.0.0.1` and check that you get responses without packet loss.

---

## 3) Ports and Processes — Who is "Listening"?
**Idea:** port is application's "door". Range 0–65535, separately for **TCP** and **UDP**. On one IP **cannot** simultaneously listen **2 processes** the same **port/protocol**.

View listeners and connections:
```bash
$ ss -ltnp         # TCP listeners + PID/process
$ ss -punt         # UDP and TCP
$ sudo lsof -i -P -n   # sockets ↔ processes (needs privileges)
```

Example: who listens on 5432 (PostgreSQL)?
```bash
$ ss -ltnp | grep ':5432'
```

**Mini-exercise:**  
1) Find who listens on TCP ports 22/80/443/5432 on your machine.  
2) If port is occupied, try to find PID and process name via `lsof` and `ps`.

---

## 4) TCP vs UDP — What's the Difference?
- **TCP** — "reliable and sequential": connection establishment (3-way handshake), delivery and packet order control, retransmissions, loss detection. Good for web pages/files/DB.
- **UDP** — "minimal overhead": without connection establishment, **without** delivery/order guarantees. Good for streaming/VoIP/games where speed is important and partial losses are acceptable.

Try quick local test with `netcat`:
```bash
# In one terminal — TCP server
$ nc -l -p 9999

# In another — TCP client
$ echo "hello over TCP" | nc 127.0.0.1 9999

# For UDP (instead of TCP): add -u
$ nc -u -l -p 9998            # server
$ echo "hello over UDP" | nc -u 127.0.0.1 9998  # client
```

**Mini-exercise:**  
1) Send phrase through TCP and UDP (as above).  
2) Explain why UDP is often chosen for video calls.

---

## 5) DNS — Domain → IP
**Idea:** people remember names, machines — addresses. DNS converts domain (`example.com`) to IP (A/AAAA), also exist CNAME, MX, TXT, NS records.

Commands:
```bash
$ dig +short example.com A
$ dig +short example.com AAAA
$ dig +trace example.com         # entire query chain
$ nslookup example.com           # classic alternative
```

**Mini-exercise:**  
Find A- and AAAA-records for 2–3 domains. Try `dig +trace` and count how many "steps" to authoritative response.

---

## 6) Connectivity Check: ping
`ping` sends ICMP Echo Request and measures **latency** and **losses**.

```bash
$ ping -c 4 8.8.8.8
$ ping -c 4 google.com     # also performs DNS resolution
```

Final "ping statistics" will show sent/received packets, % loss and RTT min/avg/max.

**Mini-exercise:**  
Compare latency to `127.0.0.1`, your router (e.g. 192.168.1.1) and public address (8.8.8.8).

---

## 7) Path to Node: traceroute / mtr
`traceroute` increases TTL from 1 up, getting "Time Exceeded" responses — this builds list of intermediate nodes (hops).

```bash
$ traceroute 8.8.8.8
# or interactive mtr (may need installation):
$ mtr -rw 8.8.8.8
```

Asterisks `*` mean node didn't respond to packets for this TTL.

**Mini-exercise:**  
Trace path to `1.1.1.1` and `google.com`. At which hop does latency increase?

---

## 8) Ping Flood — Only for Local Tests
**Idea:** `ping -f` sends packets as fast as possible. This is **only for lab tests** and **only** on your own resources (e.g. `127.0.0.1`).

```bash
$ sudo ping -f -c 10000 127.0.0.1
```
You'll see statistics for large number of packets. **Don't** run flood on other hosts — this may be considered an attack.

**Mini-exercise:**  
Run short flood on `127.0.0.1` and compare statistics with "regular" `ping -c 10000 127.0.0.1`.

---

## 9) Routes, Gateway and "Where Packet Goes"
`ip route` shows routing table; usually there's "default via …" entry — this is your gateway.

```bash
$ ip route
default via 192.168.1.1 dev eth0
192.168.1.0/24 dev eth0 proto kernel scope link src 192.168.1.50
```

**Mini-exercise:**  
Find your default route and explain where packet to `8.8.8.8` goes: directly or via gateway?

---

## 10) Firewall — What's Allowed and What's Not
On Linux often use `nftables`/`iptables` and wrappers like `ufw`/`firewalld`.
- Host firewall controls inbound/outbound at your server level.
- In cloud additionally exist "security groups"/ACL on perimeter.

Examples with `ufw` (Ubuntu):
```bash
$ sudo ufw status
$ sudo ufw allow 8080/tcp   # open TCP port 8080
$ sudo ufw delete allow 8080/tcp
```

**Mini-exercise:**  
Check `ufw` status. If enabled — add rule for test port, check `ss -ltnp`, then delete rule.

---

## 11) Practical Mini-Scenarios (Diagnostics)
### Scenario A: "Site Won't Open"
1. `ping -c 4 example.com` — any responses?
2. `dig +short example.com A` — resolves to IP?
3. `traceroute example.com` — at which hop does it hang?
4. If service is yours: `ss -ltnp | grep ':443\|:80'` — is web server listening?

### Scenario B: "Port Occupied"
```bash
$ ss -ltnp | grep ':8080'
$ sudo lsof -iTCP:8080 -sTCP:LISTEN -n -P
$ ps -fp <PID>
```
Find process, free port or change service config.

### Scenario C: "Has IP but No Internet"
- `ip route` — is there "default via …"?
- `/etc/resolv.conf` — are there DNS servers?
- Firewall/cloud rules — not blocking outbound?

---

## 12) Control Questions
1. Why can't two programs listen on same port/protocol on one IP?
2. How does TCP differ from UDP and where is which better to use?
3. What is loopback for and how does it differ from external addresses?
4. What information do "ping statistics" provide?
5. How does `traceroute` find out list of hops?
6. Where to look who listens on port 5432?
7. What does `ip route` show and how to find default gateway?
8. How to find domain IP and go through entire resolution chain?

---

## 13) Common Mistakes and How to Fix Them
- **DNS not configured** → has IP but domains don't resolve. Check `/etc/resolv.conf` or NetworkManager.
- **No default route** → only local network accessible. Add `default via …` (via DHCP/NetworkManager/`ip route add`).
- **Port occupied by other process** → find PID via `ss`/`lsof`.
- **Firewall blocks** → check `nft/iptables` or `ufw`/`firewalld`, plus cloud SG/ACL.
- **Confusing TCP/UDP** → service listens TCP but you check UDP (or vice versa).

---

## 14) Cheat Sheet (Commands)
```bash
# IP/interfaces/routes
ip -br addr
ip route
ping -c4 1.1.1.1

# DNS
dig +short example.com A
dig +short example.com AAAA
dig +trace example.com
nslookup example.com

# Ports/processes
ss -ltnp
ss -punt
sudo lsof -i -P -n
sudo lsof -iTCP:80 -sTCP:LISTEN -n -P

# Trace
traceroute 8.8.8.8
mtr -rw 8.8.8.8

# Lab
nc -l -p 9999
echo "hello" | nc 127.0.0.1 9999
nc -u -l -p 9998
echo "hello" | nc -u 127.0.0.1 9998

# Safe flood — only locally
sudo ping -f -c 10000 127.0.0.1
```

---

### Conclusion
Having mastered IP, ports, TCP/UDP, DNS and basic tools (`ip`, `ss`, `dig`, `ping`, `traceroute/mtr`), you can already confidently diagnose most network problems on Linux server and locally.

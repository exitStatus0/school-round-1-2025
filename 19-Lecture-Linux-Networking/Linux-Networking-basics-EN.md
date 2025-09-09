# Lecture: **Computer Networking Basics for Linux**

> In this lecture we'll explore how computers connect to each other and to the Internet: **LAN/WAN, IP addresses, subnets (CIDR), gateway, NAT, ports and firewall, DNS**, as well as useful Linux networking commands.

---

## Learning Objectives
After the lecture you will be able to:
- Explain the difference between **LAN**, **WAN/Internet**, roles of **switch** and **router**.
- Read/write **IPv4 addresses**, understand **subnet mask** and **CIDR** (`/24`, `/16`) and determine if address is "local" or "external".
- Understand what **gateway** and **NAT** are and why they're needed.
- Distinguish **ports** and how **firewall** controls access.
- Explain how **DNS** converts domain names to IP addresses and what caching is.
- Use basic Linux commands for network diagnostics: `ip`, `ss`, `lsof`, `ping`, `traceroute`/`mtr`, `dig`/`nslookup`, etc.

---

## Lecture Plan
1. LAN and IP addresses (IPv4/IPv6 briefly)
2. Switch (L2) and router (L3). Gateway. ARP
3. Subnets and CIDR. How to understand "own network" or Internet
4. Interface parameters and **NAT**
5. **Firewall** and **ports**
6. **DNS**: hierarchy, A/AAAA records, cache
7. Useful Linux commands (classic and modern)
8. Practical
9. Security notes and common problems
10. Control questions
11. "City and postal service" analogy
12. Cheat sheet

---

## 1) LAN and IP Addresses

- **LAN** — local network in home/office/classroom.
- **IPv4** has **32 bits** → 4 octets `0–255`: example `192.168.1.42`.
- **Private ranges (RFC1918)**: `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16` — used inside LAN, **not routed** directly in Internet.
- **IPv6** (briefly): 128 bits, hexadecimal notation (e.g. `2001:db8::1`). Solves address shortage, has built-in autoconfiguration mechanisms.

---

## 2) Switch and Router. Gateway. ARP

- **Switch (L2)** connects devices in LAN and switches **frames by MAC addresses** (not IP).  
  > Note: IP addresses are used at layer 3; IP→MAC binding within LAN is done by **ARP** protocol.
- **Router (L3)** connects different networks (LAN ↔ WAN/Internet), makes decisions based on **IP addresses**.
- **Gateway** — IP address of router in your LAN (typically something like `192.168.1.1`). "External" traffic goes through gateway.

**When is packet local or external?**  
If destination IP belongs to your subnet — frame goes directly in LAN (via switch). If not — packet is sent to **gateway** (router).

---

## 3) Subnets and CIDR

- **Subnet mask** defines which IP bits are "network" and which are "host".
  - Mask examples: `255.255.255.0` = **/24**; `255.255.0.0` = **/16**.
- **CIDR notation** `/N` — how many **network bits** are fixed.
  - `192.168.1.0/24` → host addresses: `192.168.1.1–192.168.1.254` (network `.0`, broadcast `.255`).
- Determine "own network": bitwise **AND** between host address and mask → get network address. If network addresses (source/destination) match — destination is local.

---

## 4) Interface Parameters and NAT

Each host needs:
- **IP address**
- **subnet mask/CIDR**
- **gateway**
- (usually) **DNS server(s)**

**NAT (Network Address Translation)** — router function that replaces internal private IPs with external router address. Variants:
- **SNAT/PAT (masquerading)**: many internal addresses → one external IP (via different ports). Safer and saves IPv4.
- **DNAT/port forwarding**: external connections are directed to specific internal host/port.

NAT benefits:
- **Hides** internal addressing
- Allows many LANs to use same private ranges
- Saves public IPv4

---

## 5) Firewall and Ports

- **Port** — application "door": **TCP/UDP** 0–65535.  
  Common: `80` (HTTP), `443` (HTTPS), `22` (SSH), `5432` (PostgreSQL), `3306` (MySQL).
- On one IP **impossible** to run two programs that **listen** on **same port/protocol**.
- **Firewall** (on host or perimeter) defines what's **allowed/forbidden** (inbound/outbound).  
  In Linux: **nftables** (modern), **iptables** (classic), wrappers like **ufw**, **firewalld**.

> Ephemeral ports (client) are automatically allocated by OS for initiating outbound connections.

---

## 6) DNS: Names → Addresses

- **DNS** converts domains to IP (**A** for IPv4, **AAAA** for IPv6). Also exist **CNAME**, **MX**, **TXT**, **NS** records, etc.
- **Hierarchy**: root servers (logically "13" names, physically — many anycast nodes) → **TLD** (.com, .org, .ua, …) → **authoritative** servers of specific domain.
- **Recursive resolver** (your provider/local cache) queries in chain and caches responses according to **TTL**.
- **ICANN** coordinates name space; registrars sell domains, hosters maintain authoritative DNS.

---

## 7) Useful Linux Commands

> Modern utilities: `ip`, `ss`, `dig`, `lsof`, `mtr`. Classic (`ifconfig`, `netstat`, `nslookup`) may be missing or outdated.

**Addresses/interfaces/routes:**
```bash
ip -br addr              # brief overview of interfaces and IPs
ip route                 # routing table (default gateway → "default via ...")
ping -c4 8.8.8.8         # check network reachability via ICMP
traceroute 8.8.8.8       # path to node (or mtr 8.8.8.8)
```

**Ports/connections and corresponding processes:**
```bash
ss -ltnp                 # who listens on TCP ports + PID/process
ss -punt                 # UDP and TCP with details
lsof -i -P -n            # sockets + processes (needs privileges)
```

**DNS:**
```bash
dig +short example.com A
dig +short example.com AAAA
dig example.com ANY      # general overview (may be truncated)
nslookup example.com     # classic alternative
```

**Who responds on port:**
```bash
ss -ltnp | grep ':5432'  # is PostgreSQL listening
```

**Classic (may be missing):**
```bash
ifconfig                  # interface addresses (deprecated; replacement: ip addr)
netstat -tulpen          # active sockets (deprecated; replacement: ss)
```

> `ps` **doesn't show ports**; use `ss`/`lsof` and link PID with `ps` if needed.

---

## 8) Practical (20–30 min)

1) **Determine host parameters**
```bash
ip -br addr
ip route | sed -n '1p'
```
Explain: your IP address, mask (via CIDR), default gateway.

2) **Local vs external**
- Choose 2 addresses: one within your subnet, another — public. Show where packet will go (directly or via gateway).

3) **Service check**
```bash
ss -ltnp        # find what listens on 22/80/443/5432
```
Try `curl -I http://127.0.0.1:80` (if local web server exists).

4) **DNS diagnostics**
```bash
dig +trace example.com   # see entire query chain
```

5) **Find "culprit" of port**
```bash
sudo lsof -iTCP:8080 -sTCP:LISTEN -n -P
```

---

## 9) Security and Common Problems

- **Wrong `PATH` to DNS/gateway** → "has IP but no Internet". Check `ip route`, `/etc/resolv.conf`.
- **Firewall blocks port** → service running but "unavailable externally". Check `nft/iptables`, cloud firewall/SG rules.
- **Port occupied** → another process already listening. Check `ss`/`lsof`.
- **NAT and port forwarding** → external inbound doesn't reach without DNAT/port forwarding.
- **DNS cache/TTL** → domain changes "not visible" immediately; clear cache or wait TTL.

---

## 10) Control Questions

1. Why does switch work with **MAC**, and "neighbor by IP" determination is done by **ARP**?
2. What does `192.168.10.0/24` mean and how many addresses are available for hosts?
3. How does OS decide — send packet directly or to gateway?
4. Why is **NAT** needed and how do **SNAT** and **DNAT** differ?
5. Why can't two programs listen on same **port/protocol** on one IP?
6. How do **A** and **AAAA** records differ? Where is DNS response cached?
7. What commands to find: (a) IP and gateway; (b) who listens on port 5432; (c) IP of domain `example.com`?

---

## 11) Analogy: "City and Postal Service"

- **LAN** — your neighborhood; **WAN/Internet** — other cities/countries.
- **IP address** — house address; **MAC** — "door serial number".
- **Switch** — mailman within neighborhood; **router/gateway** — central post office.
- **NAT** — sends mail with station address instead of your apartment.
- **Firewall** — security at entrance; **ports** — different doors of building.
- **DNS** — phone/address book: name → address.

---

## 12) Cheat Sheet (Commands)

```bash
# Addresses/routes
ip -br addr
ip route
ping -c4 1.1.1.1
traceroute 8.8.8.8     # or mtr 8.8.8.8

# Ports/connections
ss -ltnp               # TCP listeners
lsof -i -P -n          # sockets ↔ processes

# DNS
dig +short example.com A
dig +short example.com AAAA
nslookup example.com

# Who occupied port
sudo lsof -iTCP:80 -sTCP:LISTEN -n -P
```

---

> **Conclusion:** understanding IP, subnets, gateways, NAT, ports, firewall and DNS — foundation for working with Linux servers and troubleshooting network failures. Practice with `ip`, `ss`, `dig`, `lsof`, `ping` and you'll quickly learn to "see" network as system of interconnected layers.

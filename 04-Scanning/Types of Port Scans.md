# 📡 Types of Port Scans

> **MOC:** [[MOC - Scanning]] | **Tags:** `#scanning` `#port-scan` `#nmap`
> Back to [[Scanning in Ethical Hacking]] | Tool: [[Nmap - Complete Guide]]

---

## Overview of Scan Types

| Scan Type | Flag | Best For | Detection Risk |
|---|---|---|---|
| SYN/Stealth | `-sS` | General stealth scanning | Low |
| TCP Connect | `-sT` | Non-root users | Medium |
| UDP | `-sU` | UDP services | Low |
| NULL | `-sN` | Firewall evasion | Low |
| FIN | `-sF` | Firewall evasion | Low |
| XMAS | `-sX` | Firewall evasion | Low |
| ACK | `-sA` | Firewall mapping | Low |
| Ping/No-Port | `-sP` | Host discovery | Very Low |
| Idle | `-sI` | Ultimate stealth | Minimal |
| Banner Grabbing | — | Service detection | Medium |
| Vulnerability | — | CVE discovery | High |

---

## 1. TCP Connect Scan

- **What it does:** Completes the **full 3-way TCP handshake**
- **How:** SYN → SYN-ACK → ACK (connection established)
- **When used:** When you don't have root/admin privileges
- **Detection:** Easily detected — full connection appears in logs
- **Nmap flag:** `-sT`

```bash
nmap -sT 192.168.1.1
```

---

## 2. SYN Scan / Stealth Scan / Half-Open Scan

- **Also called:** Stealth scan, Half-open scan
- **Same as:** TCP SYN scan
- **Nmap flag:** `-sS`

**How it works:**
```
Source ──── SYN ────→ Destination
Source ←── SYN-ACK── Destination  (port is OPEN)
Source ──── RST ────→ Destination  (interrupts handshake)
```

**Port is OPEN if:** SYN-ACK received
**Port is CLOSED if:** RST received  
**Port is FILTERED if:** No response (firewall dropping packets)

**Key insight:** In this scan, the source **interrupts the 3-way handshake** by sending a RST flag. The port/destination will **NOT get any information about the source**.

- **Detection:** Low — never completes handshake
- **Best for:** General purpose stealth scanning

```bash
nmap -sS 192.168.1.1
```

---

## 3. UDP Scan

- **Used for:** Scanning **UDP-based services** (DNS, SNMP, DHCP)
- **How:** Sends UDP packets to target ports
- **Open port:** No response (or UDP response)
- **Closed port:** ICMP "Port Unreachable" message
- **Nmap flag:** `-sU`
- **Slow** — UDP doesn't have built-in confirmation

```bash
nmap -sU 192.168.1.1
nmap -sU -p 161 192.168.1.1  # SNMP port
```

---

## 4. Ping Scan / No-Port Scan

- **Used for:** Host discovery only — just find live hosts
- **Does NOT scan ports**
- **Nmap flag:** `-sP` or `-sn`

```bash
nmap -sP 192.168.1.0/24
nmap -sn 192.168.1.0/24
```

---

## 5. XMAS Scan

- **Sends:** FIN, PSH, URG flags simultaneously (lights up like a Christmas tree)
- **Nmap flag:** `-sX`
- **Open port:** No response
- **Closed port:** RST returned
- **Use:** Firewall evasion — many firewalls don't filter XMAS packets

```bash
nmap -sX 192.168.1.1
```

---

## 6. FIN Scan

- **Sends:** FIN flag only
- **Nmap flag:** `-sF` (flag represents value **0 or 1**)
- **Open port:** No response
- **Closed port:** RST returned
- **Use:** Stealth — bypasses some stateless firewalls

```bash
nmap -sF 192.168.1.1
```

---

## 7. ACK Scan

- **Sends:** ACK flag only
- **Nmap flag:** `-sA`
- **Used for:** **Firewall mapping** — determine if ports are filtered or unfiltered
- **NOT used to find open ports**

```bash
nmap -sA 192.168.1.1 -p 80
```

---

## 8. NULL Scan

- **Sends:** No flags set (empty TCP packet)
- **Nmap flag:** `-sN`
- **Same response pattern** as FIN and XMAS scans

```bash
nmap -sN 192.168.1.1
```

---

## 9. Idle Scan

- **Also called:** Zombie scan
- **Ultimate stealth** — uses a third-party "zombie" host
- Source IP is completely hidden
- Nmap flag: `-sI <zombie_host>`

```bash
nmap -sI zombie_host 192.168.1.1
```

---

## 10. Banner Grabbing

- **Purpose:** Identify **service name and version** from connection banners
- **How:** Connect to open port and read the response header
- **Tools:** [[Netcat - Complete Guide]], Telnet, Nmap `-sV`

```bash
# With Netcat
nc -v 192.168.1.1 80

# With Nmap
nmap -sV 192.168.1.1
```

---

## 11. Vulnerability Scanning

Scanning for known **CVEs and weaknesses** in discovered services.

**Tools:** Nessus, OpenVAS, Nmap NSE (`--script=vuln`)

---

## 12. Malware Scan

Detecting **malware presence** on systems.

**Tools:**
- Malware Scanners (ClamAV, etc.)
- Cloud-based infrastructure scanning
- 2FA-based detection

---

## Flag Values (Binary)

> 💡 **Flags represent values of 0 or 1** (binary):
> - `-sF` (FIN) = FIN flag = 1
> - `-sA` (ACK) = ACK flag = 1
> - `-sX` (XMAS) = FIN + PSH + URG all = 1

---

## Important Nmap Scan Flags Summary

```
-sT  → TCP Connect Scan
-sU  → UDP Scan  
-sS  → SYN Scan (Stealth)
-sN  → NULL Scan
-sP  → Ping Scan (no port)
--top-ports  → Scan N most common ports
-p   → Specific port(s)
-p-  → All ports
```

---

## Related Topics
- [[Scanning in Ethical Hacking]]
- [[Nmap - Complete Guide]]
- [[hping3 - Packet Crafting]]
- [[Banner Grabbing]]
- [[Vulnerability Scanning]]
- [[QA - Scanning Scenarios]]

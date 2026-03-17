# 📊 SNMP Enumeration

> **MOC:** [[MOC - Enumeration]] | **Tags:** `#snmp` `#enumeration` `#scanning`

---

## What is SNMP?

**SNMP (Simple Network Management Protocol)** is a protocol used for **network device management and monitoring**.

**Port:** UDP **161/162**
**Layer:** Application Layer (Session Layer)

---

## Why SNMP Matters in Ethical Hacking

SNMP provides **full speed OID (Object Identifier) from various sources** — revealing detailed network device information including:
- Device types and configurations
- Network topology
- Running services
- User accounts (in some cases)
- Community strings (like passwords)

---

## SNMP Architecture

```
Manager (NMS)  ←→  Agent (Device)
     ↑                    ↓
  Management         Management
   Database           Database
   (MIB)               (MIB)
```

**Key Components:**
| Component | Description |
|---|---|
| **Agent** | Software on managed device; collects & sends data |
| **Manager** | Network management station; queries agents |
| **Management Gear** | Routers, switches, servers being managed |
| **Management Stats** | Statistics collected from devices |
| **Network Management System (NMS)** | Platform that manages all agents |
| **Management Database (MIB)** | Database of managed objects |
| **Management Protocol** | SNMP protocol itself |
| **Protective Mission from SNMP** | Security measures |

---

## SNMP Versions

| Version | Security | Notes |
|---|---|---|
| **SNMPv1** | No encryption, plain text | Oldest, least secure |
| **SNMPv2** | No encryption, plain text | Faster, more features |
| **SNMPv3** | ✅ Encryption + Authentication | Most secure — **recommended** |

> ⚠️ **SNMPv3 is most secure** — use for authentication.

---

## SNMP Community Strings

Community strings act like **passwords** for SNMP access:

| String | Access Type |
|---|---|
| `public` | Read-only (default — often unchanged!) |
| `private` | Read-write (default — often unchanged!) |

> 🔴 **Major vulnerability:** Default community strings are often never changed!

---

## SNMP Enumeration Techniques

From your notes:

### Different File Enumeration
- **Shares**
- **Telnet**
- **Null connections**
- **NetBios enumeration**
- **NTP enumeration**
- **DNS zone transfer**
- **Digital passwords**
- **Big password in databases**

### SMB File Enumeration (also called)
- **Vulnerability Scanning** — overall scanning
- **OS detection**
- **Ghost NetNull actions**
- **Overall Scanning**

---

## SNMP Enumeration for Platforms (Use which)

**Commands:**
```bash
# Nmap NSE for SNMP
nmap -sU -p 161 --script=snmp-info <IP>
nmap -sU -p 161 --script=snmp-brute <IP>

# snmpwalk - enumerate all OIDs
snmpwalk -v 2c -c public <IP>

# snmpwalk specific OID
snmpwalk -v 2c -c public <IP> .1.3.6.1.2.1.1

# Force SNMP v1
snmpwalk -v1 -c public <IP>
```

---

## SMB Enumeration (Windows File Sharing)

### What SMB Exposes
- **Null sessions** — anonymous access
- **IRC, RPC** — inter-process communication
- **NetBIOS session** — Windows name resolution
- **Username enumeration**
- **Null user enumeration**

### SMB Enumeration Methods
1. **Different file enumeration**
2. **Telnet**
3. **Null connections**

### SMB Enumeration Tools

**1. NBTscan**
```bash
# Enumerate NetBIOS names
nbtscan <IP>
nbtscan -H <IP> -u <user> -a <password> -L <target>
```

**2. Smbclient**
```bash
# List shares
smbclient -L <IP> -N

# Connect to share anonymously
smbclient \\\\<IP>\\<share> -N
```

**3. Smbmap**
```bash
smbmap -H <IP>
smbmap -H <IP> -u <user> -p <password>
```

**4. SMBenumeration via Metasploit**
```bash
# In Metasploit
use auxiliary/scanner/smb/smb_enumshares
```

---

## Port Scanning by Nmap (Related)

### Types of Port Scanning
- **Open** → service running
- **Filtered** → firewall blocking
- **Closed** → no service

### SNMP Port Scanning
```bash
nmap -sU -p 161 <IP>
```

---

## Enumeration in Context

### ENUMERATION Overview
- **Techniques:** Shares, Telnet, Null connections, DNS zone transfers
- **DNS zone transfer** — major technique
- **Big password in databases** — credential attacks

---

## Tools in SNMP

| # | Tool | Use Case |
|---|---|---|
| 1 | snmpcheck | Use cases — shows active network metadata |
| 2 | Snmpwalk | Show active SNMP enabled data |
| 3 | snmp tools -v 19 | `-public -c` |
| 4 | Analyze OID values | (thisiD) |
| 5 | Analyze & document findings | `snmp-walk -v1 cess 2.69.69` |

---

## SNMP Steps (From Notes - 5 Steps)

1. **Identify the addition of device address** via open sources, NSE tools
2. **Identify SNMP community** settings via NMS script/tools
3. **Collect** basic test systems & file individual account location services
4. **Isolate** systems and file accounts for individual connected services
5. **Analyse, interpret, resources, account & corporate** findings (thisiD)

---

## SNMP Reconnaissance Steps

**To find vulnerabilities via source:**
```bash
nmap -sU -p 161 --script snmp-scan <IP>
# Find using:
# 4 mvi -vuln-ssh NSE scan/IP
# 4 mvi -vn NSE scan/IP -to <port> 2 <port> <IP>
```

---

## Advanced Techniques

### hping3 for SNMP-related scanning
*(See [[hping3 - Packet Crafting]])*

---

## Related Topics
- [[Enumeration - Overview]]
- [[DNS Enumeration]]
- [[NetBIOS Enumeration]]
- [[SNMP Tools]]
- [[Scanning in Ethical Hacking]]
- [[Nmap - Complete Guide]]
- [[QA - Enumeration Scenarios]]

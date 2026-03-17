# ❓ QA - Enumeration Scenarios

> **MOC:** [[MOC - Questions and Answers]] | **Tags:** `#qa` `#enumeration` `#snmp`

---

## Q1 - Mid-Size Organization SNMP Scenario

**Scenario:**
> A mid-size organization reports a discrepancy between authorized remote connection options between currently accessed organizational resources. The security team, including information has between automated management & possible misconfigurations to facilitate problems & connected to the network platform resources.

**Part A) Analyze the situation — list of the command(s) for reconnaissance following:**

```bash
# 1. Enumerate open ports & exposed services
nmap -sV <IP>

# 2. Identify active network connections by port position
nmap -p- --open <IP>

# 3. Collect basic test systems
nmap -p- <IP> -sV

# 4. Scan for active network connections & fill nightly report
nmap -sS -p 22,80,443 <IP>

# 5. Locally affect network & corporate findings
nmap -A <IP>
```

**Part B) Answers:**

1. **Performs a TCP SYN stealth scan** on an online secured of 192.161.0/es port
   - For network → allow **SNMP** scanning capability
   - Port is well for service restriction → SYN-ACK received
   - It is well for service/version, distribution identifiers → host enumeration

2. **Route Nmap traffic through online**, thought as an proxy base:
   - For bypassing network distribution → active → -N -h to identify
   - By host Nmap traffic through mediation alternative

3. **Perform relay service, destination using** distribution via -A (detect network distribution)
   - Service names & versions revealed
   - Identify for bypassing/distribution service alternat, determine in more proven for anonymous

4. **Email & User Enumeration** via SMTP VRFY, password reset abuse

5. **Locally nmap -- script=scan-data** → to find all account-related data
   - Nmap --script-vices-2.69.69 -- find-account-data to connect to the accounting process

---

## Q2 - SNMP Enumeration Commands from Assignment

**Commands from your notes - Assignment:**

**A1) List the use of following commands:**
```bash
nmap 192.168.1.1-55
nmap -sn 192.168.1.1-55
nmap --top-ports [top scan] 192.168.1.1/24
nmap -sn -p [1] 192.168.1.1/24
```

**A2) Use SSH version 3 command (snmpwalk):**
```bash
snmpwalk -v1 -c public <IP>
# v1: uses community string "public"

snmpwalk -v2c -c public <IP>
# v2c: community-based, faster

snmpwalk -v3 -u user -l authPriv -a SHA -A password -x AES -X password <IP>
# v3: authenticated + encrypted (most secure)
```

---

## Q3 - Port Scanning by Nmap (from Notes)

**Types of port scanning:**

| Type | State | Description |
|---|---|---|
| **Open** | Service running | Port responds, accepts connections |
| **Filtered** | Firewall | Packet filtered, state unknown |
| **Closed** | No service | Port accessible, no service |

**Nmap scanning by Nmap:**
- **Type of portscanning:**
  - Techniques: Shares, Telnet, Null connections
  - DNS zone transfer
  - Digital passwords
  - Big password in databases

---

## Q4 - hping3 Enumeration (Assignment 3)

**Q3 Explain following enumeration commands:**

**a) Give:facebook.com|site:twitter.com|site:instagram.com (intext:"login")**
→ Google Dorking for social media login pages — finds login forms indexed on these platforms

**b) cache:www.google.com, cache:amazon.com**
→ Views Google's cached version of these websites — useful for seeing old content/configurations

**c) allintext:"keyword"**
→ Searches for ALL specified keywords in the body text of pages simultaneously

---

## SNMP Enumeration Step Summary

**Steps in SNMP Enumeration (5 tools from notes):**

1. **snmpcheck** — Use cases: shows active network metadata
2. **snmpwalk** — Shows active SNMP enabled data
3. **Gnmp tools** — `-sU` `-p 161` `-public -c`
4. **Analyze OID values** — (thisiD)
5. **Analyze & document findings** — `snmp-walk -v1 cess 2.69.69` — same with version 2.69 (ID)

---

## Related Topics
- [[Enumeration - Overview]]
- [[SNMP Enumeration]]
- [[SMB Enumeration]]
- [[DNS Enumeration]]
- [[Nmap - Complete Guide]]
- [[QA - Scanning Scenarios]]

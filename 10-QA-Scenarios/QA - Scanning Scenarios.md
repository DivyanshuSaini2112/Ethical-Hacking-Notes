# ❓ QA - Scanning Scenarios

> **MOC:** [[MOC - Questions and Answers]] | **Tags:** `#qa` `#scanning` `#nmap` `#hping3`

---

## Assignment Day 2

### Q1 - Unauthorized Traffic through Firewall

**Scenario:**
> An organization suspects that its perimeter firewall is incorrectly configured, allowing unauthorized traffic to pass through it. An ethical hacker is asked to analyze the firewall behavior. An ethical hacker is asked to analyze the firewall behavior using packet crafting tools to perform SYN & ACK scans on target port 22,80.

**Questions:**
- A) Analyze how hping3 commands manually validate open ports using Nmap & ethical hacker's wants
- B) Analyze how hping3 behavior can be used in that configuration validation
- C) Explain how filter results confirm enumeration accuracy

### Answer 1A) - Nmap performs automated scanning & hping3 allows manual packet verification

**Nmap performs automated scanning:**
- Confirms firewall behavior
- Packet-level responses
- False positives or negatives from Nmap

**Combined Approach:**
```bash
# First - Nmap automated scan
nmap -sS -p 22,80 <target>

# Then - hping3 manual verification
hping3 -S -p 22 <target>   # Test SSH
hping3 -S -p 80 <target>   # Test HTTP
```

### Answer 1B) - hping3 for Firewall Behavior Validation

**SYN Scan:**
```bash
hping3 -S -p 22 <target>
hping3 -S -p 80 <target>
```

**TCP Flags & Meanings:**
- TCP flag by allowing response, filter, behavior can be:
  - **SYN-ACK received** → Port is OPEN
  - **RST received** → Port is CLOSED
  - **No response** → Port is FILTERED (firewall dropping)

**It is well for service/version, distribution — identify:**
- Route (Network via -N) to identify, firewall behavior can be inferred

**For bypassing methodologies (proxy-based):**
- Firewall allows on blocked active control scanner (filtered)

**SYN Scan (hping3):**
```bash
hping3 -S -p 22 <target>
hping3 -S -p 80 <target>
```
- Testing ports 22 (SSH) & 80 (HTTPS) fully using web filtering solution
- Firewall allows on blocked active control scanner (filtered)

**ACK Scan:**
```bash
hping3 -A -p 80 <target>
```
- Used to detect ACK Flag
- Used to determine stateful firewall behavior

**Verify RST packets returned for closed ports:**
```bash
hping3 -A -p 80 <target>
RST reply → Port is ACCESSIBLE (unfiltered)
Confirms firewall is NOT silently dropping traffic
```

### Answer 1C) - Filter Results & Enumeration Accuracy

- Matching responses between hping3 and Nmap **confirms port states are accurate**
- Any discrepancies indicate either **firewall interference** or **IDS/IPS behavior**
- Packet-level validation ensures **reliable enumeration**

---

## Assignment Day 1 Commands

### Q1 - List the use of following commands:

```bash
# a) nmap 192.168.1.1-55
→ Scans all hosts in range 192.168.1.1 to 192.168.1.55 with default port scan

# b) nmap -sn 192.168.1.1-55  
→ Ping scan only — discovers live hosts without port scanning

# c) nmap --top-ports [top scan] 192.168.1.1/24
→ Scans the most common N ports on entire /24 subnet

# d) nmap -sn -p [1] 192.168.1.1/24
→ Combined ping + specific port scan on /24 subnet

# e) nmap -sU -p [1] 161 -- script=snmp 3-scanning flags
→ UDP scan on port 161 (SNMP) with NSE snmp scripts

# Proxies (bypass):
nmap -sV --proxies http://proxy1 URL, http://proxy2 URL

# Script: send-ip
nmap -sV --script-send-ip 192.168.1.1 p161 --script=snmp-brute-force-scanning flags
```

---

## Nmap vs hping3 Complementary Use

### Q2 - Explain following enumeration commands

**Q3 - Explain following commands:**

**A. snmpwalk -v1 -c public <target>**
→ Queries ALL OIDs using SNMP v1 with community string "public" — basic SNMP enumeration

**B. snmpwalk -v2 -u user A public <password> -L <target>**
→ SNMP v2 enumeration with authenticated user credentials

**C. snmpwalk -v3 -u user A -L <target> (commands)**
→ SNMP v3 (most secure) — uses authentication and encryption; all accessible SNMP data

*(SNMP v3 is most secure)*

---

## Day 2 - hping3 Assignment (B1, B2, C)

### B1 - hping3 -S -p 443 <target>
- SYN-ACK → Port OPEN
- No response → FILTERED
- RST → CLOSED port

### B2 - hping3 -S -p <closed-port> <target>
- RST → closed port
- Confirms firewall is NOT silently dropping traffic

### C - Matching Responses b/w Nmap & hping3 to confirm port states are accurate
- Any discrepancies indicate firewall interference or IDS/IPS behavior
- Packet-level validation → reliable enumeration

---

## Related Topics
- [[Scanning in Ethical Hacking]]
- [[Nmap - Complete Guide]]
- [[hping3 - Packet Crafting]]
- [[Types of Port Scans]]
- [[SNMP Enumeration]]

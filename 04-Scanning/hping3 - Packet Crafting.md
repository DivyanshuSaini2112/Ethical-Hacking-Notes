# ⚡ hping3 - Packet Crafting

> **MOC:** [[MOC - Tools]] | **Tags:** `#hping3` `#scanning` `#tools` `#packet-crafting`
> Related: [[Nmap - Complete Guide]], [[Types of Port Scans]]

---

## What is hping3?

**hping3** is a command-line oriented **TCP/IP packet assembler and analyzer**. Unlike [[Nmap - Complete Guide]] which automates scanning, hping3 allows **manual, packet-level control**.

**Key uses:**
- Firewall testing and rule validation
- TCP/IP stack auditing
- Manual packet crafting
- SYN flood testing
- Traceroute with custom packets
- **Confirming Nmap results** with packet-level verification

---

## Basic Syntax

```bash
hping3 [options] <target>
```

---

## Core Commands

### TCP SYN Scan (Stealth Scan)
```bash
# Perform a TCP SYN stealth scan on port 443
hping3 -S -p 443 <target>
```
**Responses:**
- **SYN-ACK received** → Port is **OPEN**
- **RST received** → Port is **CLOSED**
- **No response** → Port is **FILTERED** (firewall dropping)

### SYN Scan on Specific Port
```bash
hping3 -S -p 443 <target>
```

### ACK Scan (Firewall Detection)
```bash
# Used to determine if a port is filtered by firewall
hping3 -A -p 80 <target>

# Used to determine unfiltered (stateless firewall behavior)
hping3 -A -p 80 <target>
```

### Port Range Scanning
```bash
hping3 -S --scan 20-1024 <target>
```

### Multiple Ports
```bash
hping3 -S --scan 22,80,443 <target>
```

---

## Flags Reference

| Flag | Meaning |
|---|---|
| `-S` | Set SYN flag |
| `-A` | Set ACK flag |
| `-F` | Set FIN flag |
| `-R` | Set RST flag |
| `-U` | Set URG flag |
| `-P` | Set PSH flag |
| `-p <port>` | Destination port |
| `--scan` | Port range mode |
| `-c <count>` | Number of packets to send |
| `-i <interval>` | Interval between packets |
| `--rand-source` | Random source IP (spoofing) |
| `-V` | Verbose output |
| `-n` | Numeric output (no DNS) |

---

## SYN Scan Deep Dive

```bash
hping3 -S -p 443 <target>
```

**What happens:**
1. hping3 sends **SYN packet** to target port
2. If **SYN-ACK** returned → Port is OPEN
3. If **RST** returned → Port is CLOSED
4. If **no response** → Port is FILTERED (firewall blocking)

---

## ACK Scan for Firewall Analysis

```bash
# Check if port 80 is unfiltered
hping3 -A -p 80 <target>
```

**Responses:**
- **RST returned** → Port is **UNFILTERED** (firewall doesn't block)
- **No response** → Port is **FILTERED** by firewall

**Used for:** Confirming whether firewall is **stateless or stateful**

---

## hping3 vs Nmap - When to Use Which

| Scenario | Use Nmap | Use hping3 |
|---|---|---|
| Broad network scan | ✅ | ❌ |
| Quick host discovery | ✅ | ❌ |
| Firewall rule validation | ❌ | ✅ |
| Custom packet crafting | ❌ | ✅ |
| Confirm Nmap results | ❌ | ✅ |
| Service version detection | ✅ | ❌ |
| NSE script execution | ✅ | ❌ |

> 💡 **Combined workflow:**
> 1. Use **Nmap** for automated, broad scanning
> 2. Use **hping3** for manual packet verification to confirm firewall behavior, packet-level responses, and resolve false positives/negatives

---

## From Your Notes - Assignment Commands

### SYN Scan Examples (Assignment Day 2)

**B1 - Test if port 443 is open:**
```bash
hping3 -S -p 443 <target>
```
- SYN-ACK → Port OPEN
- No response → FILTERED
- RST → CLOSED port

**B2 - Test a closed port to confirm firewall behavior:**
```bash
hping3 -S -p <closed-port> <target>
```
- RST → closed port; confirms firewall is **not silently dropping traffic**

**C - Match responses between Nmap and hping3:**
- Matching responses b/w Nmap & hping3 confirms port states are accurate
- Any discrepancies indicate either **firewall interference** or **IDS/IPS behavior**
- Packet-level validation ensures **reliable enumeration**

---

## Day 2 Assignment - Firewall Analysis

**Q: An organization suspects unauthorized traffic is penetrating firewall. Ethical hacker is asked to analyze firewall behavior.**

**A - Identify Open Ports Manually:**
```bash
hping3 -S -p 22 <target>    # Test SSH
hping3 -S -p 80 <target>    # Test HTTP
```

**B - Analyze firewall behavior using packet crafting:**
```bash
# SYN Scan
hping3 -S -p 22 <target>
# ACK Scan  
hping3 -A -p 80 <target>
```

**SYN Scan Analysis:**
- Testing ports 22 (SSH) & 80 (HTTP) fully using web filtering solution
- Firewall allows on blocked active control scanner (filtered)

**ACK Scan:**
```bash
hping3 -A -p 80 <target>
```
- Used to detect ACK Flag
- Used to determine stateful firewall behavior

**Verify if RST packets are returned to close port:**
```bash
hping3 -A -p 80 <target>
```
- RST reply → Port accessible (unfiltered)
- Confirms firewall is not silently dropping traffic

**C - Explain how filter results confirm enumeration accuracy:**
- Matching responses between hping3 and Nmap confirms port states
- Any discrepancies indicate firewall interference or IDS/IPS behavior
- Packet-level validation ensures reliable enumeration

---

## Related Topics
- [[Nmap - Complete Guide]]
- [[Types of Port Scans]]
- [[Scanning in Ethical Hacking]]
- [[QA - Scanning Scenarios]]
- [[Netcat - Complete Guide]]

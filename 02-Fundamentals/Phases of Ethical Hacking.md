# 🔄 Phases of Ethical Hacking

> **MOC:** [[MOC - Ethical Hacking Fundamentals]] | **Tags:** `#ethical-hacking` `#phases` `#methodology`
> Back to [[Ethical Hacking - Definition and Overview]]

---

## The 5 Phases

```
Phase 1: Reconnaissance
      ↓
Phase 2: Scanning
      ↓
Phase 3: Gaining Access
      ↓
Phase 4: Maintaining Access
      ↓
Phase 5: Clearing Tracks
```

---

## Phase 1: 🔍 Reconnaissance (Information Gathering)

**Also called:** Footprinting, OSINT

**Goal:** Collect as much information about the target as possible **without directly interacting** with it (passive) or with limited interaction (active).

**Sub-phases:**
- **Passive:** OSINT, WHOIS, DNS records, social media
- **Active:** Network scanning, port probing

**Key tools:** Google Dorking, WHOIS, Shodan, Maltego

> See: [[Reconnaissance - Overview]], [[Footprinting - Active and Passive]], [[OSINT Techniques]]

---

## Phase 2: 📡 Scanning

**Goal:** Identify **open ports, live systems, and services** running on the target.

**Types of scanning:**
- **Port Scanning** — what ports are open
- **Vulnerability Scanning** — what CVEs exist
- **Network Scanning** — map the network topology

**Key tools:** [[Nmap - Complete Guide]], [[hping3 - Packet Crafting]], Nessus

> See: [[Scanning in Ethical Hacking]], [[Types of Port Scans]]

---

## Phase 3: ⚡ Gaining Access (Exploitation)

**Goal:** Exploit identified vulnerabilities to **break into** the target system.

**Methods:**
- Password attacks (brute force, dictionary)
- Exploiting unpatched software
- Social engineering
- SQL injection, buffer overflow

**Key tools:** Metasploit, SQLmap, John the Ripper

> See: [[Privilege Escalation]], [[Attack Vectors and Vulnerabilities]]

---

## Phase 4: 🔒 Maintaining Access

**Goal:** Ensure **persistent access** to the compromised system for further exploitation.

**Methods:**
- Installing backdoors / rootkits
- Creating hidden admin accounts
- Using Trojans to maintain C2 (Command & Control)

**Why it matters:** Tests how long an attacker could stay in a system undetected.

---

## Phase 5: 🧹 Clearing Tracks

**Goal:** Remove evidence of the intrusion to **avoid detection**.

**Methods:**
- Deleting log files
- Modifying timestamps
- Using anti-forensics tools

**Ethical Hacker's role:** Document what was cleared to show how a real attacker could evade detection.

---

## Tools for Each Phase (3 per phase)

| Phase | Tool 1 | Tool 2 | Tool 3 |
|---|---|---|---|
| Reconnaissance | WHOIS | Maltego | Shodan |
| Scanning | [[Nmap - Complete Guide]] | [[hping3 - Packet Crafting]] | Nessus |
| Gaining Access | Metasploit | SQLmap | Hydra |
| Maintaining Access | Netcat | Meterpreter | Cobalt Strike |
| Clearing Tracks | Meterpreter | Log-wipers | Timestomp |

---

## Key Insight

> ⚠️ **Reconnaissance remains effective even when firewalls and IDS are updated** — because:
> - Firewalls only block traffic, **not intelligence**
> - IDS detects signatures, **not intent behind them**
> - Reconnaissance relies heavily on **legitimate and public data**

---

## Related Topics
- [[Reconnaissance - Overview]]
- [[Scanning in Ethical Hacking]]
- [[Privilege Escalation]]
- [[Cybersecurity Threat Landscape]]
- [[Skills Required for Ethical Hacking]]

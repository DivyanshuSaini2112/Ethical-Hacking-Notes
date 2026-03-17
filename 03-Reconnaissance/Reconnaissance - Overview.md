# 🔍 Reconnaissance - Overview

> **MOC:** [[MOC - Reconnaissance]] | **Tags:** `#reconnaissance` `#osint` `#footprinting`

---

## What is Reconnaissance?

**Reconnaissance (Recon)** is a way of **gathering information** about systems, networks, and users about a target. It can be **both active and passive**.

It is **Phase 1** of ethical hacking — the attacker collects as much data as possible before launching any attack.

> 🔑 **Key Insight:** Reconnaissance remains effective even when an organization has updated firewalls and IDS, because it relies on **legitimate and public data**.

---

## Active vs Passive Reconnaissance

| Parameter | Passive Recon | Active Recon |
|---|---|---|
| Definition | Gathering info **without directly** interacting with target | Gathering info **by directly** interacting with target |
| Detection Risk | Low — target doesn't know | Higher — leaves traces |
| Examples | OSINT, WHOIS, Google Dorking, LinkedIn | Network scanning, port probing, ping sweeps |
| Tools | Shodan, Maltego, WHOIS | [[Nmap - Complete Guide]], [[hping3 - Packet Crafting]] |

---

## Passive Reconnaissance Techniques

### 1. OSINT (Open Source Intelligence)
Gathering information from publicly available sources.
> See: [[OSINT Techniques]]

Sub-types:
- **Company website, GitHub, LinkedIn, job posts** — reveal technologies used, employee names
- **Search Engine Intelligence** — Google Dorking
- **DNS & Certificate Transparency** — subdomain discovery, SSL certificate metadata
- **Data Breaches & Leaks** — email/password reuse databases

### 2. Search Engine Intelligence
- **Google Dorking** — using advanced operators to find exposed data
> See: [[Google Dorking]]

### 3. DNS & Certificate Transparency
- Subdomain discovery
- SSL certificate metadata

### 4. Data Breaches & Leaks
- Email/password reuse from breach databases

---

## Active Reconnaissance Techniques

### 1. Network Scanning
- Open ports, live hosts
> See: [[Scanning in Ethical Hacking]]

### 2. Service Enumeration
- Service versions reveal older, known vulnerabilities
> See: [[Enumeration - Overview]]

### 3. Web Application Enumeration
- Brute force of directories
> See: [[Web Based Reconnaissance]]

### 4. Email & User Enumeration
- SMTP VRFY, password reset abuse

---

## Types of Recon Information

### In-Person Reconnaissance
1. **Dumpster Diving** — searching physical trash for sensitive info
2. **Shoulder Surfing** — watching someone type credentials
3. **Tailgating** — following authorized personnel through secure doors
4. **Social Engineering** — manipulating people into revealing info
> See: [[In-Person Reconnaissance Techniques]], [[Social Engineering Reconnaissance]]

---

## Web-Based Recon
- **Website enumeration**
- **Directory enumeration**
> See: [[Web Based Reconnaissance]]

---

## IP Address Reconnaissance
- **WHOIS lookup**
- **DNS Enumeration**
- **Network Scanning**
- **Port Scanning**
> See: [[WHOIS and DNS Footprinting]], [[IP Address Reconnaissance]]

---

## Google Dorking for Recon
> See: [[Google Dorking]]

---

## SOCMINT (Social Media Intelligence)
Using social media platforms for intelligence gathering.
> See: [[SOCMINT - Social Media Intelligence]]

---

## Physical Device Reconnaissance
1. **Wi-Fi Network Analysis**
2. **Bluetooth Discovery/Location**
3. **RF Analysis**
4. **IoT Device Discovery**
> See: [[Physical Device Reconnaissance]]

---

## Email Intelligence (EMAILINT)
1. **Email Harvesting**
2. **Email Verification**
3. **Email Header Analysis**
> See: [[Email Intelligence (EMAILINT)]]

---

## Digital Footprint & Metadata Analysis

| Source | Data Type |
|---|---|
| HaveBeenPwned | Breach records |
| Pastebin Sites | Leaked credentials |
| Dark Web Marketplaces | Stolen data |
| Document Metadata | Author info, software version |
| Image EXIF Data | Location, device info |
| Code Repositories | API keys, credentials |
| Forum Posts | Employee discussions |

> See: [[Digital Footprint and Metadata Analysis]]

---

## Snooping vs Spoofing

> 💡 **Important distinction:**
> - **Snooping** = passive eavesdropping (masquerading; can be active)
> - **Spoofing** = actively impersonating another system/user

---

## Related Topics
- [[Footprinting - Active and Passive]]
- [[OSINT Techniques]]
- [[Google Dorking]]
- [[WHOIS and DNS Footprinting]]
- [[Phases of Ethical Hacking]]
- [[Scanning in Ethical Hacking]]

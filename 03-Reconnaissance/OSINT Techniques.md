# 🔍 OSINT Techniques

> **MOC:** [[MOC - Reconnaissance]] | **Tags:** `#osint` `#reconnaissance` `#intelligence`
> Back to [[Reconnaissance - Overview]]

---

## Definition

**OSINT (Open Source Intelligence)** is the collection and analysis of information gathered from **publicly available sources** to produce actionable intelligence.

In ethical hacking: used to gather information about targets **without directly interacting** with them.

---

## OSINT Categories

### 1. SOCMINT (Social Media Intelligence)
Extracting intelligence from social media platforms.

**What it reveals:**
- Employee names and roles
- Company events and news
- Personal relationships (social engineering targets)
- Office locations and schedules
- Exposed credentials in posts

> See: [[SOCMINT - Social Media Intelligence]]

---

### 2. EMAILINT (Email Intelligence)

**Sub-techniques:**

| Technique | Purpose |
|---|---|
| **Email Harvesting** | Collect email addresses from public sources |
| **Email Verification** | Confirm email validity without sending |
| **Email Header Analysis** | Trace email routing, identify servers |

> See: [[Email Intelligence (EMAILINT)]]

---

### 3. Digital Footprint & Metadata Analysis

**Sources:**

| Source | Data Available |
|---|---|
| **HaveIBeenPwned** | Check if email/password appears in breaches |
| **Pastebin Sites** | Leaked credentials, code snippets |
| **Dark Web Marketplaces** | Stolen data for sale |
| **Document Metadata** | Author name, software version, GPS data |
| **Image EXIF Data** | Location, camera model, timestamp |
| **Code Repositories (GitHub)** | API keys, credentials, internal paths |
| **Forum Posts** | Technical discussions revealing architecture |

> See: [[Digital Footprint and Metadata Analysis]]

---

### 4. Data Breach & Leaked Database Analysis

| Source | What It Contains |
|---|---|
| **HaveIBeenPwned** | Email addresses in known breaches |
| **Pastebin Sites** | Pasted credentials, configs |
| **Dark Web Marketplaces** | Full credential dumps |
| **Public Data Breaches** | Large dataset dumps |

> See: [[Data Breach and Leaked Database Analysis]]

---

### 5. DNS Intelligence

**Techniques:**

| Technique | Purpose |
|---|---|
| **Zone Transfers** | Download entire DNS zone if misconfigured |
| **DNS Cache Snooping** | See what domains a server has visited |
| **Reverse DNS Lookup** | Map IPs back to domain names |
| **DNS History** | Find old IP addresses and infrastructure |

> See: [[DNS Intelligence]]

---

### 6. Web-Based Recon

- **Website Enumeration** — map site structure
- **Directory Enumeration** — find hidden paths
- **Google Dorking** — advanced search operators

> See: [[Web Based Reconnaissance]], [[Google Dorking]]

---

### 7. IP Address Reconnaissance

| Technique | Purpose |
|---|---|
| **WHOIS Lookup** | Domain registration info |
| **DNS Enumeration** | DNS record mapping |
| **Network Scanning** | Live host detection |
| **Port Scanning** | Open port identification |

> See: [[WHOIS and DNS Footprinting]], [[IP Address Reconnaissance]]

---

## OSINT Tools Quick Reference

| Tool | Category | Purpose |
|---|---|---|
| Maltego | All-in-one | Visual link analysis and OSINT |
| Shodan | Network | Search internet-connected devices |
| theHarvester | Email/Domain | Email and domain enumeration |
| Recon-ng | Framework | Modular OSINT framework |
| SpiderFoot | Automated | Automated OSINT collection |
| WHOIS | Domain | Domain registration lookup |
| Hunter.io | Email | Email address discovery |
| LinkedIn | SOCMINT | Professional network intelligence |

---

## Related Topics
- [[Reconnaissance - Overview]]
- [[Footprinting - Active and Passive]]
- [[Google Dorking]]
- [[WHOIS and DNS Footprinting]]
- [[Social Engineering Reconnaissance]]
- [[Phases of Ethical Hacking]]

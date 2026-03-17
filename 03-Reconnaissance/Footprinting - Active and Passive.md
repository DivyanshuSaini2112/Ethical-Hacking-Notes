# 👣 Footprinting - Active and Passive

> **MOC:** [[MOC - Reconnaissance]] | **Tags:** `#footprinting` `#reconnaissance`
> Back to [[Reconnaissance - Overview]]

---

## Definition

**Footprinting** is the process of **gathering information about a target system** that can be used to execute a successful cyber attack.

It is the **first step** in ethical hacking and penetration testing.

> Footprinting = gathering info about target system that can be used to execute a successful cyber attack.

---

## Two Types of Footprinting

### 🔕 Passive Footprinting
Gathering information **without directly interacting** with the target.
- Uses publicly available information
- Target does NOT know they are being observed
- Leaves no trace

**Define + Example for each:**

| Technique | Example |
|---|---|
| WHOIS Lookup | Look up domain registration info without contacting the org |
| DNS Record Analysis | Find all subdomains via public DNS records |
| Google Dorking | Search for exposed files without visiting the site |
| Social Media Analysis | View LinkedIn for employee names and job titles |
| Job Posting Analysis | A job post saying "must know AWS" reveals cloud stack |

### 🔔 Active Footprinting
Gathering information by **directly interacting** with the target.
- Can leave traces in logs
- Higher detection risk
- More precise data

**Define + Example for each:**

| Technique | Example |
|---|---|
| Ping Sweeps | Send ICMP ping to find live hosts on a subnet |
| Port Scanning | Use Nmap to find open ports on target server |
| Banner Grabbing | Connect to a service to read its version banner |
| DNS Zone Transfer | Request a DNS zone transfer to enumerate subdomains |
| Network Traceroute | Trace the network path to identify intermediate routers |

---

## Types of Information Gathered in Footprinting

| Category | Data Collected |
|---|---|
| **Network** | Firewalls, IP addresses, network maps |
| **System** | OS version, running services |
| **Organization** | Employee emails, phone numbers, structure |
| **Security** | Security policies, IDS/IPS presence |
| **Application** | URLs, Server Configs, VPNs |
| **Credentials** | Email IDs & Passwords (from breaches) |

---

## Domain & Registration Footprinting

**WHOIS Records Reveal:**
- Organization name
- Domain registrar
- Contact emails & phone numbers

This helps attackers **identify responsible teams** (IT, admin) and **launch domain-specific phishing attacks**.

---

## DNS Footprinting

**Via DNS record analysis:**
- Subdomain discovery (DNS zone transfers)
- **Network Footprinting via Public IP Mapping**
  - ASN records
  - Cloud provider metadata → map network boundaries & identify exposed services

---

## Employee Email Enumeration

- Via **breach databases**
- Social profiles enable **phishing** and **organizational structure mapping**

---

## Countermeasures to Footprinting (At Least 5)

1. **Restrict public information** on websites and job posts
2. **Implement a robust robots.txt file**
3. **Use "NoIndex" and "NoFollow" tags** on sensitive pages
4. **Regularly conduct website audits**
5. **Limit file and directory permissions** on web servers
6. **Train employees** on social engineering awareness
7. **Minimize DNS record exposure**

---

## Advantages of Footprinting (At Least 5)

1. Identifies the **attack surface** before real attackers do
2. Helps in **planning targeted penetration tests**
3. Reveals **publicly accessible sensitive data**
4. Helps discover **employee vulnerabilities** through social engineering testing
5. Builds a **complete network map** for security assessment
6. Uncovers **misconfigured cloud/network resources**

---

## Kirchhoff's Rule in Cryptography (Related to Key Pool)

> 📝 **Kirchhoff's Principle:** A cryptographic system should be secure even if everything about the system, except the key, is **public knowledge**.
> - Security should depend on the **key**, not on the secrecy of the algorithm
> - Relevant in footprinting when attackers discover encryption methods used

---

## Tools

| Tool | Type | Purpose |
|---|---|---|
| WHOIS | Passive | Domain registration info |
| NeoTrace | Passive | Visual traceroute |
| ICMPsweep | Active | Host discovery |
| Maltego | Passive | Visual link analysis |
| Shodan | Passive | Internet-connected device search |

---

## Related Topics
- [[Reconnaissance - Overview]]
- [[WHOIS and DNS Footprinting]]
- [[OSINT Techniques]]
- [[Google Dorking]]
- [[Phases of Ethical Hacking]]
- [[QA - Footprinting Scenarios]]

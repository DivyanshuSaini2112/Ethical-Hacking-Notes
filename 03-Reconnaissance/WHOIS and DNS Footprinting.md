# 🌐 WHOIS and DNS Footprinting

> **MOC:** [[MOC - Reconnaissance]] | **Tags:** `#whois` `#dns` `#footprinting` `#reconnaissance`
> Back to [[Reconnaissance - Overview]]

---

## Overview

**WHOIS & DNS Footprinting** is an ethical hacker's practice of **information gathering** using domain registration records and DNS data.

**Two types:** Active & Passive
*(Define + Example for each — see [[Footprinting - Active and Passive]])*

---

## Passive WHOIS & DNS Tools
- **WHOIS** — domain registration lookup
- **NeoTrace** — visual traceroute/network mapping

## Active WHOIS & DNS Tools
- **ICMPsweep** — host discovery using ICMP

---

## WHOIS Lookup

WHOIS is a protocol for querying databases that store information about internet resources (domains, IP addresses).

**Data revealed:**
| Field | Example |
|---|---|
| Organization name | "FastRoute Inc." |
| Domain registrar | GoDaddy |
| Domain registrar | Name, address |
| Contact emails | admin@target.com |
| Phone numbers | +1-555-0100 |
| Name servers | ns1.target.com |
| Registration & expiry dates | 2020-01-01 to 2025-01-01 |

**How attackers use it:**
- Identify **responsible IT teams** for targeted attacks
- Find **contact emails** for spear phishing
- Launch **domain-specific phishing attacks**

---

## DNS Footprinting

DNS (Domain Name System) records hold mapping information that attackers can analyze.

### Common DNS Record Types

| Record | Purpose | Attacker Interest |
|---|---|---|
| **A** | Maps domain → IPv4 | Find IP addresses |
| **AAAA** | Maps domain → IPv6 | IPv6 infrastructure |
| **MX** | Mail server | Email infrastructure |
| **NS** | Name servers | DNS infrastructure |
| **TXT** | Text records | SPF, DKIM, misc info |
| **CNAME** | Alias records | Subdomain discovery |
| **PTR** | Reverse DNS | IP → domain mapping |
| **SOA** | Start of Authority | Zone info, admin contact |

---

## DNS Intelligence Techniques

### 1. Zone Transfers
Attempting to download the **entire DNS zone** from a misconfigured DNS server.
- Reveals **all subdomains** at once
- Most properly configured DNS servers block this

### 2. DNS Cache Snooping
Reading cached DNS entries to determine what sites a system has visited.

### 3. Reverse DNS Lookup
Mapping **IP addresses back to domain names**.

### 4. DNS History
Viewing historical DNS records to find old infrastructure.

> *(Tools list — see [[DNS Enumeration]])*

---

## Google Dorking for Domain Recon

*(Links to [[Google Dorking]])*

---

## DNS & Network Footprinting Chain

```
WHOIS Lookup
    → Organization name, registrar, contacts, name servers
    
DNS Record Analysis
    → Subdomain discovery, mail servers, IP ranges
    
Network Footprinting (ASN/BGP)
    → Public IP mapping, cloud provider metadata
    → Map network boundaries
    → Identify exposed services

Employee Email Enumeration
    → Via breach databases
    → Social profiles → phishing + org mapping
```

---

## Attacker Use Case

> The attackers combine all this data to create a **precise attack surface map**, reducing noise and increasing success rates.

---

## Related Topics
- [[Footprinting - Active and Passive]]
- [[Reconnaissance - Overview]]
- [[DNS Enumeration]]
- [[IP Address Reconnaissance]]
- [[OSINT Techniques]]

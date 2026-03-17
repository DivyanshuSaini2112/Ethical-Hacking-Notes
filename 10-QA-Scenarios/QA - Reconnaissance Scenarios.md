# ❓ QA - Reconnaissance Scenarios

> **MOC:** [[MOC - Questions and Answers]] | **Tags:** `#qa` `#reconnaissance` `#footprinting`

---

## Q1 - Organization with Updated Firewall & IDS

**Scenario:**
> An organization has experienced repeated cyber intrusion despite having updated firewalls and IDS. As an Ethical Hacker, you are assigned to perform recon phase to identify how attackers may be gathering critical information about the target. Analyze the possible active & passive recon techniques an attacker can use to collect system, network & employee-related information. Further examine how weaknesses in publicly available data & network exposure contribute to successful attacks.

### Answer 1: Reconnaissance Remains Effective Despite Firewalls

Reconnaissance remains effective because:
- **Firewalls only block traffic, not intelligence**
- **IDS detects signatures, not intent behind them**
- Reconnaissance relies heavily on **legitimate and public data**

### Passive Recon Techniques Used

| # | Technique | What It Reveals |
|---|---|---|
| 1 | **OSINT** — Company website, GitHub, LinkedIn, job posts | Technologies used, employee names |
| 2 | **Search Engine Intelligence** — Google Dorking | Exposed files, login pages |
| 3 | **DNS & Certificate Transparency** — subdomain discovery, SSL metadata | Infrastructure |
| 4 | **Data Breaches & Leaks** — email/password reuse | Credentials |

### Active Recon Techniques Used

| # | Technique | What It Reveals |
|---|---|---|
| 1 | **Network Scanning** — open ports | Services running |
| 2 | **Service Enumeration** — version detection | Old, vulnerable versions |
| 3 | **Web Application Enumeration** — directory brute force | Hidden pages |
| 4 | **Email & User Enumeration** — SMTP VRFY, password reset abuse | Valid email addresses |

### Public Data Weaknesses

**Public data weaknesses include:**
- Overly informative **job postings** revealing tech stack
- Poor **data sanitization** in public documents
- Excessive **employee social media presence**

**Network exposure weaknesses:**
- **Unused open ports** exposed to internet
- **Legacy systems** exposed to internet
- **Misconfigured cloud security groups**

> The attackers persist despite the defenses because **firewalls only block traffic not intelligence** and **IDS detects signatures not intent** behind them, and reconnaissance relies heavily on **legitimate and public data**.

---

## Q2 - How Publicly Exposed Information Increases Risks

**Question:** Discover how publicly exposed information increases risk of targeted cyber attacks.

### Answer:
Publicly exposed information increases risk by:

1. **Enables precision attacks** — attackers target specific individuals/systems
2. **Reduces guesswork** — exact technology stack known
3. **Better Social Engineering** — employee names, roles, personal info available
4. **Facilitates supply chain attacks & lateral attacks**
5. **Supports credential-based attacks** — using leaked credentials

---

## Q3 - FastRoute.inc Google Dork Scenario

**Scenario:**
> A mid-sized logistics company FastRoute.inc recently migrated its internal document management & employee portal to a new cloud-based WordPress setup. Within a week, sensitive internal documents including employees' salary spreadsheets & network configuration files appeared in public Google search results. A security researcher found that attackers were using specialized Google search queries to locate these files. You are a security analyst tasked with conducting an investigation to understand how this leak occurred and to propose remediation steps.

### A) Construct Three Specific Google Dork Queries

As an attacker, three dork queries to find exposed documents/admin panel on FastRoute.inc:

**1. Exposed Internal Documents (PDFs):**
```
site:fastroute.inc filetype:pdf "internal" OR "confidential"
```

**2. Exposed Spreadsheets:**
```
site:fastroute.inc filetype:xls OR filetype:xlsx "salary" OR "network"
```

**3. Exposed WordPress Admin or Login Panel:**
```
site:fastroute.inc inurl:wp-admin OR inurl:wp-login.php
```

### B) Potential Risks to FastRoute.inc & Immediate Remediation Steps

**Risks:**
1. **Confidentiality breach** — sensitive employee/financial data exposed
2. **Increased attack surface** — login panels accessible
3. **Privilege escalation risk** — admin credentials could be targeted
4. **Legal and compliance** violations
5. **Reputational damage** — trust lost with employees and clients

**Remediation:**
1. **Updating robots.txt** to prevent indexing of sensitive paths
2. **Applying "noindex" headers** to sensitive pages
3. **Access control hardening** on document directories
4. **Enforcing strong authentication** to secure WordPress configuration
5. **Removing exposed files** & requesting de-indexing from Google
6. **Auditing public search exposure** & sanitizing data before cloud upload

---

## Related Topics
- [[Reconnaissance - Overview]]
- [[Footprinting - Active and Passive]]
- [[Google Dorking]]
- [[WHOIS and DNS Footprinting]]
- [[OSINT Techniques]]

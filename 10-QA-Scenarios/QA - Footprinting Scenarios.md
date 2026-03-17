# ❓ QA - Footprinting Scenarios

> **MOC:** [[MOC - Questions and Answers]] | **Tags:** `#qa` `#footprinting` `#reconnaissance`

---

## Q1 - IT Service Organization Web Portal

**Scenario:**
> A mid-size IT service organization plans to launch a new customer web portal & hires an ethical hacker to assess its security posture before deployment. During preliminary assessment, ethical hacker discovers large amount of organizational information is publicly accessible including domain name, registration details, employee email formats, server technologies, & network ranges. Analyze how footprinting techniques can be used by attackers to create a complete & attack profile of an organization.

### Answer:

**Footprinting is the first phase of an attack** where adversaries collect information about a target without direct exploitation.

**Domain & Registration Footprinting:**
- WHOIS records reveal:
  - Organization name
  - Domain registrar
  - Contact emails & phone numbers
- This helps attackers identify **responsible teams** (IT, admin) & launch **domain-specific phishing attacks**

**DNS Footprinting via DNS record analysis enables:**
- Subdomain discovery → subdomain takeovers
- **Network Footprinting via Public IP Mapping**
  - ASN records
  - Cloud provider metadata → helps attacker map network boundaries & identify exposed services

**Employee Email Enumeration:**
- Via breach databases
- Social profiles enable **phishing** & **organizational structure mapping**

**The attackers combine all of this data to create a precise attack surface map, increasingly noise & success rates.**

---

## Q2 - How Publicly Exposed Data Increases Risk

**Question:** Discuss how publicly exposed information increases risk of targeted cyber attacks.

### Answer:

Publicly exposed information increases risk by:

1. **Enables precision attacks** — exact employee roles, technologies known
2. **Reduces guesswork** — no need for broad scanning; targeted approach
3. **Better Social Engineering** — enables convincing phishing with real names/roles
4. **Facilitates supply chain attacks & lateral attacks** — knowing vendor relationships
5. **Supports credential-based attacks** — leaked email/password combos reused

---

## Q3 - Mid-Size Organization Network Scenario

**Scenario:**
> A mid-size organization reports a discrepancy between authorized remote user access management solution between currently discovered automated store data. A security team including information has between automated store & possible misconfigurations to facilitate problems & one of their team members connected to the internal network who is found to be in the local network.

**Part A) List the appropriate commands for the recursive position following:**

1. **Enumerate open ports & exposed services** on the system:
```bash
nmap -sV <IP>
```

2. **Identify** any active network connections, the port position:
```bash
nmap -p- --open <IP>
```

3. **Collect** basic test systems & possible legal account collection services:
```bash
nmap -p- <IP> -sV
```

4. **Scan** systems for period mediate account position process:
```bash
nmap -sS -p 22,80,443 <IP>
```

5. **Locally affect account & corporate** legal process through metadata:
```bash
nmap -A <IP>
```

**Part B):**
1. `nmap -sS`
   - SYN stealth scan, checking — all open ports, host identity
   - For authorized connection → let known
   - Will serve as network positions

2. Detect:
   - DNS resolve, check for host
   - Look up known service identity
   - Host running enumeration

3. Use SNMP version 3 for authentication
   - `all accessible SNMP data`
   - Use SNMP via to go through specific OID enumeration → counters (authenticated)

4. `nmap -sS -p -h -localhost (-open + 22/default)`
   - To enumerate all connected windows account data to max

5. Full output `<target>`:
   - `nmap -v cess 2.69.69` (by user account/domain)

**Use SNMP via to go → fingerprinting:**
- To assist in vulnerability assessment

---

## Related Topics
- [[Footprinting - Active and Passive]]
- [[Reconnaissance - Overview]]
- [[WHOIS and DNS Footprinting]]
- [[OSINT Techniques]]
- [[QA - Reconnaissance Scenarios]]

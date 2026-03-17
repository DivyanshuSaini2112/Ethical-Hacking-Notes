# 🔨 Types of Ethical Hacking

> **MOC:** [[MOC - Ethical Hacking Fundamentals]] | **Tags:** `#ethical-hacking` `#types`
> Back to [[Ethical Hacking - Definition and Overview]]

---

## 5 Main Types

### 1. 🌐 Hacking the Network

Testing **network infrastructure** for vulnerabilities.

**What is tested:**
- Routers, switches, firewalls
- Network protocols (TCP/IP, UDP, ICMP)
- VPNs, wireless configurations

**Common techniques:**
- [[Scanning in Ethical Hacking]] — port scanning
- [[SNMP Enumeration]] — network device info
- Packet sniffing (snooping vs spoofing)

**Examples:**
- Intercepting unencrypted traffic on a corporate Wi-Fi
- Testing if firewall rules block unauthorized ports

---

### 2. 🌍 Hacking Web Applications

Testing **web applications** for security flaws.

**What is tested:**
- Websites, web portals, APIs
- Authentication mechanisms
- Input validation (SQL injection, XSS)

**Common techniques:**
- [[Web Application Scan (WAScan)]]
- Directory enumeration
- Fuzzing with tools like BeautifulSoup

**Examples:**
- Finding SQL injection in a login form
- Testing for broken authentication on an e-commerce site

---

### 3. 💻 Hacking Systems

Testing **operating systems** and **software** for vulnerabilities.

**What is tested:**
- OS-level vulnerabilities
- Service configurations
- Patch management

**Common techniques:**
- [[Privilege Escalation]]
- Exploiting unpatched services
- Credential attacks

**Examples:**
- Exploiting an outdated Windows service to gain admin
- Testing Linux sudo misconfigurations

---

### 4. 🧠 Social Engineering

Exploiting **human psychology** rather than technical vulnerabilities.

**What is tested:**
- Employee awareness
- Physical security
- Phishing susceptibility

**Techniques:**
- [[In-Person Reconnaissance Techniques]] — tailgating, shoulder surfing
- Phishing emails
- Vishing (voice phishing)

**Examples:**
- Sending fake IT support emails to capture credentials
- Tailgating through a secure door

---

### 5. 📶 Hacking into Wireless Networks

Testing **Wi-Fi and wireless** security.

**What is tested:**
- WPA/WPA2 encryption strength
- Rogue access points
- Bluetooth security

**Common techniques:**
- [[Physical Device Reconnaissance]]
- RF analysis
- Deauthentication attacks

**Examples:**
- Cracking a WPA2 password using a wordlist attack
- Setting up an evil twin access point

---

## Summary Table

| Type | Target | Key Tools | Example Attack |
|---|---|---|---|
| Network | Routers, switches, firewalls | [[Nmap - Complete Guide]], Wireshark | MITM, ARP spoofing |
| Web App | Websites, APIs | WAScan, Burp Suite | SQL injection, XSS |
| System | OS, software | Metasploit | Privilege escalation |
| Social Engineering | People | Phishing kits | Credential theft |
| Wireless | Wi-Fi, Bluetooth | Aircrack-ng | WPA2 cracking |

---

## Related Topics
- [[Types of Ethical Hackers]]
- [[Phases of Ethical Hacking]]
- [[Skills Required for Ethical Hacking]]
- [[Attack Vectors and Vulnerabilities]]

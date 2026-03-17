# ⬆️ Privilege Escalation

> **MOC:** [[MOC - Ethical Hacking Fundamentals]] | **Tags:** `#privilege-escalation` `#exploitation`

---

## Definition

**Privilege Escalation** is the process of exploiting a **vulnerability, design flaw, or configuration error** in an OS or application to gain **elevated access** to resources that are normally restricted.

---

## Two Types of Privilege Escalation

### 1. ↔️ Horizontal Privilege Escalation

- Gaining access to **another user's account** at the **same privilege level**
- No increase in permissions — just lateral movement
- Example: Logging in as "User B" while being "User A" — both are regular users

### 2. ⬆️ Vertical Privilege Escalation

- Gaining access to **higher privileges** (escalating UP)
- Going from normal user → admin/root
- Example: A regular employee account exploiting a vulnerability to become an Administrator

---

## Common Vulnerable Service Example (From Notes)

**Scenario:**
> An organization uses a Windows Server 2019 system. A local user account is compromised through phishing. The attacker discovers an **outdated third-party service running with SYSTEM privileges**. By exploiting this service, the attacker gains **full administrative control** over the system.

**Questions to answer:**

### a) Is this Horizontal or Vertical Privilege Escalation? Justify.

→ **Vertical Privilege Escalation**

**Justification:**
- The attacker started with a **low-privilege local user account** (compromised via phishing)
- By exploiting the vulnerable service, gained **SYSTEM-level privileges** (full administrative control)
- This is a clear **upward movement** in privilege level
- Horizontal escalation would mean moving to another user at the same level, NOT gaining admin

### b) Role of Vulnerable Service in Privilege Escalation Attacks

Vulnerable services are a primary attack vector for privilege escalation:

| Role | Explanation |
|---|---|
| **Service runs as SYSTEM** | Services with high privileges are high-value targets |
| **Unpatched/outdated** | Known CVEs can be exploited to inject code |
| **Misconfigured permissions** | Writable service binaries = attacker can replace executable |
| **DLL Hijacking** | Service loads DLLs from unprotected paths |
| **Weak registry permissions** | Attacker can modify service configuration |

### c) What Security Misconfigurations Enable This Attack?

1. **Running services as SYSTEM/Administrator** instead of least-privilege accounts
2. **Unpatched third-party software** — not applying security updates
3. **Writable service directories** — attackers can replace binaries
4. **Weak file/folder permissions** — users can modify system files
5. **No application whitelisting** — any executable can run
6. **Missing integrity checks** — no verification of binary authenticity

### d) List 3 Mitigation Techniques

1. **Principle of Least Privilege (PoLP)** — services should run with minimum required permissions
2. **Regular patch management** — keep all software updated
3. **File integrity monitoring** — detect unauthorized changes to system files

---

## Penetration Tester Privilege Escalation Scenario

**Scenario:**
> A penetration tester gains access to a system as a normal user. Due to **improper service permissions** and **lack of patch management**, the tester escalates privilege to administrator level.

**ai) Explain the complete privilege escalation process, the vulnerability exploited & preventive measures:**

### The Process:
```
1. Initial Access: Gained normal user account access
2. Enumeration: Discovered running services & their permissions
3. Vulnerability Found: Improper service permissions (writable binary path)
4. Exploitation: Replaced service binary with malicious executable
5. Privilege Gained: Service runs as Administrator → attacker gets admin shell
```

### Vulnerability Exploited:
- **Improper service permissions** — users can write to service binary location
- **Lack of patch management** — known CVE exploited in unpatched service

### Preventive Measures for System Administrators:
1. **Apply Principle of Least Privilege** — services use minimum required permissions
2. **Implement patch management policy** — regular, timely updates
3. **Audit service permissions** — use `icacls` (Windows) or `ls -la` (Linux)
4. **Use application whitelisting** — only approved executables run
5. **Enable Windows Defender Credential Guard**
6. **Monitor for privilege escalation** with SIEM tools
7. **Disable/remove unnecessary services**
8. **Use security auditing tools** — regularly scan for misconfigured services

---

## Linux Privilege Escalation Methods

| Method | Description |
|---|---|
| **SUID/SGID abuse** | Files with setuid bit run as owner (root) |
| **Sudo misconfiguration** | `sudo -l` shows exploitable sudo rules |
| **Cron job exploitation** | Writable scripts run as root via cron |
| **Weak file permissions** | `/etc/passwd` writable → add root user |
| **Kernel exploits** | Dirty COW, Dirty Pipe — kernel vulnerabilities |
| **Path hijacking** | Replace binary in PATH with malicious one |

---

## Windows Privilege Escalation Methods

| Method | Description |
|---|---|
| **Unquoted service paths** | Services with unquoted paths with spaces |
| **DLL hijacking** | Planting malicious DLL in search path |
| **Token impersonation** | Abusing SeImpersonatePrivilege |
| **UAC bypass** | Circumventing User Account Control |
| **Registry auto-run keys** | Persistence + elevated execution |
| **Scheduled tasks** | Writable task with high privilege |

---

## Tools for Privilege Escalation

| Tool | OS | Purpose |
|---|---|---|
| LinPEAS | Linux | Automated Linux priv esc enumeration |
| WinPEAS | Windows | Automated Windows priv esc enumeration |
| BeRoot | Both | Check for common priv esc paths |
| PowerUp | Windows | PowerShell priv esc checks |
| GTFOBins | Linux | Find exploitable binaries |
| LOLBAS | Windows | Living off the land binaries |

---

## Related Topics
- [[Phases of Ethical Hacking]]
- [[Attack Vectors and Vulnerabilities]]
- [[Vulnerability Scanning]]
- [[Types of Ethical Hacking]]
- [[QA - Privilege Escalation Scenarios]]

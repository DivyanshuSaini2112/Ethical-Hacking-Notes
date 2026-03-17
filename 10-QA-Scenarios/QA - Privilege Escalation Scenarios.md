# ❓ QA - Privilege Escalation Scenarios

> **MOC:** [[MOC - Questions and Answers]] | **Tags:** `#qa` `#privilege-escalation`

---

## Q1 - Windows Server 2019 Scenario

**Scenario:**
> An organization uses a Windows Server 2019 system. A local user account is compromised through phishing. The attacker discovers an outdated third-party service running with system privileges. By exploiting this service, the attacker gains full administrative control over the system.

### a) Is this Horizontal or Vertical Privilege Escalation? Justify.

**Answer: VERTICAL Privilege Escalation**

**Justification:**
- The attacker started as a **low-privilege local user** (compromised via phishing)
- After exploitation, gained **SYSTEM-level / full administrative control**
- Movement was **upward** in privilege level (user → admin)
- Horizontal escalation = same privilege level, different account
- Vertical escalation = HIGHER privilege level ✅

---

### b) Explain the role of vulnerable service in privilege escalation attacks

**Key roles:**

1. **Service runs with elevated privileges (SYSTEM)** — high-value exploitation target
2. **Unpatched/outdated service** — known CVEs exploitable to inject code
3. **Misconfigured service permissions** — writable binary allows attacker to replace executable
4. **DLL hijacking vulnerability** — service loads DLLs from unprotected paths
5. **Weak registry permissions** — attacker can modify service startup configuration

> The service becomes a **privilege bridge** — attacker uses it to jump from low-privilege to SYSTEM level.

---

### c) What security misconfigurations enabled this attack?

1. **Service configured to run as SYSTEM** instead of a least-privilege service account
2. **Unpatched third-party software** — outdated software with known CVEs not updated
3. **Writable service directory** — low-privilege users can write to service binary path
4. **No application whitelisting** — any executable can be swapped in
5. **Missing integrity checking** — no hash verification of service binaries
6. **Excessive local user privileges** — phished account had unnecessary permissions

---

### d) List any 3 mitigation techniques for these attacks

1. **Principle of Least Privilege (PoLP)** — run services with minimum required permissions, not SYSTEM
2. **Regular patch management** — keep ALL software (especially third-party) updated promptly
3. **File integrity monitoring** — detect unauthorized changes to service binaries and configs

---

## Q2 - Penetration Tester Privilege Escalation

**Scenario:**
> A penetration tester gains access to a system as a normal user. Due to improper service permissions and lack of patch management, the tester escalates privilege to administrator level.

### ai) Explain complete privilege escalation process, vulnerability exploited & preventive measures

**The Complete Process:**

```
Step 1: Initial Access
→ Penetration tester gains normal user access (via phishing/credential reuse/etc.)

Step 2: Enumeration
→ Run privilege escalation scripts (WinPEAS/LinPEAS)
→ Identify running services and their permission levels
→ Find services with writable binary paths or weak registry permissions

Step 3: Vulnerability Identified
→ Improper service permissions: local user can write to service binary directory
→ Lack of patch management: service has known unpatched CVE

Step 4: Exploitation
→ Replace legitimate service binary with malicious executable (or DLL)
→ OR modify service registry entry to point to attacker's payload

Step 5: Trigger Escalation
→ Restart service (or wait for restart/reboot)
→ Service executes with Administrator/SYSTEM privileges
→ Malicious binary creates elevated shell for attacker

Step 6: Administrator Access Achieved
→ Full system control obtained
→ Tester documents findings and reports
```

**Vulnerability Exploited:**
- **Improper service permissions** — `SERVICE_ALL_ACCESS` granted to low-privilege users
- **Lack of patch management** — known CVE in unpatched service version

**Preventive Measures for System Administrators:**

| # | Measure | Implementation |
|---|---|---|
| 1 | **Apply Principle of Least Privilege** | Services run as dedicated low-privilege accounts |
| 2 | **Implement patch management policy** | Automated, timely updates for all software |
| 3 | **Audit service permissions** | Use `icacls` (Windows) / `ls -la` (Linux) regularly |
| 4 | **Use application whitelisting** | Only approved, hash-verified executables run |
| 5 | **Enable Windows Defender Credential Guard** | Protect privileged credentials |
| 6 | **Monitor with SIEM** | Alert on privilege escalation indicators |
| 7 | **Disable unnecessary services** | Reduce attack surface |
| 8 | **Security auditing tools** | Regular scanning for misconfigured services |

---

## Privilege Escalation Quick Reference

| Type | Direction | Example |
|---|---|---|
| **Vertical** | Low → High | User → Admin → SYSTEM |
| **Horizontal** | Same → Same | User A → User B (same level) |

---

## Related Topics
- [[Privilege Escalation]]
- [[Attack Vectors and Vulnerabilities]]
- [[Phases of Ethical Hacking]]
- [[Types of Ethical Hacking]]

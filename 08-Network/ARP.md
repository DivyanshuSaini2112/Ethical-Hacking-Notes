# ARP (Address Resolution Protocol)

ARP is a protocol that enables network communication to reach a specific device/address on the network. ARP translates IP to MAC addresses and vice versa. Devices use ARP to connect to a router or gateway that enables them to connect with the internet.

ARP was not designed for security, so it does not verify that the response to an ARP request really comes from an authorized party. It also lets hosts accept ARP responses even if they never sent out a request. This is a weak point in the ARP protocol which opens the door to ARP spoofing attacks.

---

## ARP Spoofing

ARP spoofing is a dangerous local area network attack where a malicious actor sends forged ARP messages to associate their MAC address with a legitimate IP address — usually the default gateway. This allows the attacker to intercept, modify, or stop traffic, enabling **Man-in-the-Middle (MITM)** attacks, data theft, and session hijacking.

---

## How ARP Spoofing Works

1. **Connect** — The attacker must be connected to the target local network.
2. **Scan** — The attacker scans the network to determine the IP addresses of at least 2 devices (e.g., a workstation and a router).
3. **Forge ARP Responses** — Using tools like `arpspoof` or `bettercap`, the attacker sends out forged ARP responses advertising that the correct MAC address for both the router and workstation IPs is the attacker's MAC address. This fools both devices into connecting to the attacker's machine.
4. **Update Cache** — The victim devices update their ARP tables with the fake entries, directing traffic to the attacker.
5. **Interception** — The attacker sits in the middle of the communication, allowing them to eavesdrop on or alter data.

### Once ARP Spoofing Succeeds, the Attacker Can:

- Continue the communication as-is (passive eavesdropping)
- Perform **session hijacking**
- **Alter communication** (data manipulation)
- Launch a **DDoS attack**

> **Detection Tip:** Run `arp -a` to get a list of IP addresses with their associated MAC addresses. If two IP addresses share the same MAC address, ARP spoofing may be occurring.

---

## Tools for ARP Poisoning

| Tool | Description |
|------|-------------|
| **Wireshark** | Network packet analyzer |
| **arpspoof** | CLI tool for ARP spoofing |
| **Bettercap** | Advanced MITM framework |
| **Ettercap** | Network sniffer and MITM tool |

---

## Prevention Mechanisms

- **Use a VPN** — Encrypts traffic, making interception ineffective
- **Monitor the Network** — Log ARP entries and watch for anomalies
- **Packet Filtering** — Filter suspicious ARP packets at the switch/firewall level
- **Security Awareness** — Educate users and staff about network threats
- **Patch Management** — Keep systems and network devices up to date

---

## Practice Questions

### Question 1
> Assume an attacker is connected to the same LAN as 2 victims. Demonstrate how ARP poisoning can be performed to place the attacker in a MITM position between the 2 hosts.

### Question 2
> Evaluate different backdoor detection and prevention techniques such as IDS, log analysis, and firewall monitoring in protecting enterprise networks. Mention their advantages, disadvantages, and effectiveness.



## Related Topics

[[SNMP Enumeration]]
[[Nmap - Complete Guide]]
[[Scanning in Ethical Hacking]]
[[Reconnaissance - Overview]]
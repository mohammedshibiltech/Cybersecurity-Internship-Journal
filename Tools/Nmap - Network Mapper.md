# Nmap - Network Mapper

## Overview

Nmap (Network Mapper) is an open-source network discovery and security auditing tool widely used by system administrators, network engineers, and penetration testers. It enables the identification of live hosts, open ports, running services, operating systems, and potential vulnerabilities within a network.

Nmap is one of the most essential tools in Vulnerability Assessment and Penetration Testing (VAPT) because it provides a detailed understanding of a target's attack surface before any exploitation attempts.

---

# TCP/IP Fundamentals

Understanding TCP/IP communication is essential before performing scans.

## TCP Three-Way Handshake

A normal TCP connection is established through three steps:

1. **SYN** – Client requests a connection.
2. **SYN-ACK** – Server acknowledges the request.
3. **ACK** – Client confirms the connection.

```text
Client                Server
  | ---- SYN -------> |
  | <--- SYN-ACK ---- |
  | ---- ACK -------> |
```

Many Nmap scan types manipulate or interrupt this process to gather information without fully establishing a connection.

---

# Host Discovery

Before scanning ports, Nmap can identify active hosts on a network.

## Ping Sweep

```bash
nmap -sn 192.168.1.0/24
```

### Purpose

- Discover live hosts
- Skip port scanning
- Useful during reconnaissance

### Techniques Used

- ICMP Echo Requests
- TCP SYN Ping
- TCP ACK Ping
- ARP Requests (Local Networks)

---

# Port Scanning

Ports are logical communication endpoints used by applications and services.

## Common Ports

| Port | Service |
|------|---------|
| 21 | FTP |
| 22 | SSH |
| 23 | Telnet |
| 25 | SMTP |
| 53 | DNS |
| 80 | HTTP |
| 110 | POP3 |
| 139 | NetBIOS |
| 143 | IMAP |
| 443 | HTTPS |
| 445 | SMB |
| 3389 | RDP |

---

# TCP SYN Scan

## Command

```bash
nmap -sS <target>
```

## Description

Also known as a **Half-Open Scan**.

Instead of completing the full TCP handshake, Nmap sends a SYN packet and analyzes the response.

### Responses

| Response | Meaning |
|----------|---------|
| SYN-ACK | Port Open |
| RST | Port Closed |
| No Response | Filtered |

### Advantages

- Fast
- Stealthier than full connections
- Less likely to be logged

### Disadvantages

- Requires elevated privileges/root access

---

# TCP Connect Scan

## Command

```bash
nmap -sT <target>
```

## Description

Performs a complete TCP handshake.

### Advantages

- Does not require raw packet privileges
- Reliable

### Disadvantages

- Easily detected
- Generates logs on target systems

---

# FIN Scan

## Command

```bash
nmap -sF <target>
```

## Concept

Sends packets with only the FIN flag set.

### Expected Behavior

- Closed Port → RST Response
- Open Port → No Response

### Usage

Useful against certain packet-filtering firewalls.

---

# NULL Scan

## Command

```bash
nmap -sN <target>
```

## Concept

Sends TCP packets without any flags.

### Usage

Can sometimes bypass basic firewall configurations.

---

# XMAS Scan

## Command

```bash
nmap -sX <target>
```

## Concept

Sends packets with FIN, PSH, and URG flags enabled.

Named "XMAS" because all flags are lit up like a Christmas tree.

### Usage

Used for stealth reconnaissance and firewall testing.

---

# UDP Scan

## Command

```bash
nmap -sU <target>
```

## Purpose

Identifies services running on UDP ports.

### Common UDP Services

| Port | Service |
|------|---------|
| 53 | DNS |
| 67/68 | DHCP |
| 69 | TFTP |
| 123 | NTP |
| 161 | SNMP |

### Challenges

- Slower than TCP scans
- UDP services often do not respond
- High chance of packet filtering

---

# Service Version Detection

## Command

```bash
nmap -sV <target>
```

## Purpose

Determines:

- Running services
- Software versions
- Service banners

### Example

```text
22/tcp open ssh OpenSSH 9.0
80/tcp open http Apache 2.4.57
```

### Benefits

- Helps identify outdated software
- Useful for vulnerability research

---

# Operating System Detection

## Command

```bash
nmap -O <target>
```

## Purpose

Uses TCP/IP fingerprinting techniques to determine:

- Operating System
- Device Type
- Network Characteristics

### Example Results

- Linux
- Windows Server
- FreeBSD
- Network Appliances

---

# Aggressive Scan

## Command

```bash
nmap -A <target>
```

## Includes

- OS Detection
- Service Version Detection
- Script Scanning
- Traceroute

### Use Case

Fast information gathering during assessments.

### Caution

Produces significant network traffic and is easier to detect.

---

# Nmap Scripting Engine (NSE)

## Overview

The Nmap Scripting Engine extends Nmap through Lua-based scripts.

NSE can automate:

- Enumeration
- Vulnerability Detection
- Service Discovery
- Authentication Checks

---

## Script Categories

### Discovery

Identifies additional information about targets.

### Safe

Low-risk information gathering.

### Vulnerability

Checks for known vulnerabilities.

### Authentication

Tests login and authentication mechanisms.

### Exploit

Performs limited exploitation activities.

### Malware

Searches for malware indicators.

### Broadcast

Discovers hosts using broadcast traffic.

---

# Useful NSE Commands

## Run Vulnerability Scripts

```bash
nmap --script vuln <target>
```

## Run Safe Scripts

```bash
nmap --script safe <target>
```

## SMB User Enumeration

```bash
nmap --script smb-enum-users <target>
```

## List Available Scripts

```bash
ls /usr/share/nmap/scripts/
```

---

# Firewall Evasion Techniques

Firewalls and IDS/IPS systems may block or detect scans.

Nmap provides several options to improve stealth.

## Fragment Packets

```bash
nmap -f <target>
```

### Purpose

Splits packets into smaller fragments.

### Benefit

May bypass poorly configured packet filters.

---

## Skip Host Discovery

```bash
nmap -Pn <target>
```

### Purpose

Treat target as alive even if ping responses are blocked.

### Useful When

- ICMP is disabled
- Firewall blocks ping requests

---

## Timing Templates

```bash
nmap -T0 <target>
nmap -T1 <target>
nmap -T2 <target>
nmap -T3 <target>
nmap -T4 <target>
nmap -T5 <target>
```

### Timing Levels

| Template | Description |
|----------|-------------|
| T0 | Paranoid |
| T1 | Sneaky |
| T2 | Polite |
| T3 | Normal |
| T4 | Aggressive |
| T5 | Insane |

### Usage

- Lower values = Stealthier
- Higher values = Faster

---

# Output Formats

## Normal Output

```bash
nmap -oN scan.txt <target>
```

## XML Output

```bash
nmap -oX scan.xml <target>
```

Useful for importing results into security tools.

## Grepable Output

```bash
nmap -oG scan.grep <target>
```

Useful for scripting and automation.

---

# Common Practical Commands

## Scan Entire Subnet

```bash
nmap 192.168.1.0/24
```

## Scan Specific Ports

```bash
nmap -p 22,80,443 <target>
```

## Scan Port Range

```bash
nmap -p 1-1000 <target>
```

## Scan Top 100 Ports

```bash
nmap --top-ports 100 <target>
```

## Full TCP Port Scan

```bash
nmap -p- <target>
```

## Service and Version Detection

```bash
nmap -sV -sC <target>
```

---

# Key Takeaways

- Nmap is a foundational tool in VAPT and network reconnaissance.
- Different scan types offer varying levels of speed, stealth, and reliability.
- NSE significantly expands Nmap's capabilities beyond simple port scanning.
- Understanding TCP/IP behavior improves interpretation of scan results.
- Firewall evasion techniques can help assess filtered environments.
- Proper enumeration is often the most important step before vulnerability exploitation.

---

# References

Official Documentation:
https://nmap.org/book/

Official Website:
https://nmap.org
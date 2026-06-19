# TryHackMe - Blue Room Write-up

## Room Overview

The Blue room introduces vulnerability discovery and exploitation of a Windows machine vulnerable to SMB-related attacks. The primary objective is to identify the vulnerability affecting the target and prepare an exploitation path using publicly available security tools.

---

# Initial Enumeration

## Connectivity Testing

The first step was verifying whether the target machine was reachable.

An ICMP echo request was sent to the target; however, no response was received.

### Observation

The lack of ICMP responses initially suggested either:

- The host was offline
- ICMP traffic was being filtered by a firewall

Because modern firewalls frequently block ICMP traffic, the second possibility was investigated further.

---

# Nmap Host Discovery

A standard Nmap scan was attempted.

Nmap reported the host as down.

### Why?

By default, Nmap performs host discovery before beginning port scanning. If host discovery fails, Nmap assumes the host is offline.

To bypass this behavior, host discovery was disabled using the `-Pn` option.

### Key Learning

```text
-Pn = Treat host as online and skip host discovery.
```

---

# Port Scanning Results

After disabling host discovery, the target was successfully scanned.

## Open Ports

| Port | Service |
|--------|----------|
| 135 | MSRPC |
| 139 | NetBIOS |
| 445 | SMB |
| 3389 | RDP |
| 49152 | Dynamic RPC |
| 49153 | Dynamic RPC |
| 49154 | Dynamic RPC |
| 49160 | Dynamic RPC |
| 49165 | Dynamic RPC |

---

## Analysis

The combination of:

- Port 139
- Port 445

immediately indicated that SMB services were available.

Historically, SMB has been responsible for several critical Windows vulnerabilities, making it a high-priority target for further investigation.

---

# Host Discovery Investigation

To better understand Nmap's behavior, host discovery mode (`-sn`) was examined.

## Purpose of -sn

Host discovery only.

No port scanning is performed.

### Common Uses

- Network mapping
- Identifying active hosts
- Fast reconnaissance
- Large subnet discovery

---

## Discovery Methods Used by Nmap

| Scenario | Discovery Method |
|-----------|------------------|
| Local network as root | ARP |
| Remote network as root | ICMP |
| Non-root user | TCP |

---

## ARP Investigation

A question arose:

> If ICMP is blocked, can ARP be used instead?

The answer depended on network topology.

Because the target machine was accessed through a VPN and existed on a remote network, ARP could not be used.

### Reason

ARP packets cannot traverse routers.

Nmap therefore reverted to ICMP-based host discovery methods.

This explained why privileged scans still failed to discover the host.

---

# Vulnerability Detection Using NSE

The next objective was answering the room question:

> What is this machine vulnerable to?

Nmap's Scripting Engine (NSE) was used.

## About NSE

NSE (Nmap Scripting Engine) is a Lua-based framework that extends Nmap's capabilities beyond simple port scanning.

Common uses include:

- Enumeration
- Service interrogation
- Authentication testing
- Vulnerability detection

---

# Vulnerability Discovery

The vulnerability scan identified:

## MS17-010

### Severity

High

### Type

Remote Code Execution

### Affected Component

SMBv1

### CVE

CVE-2017-0143

### Description

MS17-010 is a critical vulnerability in Microsoft's SMBv1 implementation that allows remote attackers to execute arbitrary code on vulnerable systems.

This vulnerability became widely known after its use in the WannaCry ransomware outbreak.

---

# Enhanced Vulnerability Scanning

TryHackMe suggested running:

```bash
nmap -sV -vv --script vuln TARGET_IP
```

To understand the recommendation, the scan options were analyzed.

---

## Service Version Detection (-sV)

This option performs active interrogation of services.

Nmap attempts to:

- Grab banners
- Send protocol-specific probes
- Identify software versions

This information helps determine whether a service version is vulnerable.

---

## Increased Verbosity (-vv)

Provides detailed real-time information about:

- Scan progress
- Probe activity
- Service detection process
- NSE execution

This makes troubleshooting and learning significantly easier.

---

# Additional Vulnerability Identified

The detailed NSE scan reported:

## CVE-2009-3103

### Vulnerability Name

SMBv2 Negotiation Vulnerability

### Microsoft Advisory

975497

### Impact

- Remote Code Execution
- Denial of Service

### Affected Systems

- Windows Vista
- Windows Server 2008
- Early Windows 7 Releases

### Technical Summary

The vulnerability exists within the SMBv2 implementation and can trigger an out-of-bounds memory access condition during protocol negotiation.

Because SMB operates with elevated privileges, successful exploitation can lead to full system compromise.

---

# Understanding CVE References

The vulnerability report contained:

```
CVE-2009-3103
```

### Breakdown

| Component | Meaning |
|------------|-----------|
| 2009 | Year discovered |
| 3103 | Unique vulnerability identifier |

CVE identifiers provide a universal reference system used across the cybersecurity industry.

---

# Exploit Research

After identifying vulnerabilities, the next step was determining whether public exploits existed.

Research methodology:

1. Review CVE references.
2. Study vendor advisories.
3. Search Exploit-DB.
4. Search Metasploit Framework.

---

# Metasploit Enumeration

Metasploit was launched and searched for relevant modules.

## CVE-2009-3103

Multiple modules were identified, including:

- Remote Code Execution modules
- Denial-of-Service modules

## MS17-010

Several EternalBlue-related modules were available, including:

- EternalBlue
- EternalRomance
- EternalSynergy
- DoublePulsar

---

# Selecting an Exploit Module

When reviewing Metasploit results, exploit modules are generally the primary focus.

### Exploit Modules

Purpose:

- Execute payloads
- Gain code execution
- Establish shells

### Auxiliary Modules

Purpose:

- Scanning
- Detection
- Validation
- Administration

---

# Evaluating Exploit Reliability

Metasploit provides a ranking system.

Examples:

- Excellent
- Great
- Good
- Normal
- Average

Higher-ranked modules are generally considered safer and more reliable.

---

# Module Configuration

The EternalBlue module was examined.

Important parameters included:

| Parameter | Purpose |
|------------|-----------|
| RHOSTS | Target IP |
| RPORT | Target Port |
| LHOST | Attacker IP |
| LPORT | Listener Port |

---

# Preparing for Exploitation

Metasploit requires mandatory parameters to be configured before execution.

Parameters can be assigned using:

```text
set <parameter> <value>
```

Examples include:

- Setting the target IP address
- Defining listener addresses
- Configuring payload options

At this stage, vulnerability validation, exploit research, and module preparation had been completed.

---

# Status

**Write-up Progress:** Completed up to exploit preparation phase.

Exploitation and post-exploitation activities have not yet been performed.
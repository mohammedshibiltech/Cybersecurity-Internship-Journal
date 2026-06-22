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


# Exploitation

## Initial Challenges

Although the target vulnerability had been successfully identified, exploitation was not immediately successful. Multiple attempts resulted in failed sessions and unstable target behavior.

During troubleshooting, research revealed an important practical consideration when working with vulnerable machines: repeated exploitation attempts can occasionally destabilize or crash the target system. To avoid false negatives caused by an unstable machine state, a fresh target instance was launched after several failed attempts.

This highlighted an important penetration testing lesson:

> Vulnerability identification does not guarantee successful exploitation. Proper payload configuration, network settings, and target stability all play a significant role.

---

## Troubleshooting Payload Configuration

Further investigation identified two critical payload settings that required modification.

### LHOST Configuration

The default configuration provided by the lab environment used a local system IP address. However, because the attack machine was connected through OpenVPN, the payload needed to communicate through the VPN tunnel interface.

Instead of using the default IP address, the VPN interface (`tun0`) was configured as the listener host.

### LPORT Configuration

The default listener port occasionally failed to establish reliable reverse connections.

Changing the listener port to a commonly allowed service port such as:

* 443 (HTTPS)
* 80 (HTTP)

improved connection reliability.

After adjusting these settings, the exploit successfully established a reverse shell.

---

# Establishing a Meterpreter Session

The initial exploitation resulted in a standard Windows command shell.

While functional, a Meterpreter session provides significantly more capabilities including:

* Process migration
* Credential dumping
* File management
* Privilege interaction
* Post-exploitation modules

To upgrade the shell, the existing session was first backgrounded and returned to the Metasploit console.

A post-exploitation module designed for shell upgrades was then identified and configured.

## Shell Upgrade Process

The `shell_to_meterpreter` module was selected.

The module required:

* LHOST
* LPORT
* SESSION ID

After configuring the required parameters and executing the module, a new Meterpreter session was successfully created.

Verification through the session manager confirmed both the original shell and the newly established Meterpreter session were active.

---

# System Enumeration

After interacting with the Meterpreter session, system processes were enumerated to understand the operating environment and identify high-privilege processes.

The process list revealed numerous services running under:

```text
NT AUTHORITY\SYSTEM
```

This confirmed that highly privileged processes were available for migration.

---

# Process Migration

Meterpreter allows migration into another running process.

Migrating into a stable SYSTEM-level process provides several advantages:

* Increased stability
* Higher privileges
* Access to protected system resources
* Ability to perform credential extraction

A suitable SYSTEM-owned process was selected and the Meterpreter session was migrated successfully.

---

# Credential Extraction

Following successful migration, credential dumping was performed.

The extracted Security Account Manager (SAM) hashes revealed three user accounts:

| User          | RID  |
| ------------- | ---- |
| Administrator | 500  |
| Guest         | 501  |
| Jon           | 1000 |

Among the discovered credentials, the NTLM hash belonging to the user **Jon** was selected for offline cracking.

---

# Understanding NTLM

NTLM (NT LAN Manager) is a Microsoft authentication protocol historically used within Windows environments.

Its purpose is to:

* Verify user identity
* Support authentication workflows
* Provide Single Sign-On (SSO) functionality

Although widely used in older systems, NTLM suffers from several security weaknesses including:

* Pass-the-Hash attacks
* NTLM Relay attacks
* Weak server authentication mechanisms

Modern Windows environments increasingly favor Kerberos and Windows Hello for authentication.

---

# Password Recovery

The extracted NTLM hash was submitted to an online password cracking service.

The password associated with the user account **Jon** was successfully recovered.

This demonstrated the risk of weak passwords even when credentials are stored as hashes.

---

# Flag Discovery

With SYSTEM-level access established, the final objective was locating the room flags.

## Flag 1

Enumeration of the root directory revealed the first flag stored directly within the system drive.

This reinforced the importance of performing thorough filesystem enumeration immediately after gaining access.

---

## Flag 2 and Flag 3

Instead of manually traversing every directory, Meterpreter's built-in search functionality was used to locate the remaining flags.

This approach significantly reduced enumeration time and demonstrated the value of leveraging framework capabilities during post-exploitation activities.

Both remaining flags were successfully located and retrieved.

---

# Key Takeaways

## Technical Skills Developed

* Troubleshooting failed exploit attempts
* Configuring reverse shell payloads
* Understanding VPN listener requirements
* Meterpreter session management
* Process migration
* Windows privilege context analysis
* Credential dumping
* NTLM hash analysis
* Password cracking workflow
* Post-exploitation file enumeration

## Security Concepts Reinforced

* Exploitation often requires tuning rather than simply executing a module.
* VPN environments require proper listener configuration.
* SYSTEM-level processes provide opportunities for privilege-focused post-exploitation.
* Weak passwords remain vulnerable even when stored as hashes.
* Effective post-exploitation relies heavily on enumeration and automation.

---

# Conclusion

The Blue Room provided practical exposure to the complete penetration testing workflow against a vulnerable Windows target. The exercise covered vulnerability discovery, exploit validation, payload troubleshooting, Meterpreter usage, credential extraction, password recovery, and post-exploitation enumeration.

Most importantly, the room demonstrated that successful exploitation is only one stage of an engagement. Valuable findings often emerge during post-exploitation activities such as privilege analysis, credential access, and system enumeration.

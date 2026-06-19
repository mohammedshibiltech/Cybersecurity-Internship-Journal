# Day 04 - Nmap Host Discovery, NSE Vulnerability Scanning & Metasploit Research

**Date:** 18 June 2026  
**Internship Domain:** VAPT (Vulnerability Assessment & Penetration Testing)

---

## Objective

Today's focus was understanding how Nmap performs host discovery, how firewalls affect network reconnaissance, and how vulnerability identification can be performed using the Nmap Scripting Engine (NSE). The session also introduced vulnerability research and exploit discovery using the Metasploit Framework.

---

## Activities Performed

### 1. Host Discovery and Network Reachability

Started by attempting to verify whether the target machine was online using ICMP echo requests.

During testing, the target did not respond to ping requests. This led to an investigation of how modern firewalls often block or silently drop ICMP traffic while still allowing legitimate services to remain accessible.

### Key Learning

- A failed ping does not necessarily indicate that a host is offline.
- Many environments intentionally block ICMP traffic.
- Host discovery methods should not rely solely on ping responses.

---

### 2. Understanding Nmap's Default Behavior

Performed a standard Nmap scan against the target.

Observed that Nmap first attempts host discovery before beginning port scanning. Because the target was not responding to ping probes, Nmap initially reported the host as down.

### Key Learning

Nmap performs host discovery before scanning ports. If host discovery fails, port scanning does not occur unless explicitly instructed.

Useful switch:

```bash
-Pn
```

This disables host discovery and treats the target as online.

---

### 3. Port Enumeration

After bypassing host discovery restrictions, the target machine was successfully scanned.

Several Windows-related services were identified, including:

- RPC
- NetBIOS
- SMB
- RDP

The presence of SMB immediately indicated that the system could potentially be vulnerable to historical Windows network service attacks.

### Key Learning

Open services often reveal the operating system type and possible attack surface.

---

### 4. Host Discovery Techniques (-sn)

Explored Nmap's host discovery mode using:

```bash
-sn
```

Studied how Nmap chooses different discovery methods depending on:

- User privileges
- Network location
- Whether the target resides on a local or remote network

### Discovery Methods

| Scenario | Method Used |
|-----------|------------|
| Root user on local network | ARP |
| Root user on remote network | ICMP |
| Non-root user | TCP probes |

---

### 5. ARP vs ICMP Investigation

A question arose regarding whether running Nmap as root would force ARP discovery and bypass ICMP filtering.

Further analysis revealed that:

- ARP packets only work within the local broadcast domain.
- ARP requests cannot traverse routers.
- Since the target was reached through a VPN, ARP was not possible.
- Nmap automatically reverted to ICMP-based discovery.

### Key Learning

Network topology directly affects which discovery techniques are available.

---

### 6. NSE Vulnerability Scanning

Introduced to the Nmap Scripting Engine (NSE).

Studied how NSE uses Lua scripts to automate:

- Enumeration
- Vulnerability detection
- Service interrogation
- Authentication testing

Used the vulnerability script category to identify known weaknesses on the target.

### Major Finding

The target was identified as vulnerable to:

**MS17-010 (EternalBlue)**

A critical SMBv1 remote code execution vulnerability.

### Key Learning

NSE can quickly identify publicly known vulnerabilities without requiring manual verification.

---

### 7. Service Version Detection

Studied the purpose of:

```bash
-sV
```

and

```bash
-vv
```

### Purpose

#### -sV

Performs service version detection by actively interrogating open ports and collecting banner information.

#### -vv

Enables increased verbosity, allowing real-time visibility into scanning progress and decisions.

### Observation

Combining service detection with vulnerability scanning provided significantly richer information about the target system.

---

### 8. Vulnerability Research Methodology

After identifying vulnerabilities, focused on understanding how a penetration tester validates findings.

Research process included:

1. Identifying CVE references.
2. Understanding vulnerability details.
3. Reviewing vendor advisories.
4. Searching exploit databases.
5. Checking Metasploit for available modules.

### Resources Studied

- CVE Database
- Microsoft Security Advisories
- Exploit-DB
- Metasploit Framework

---

### 9. Introduction to Metasploit Module Discovery

Learned how to search for exploits within Metasploit using CVE identifiers and vulnerability names.

Observed the differences between:

- Exploit modules
- Auxiliary modules
- Detection modules
- Denial-of-Service modules

### Key Learning

Not every Metasploit module provides system access.

Exploit modules are designed to achieve code execution, while auxiliary modules are often used for scanning, validation, or administration tasks.

---

### 10. Understanding Exploit Configuration

Examined the configuration requirements of a Metasploit exploit module.

Studied common parameters such as:

- RHOSTS
- RPORT
- LHOST
- LPORT

Learned how payloads require both target information and listener configuration before execution.

---

## Key Concepts Learned

- ICMP filtering and firewall behavior
- Nmap host discovery process
- Difference between local and remote network discovery
- ARP limitations across routers
- Port enumeration methodology
- Nmap Scripting Engine (NSE)
- Vulnerability identification using scripts
- Service version detection
- CVE research workflow
- Exploit discovery in Metasploit
- Exploit module configuration

---

## Outcome

Successfully identified a vulnerable Windows host, confirmed the presence of SMB-related vulnerabilities using NSE, researched publicly available exploits, and prepared for exploitation using Metasploit while developing a deeper understanding of network reconnaissance and vulnerability validation techniques.
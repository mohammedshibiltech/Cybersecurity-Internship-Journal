# TryHackMe - Blue Room Write-up

## Room Overview
This write-up details the exploitation of the "Blue" room on TryHackMe. The objective is to identify and exploit vulnerabilities within a Windows machine running Server Message Block (SMB) services, specifically focusing on the critical MS17-010 (EternalBlue) vulnerability. This guide covers the complete methodology from initial reconnaissance and vulnerability identification to exploitation, post-exploitation, and troubleshooting.

---

## Initial Reconnaissance & Enumeration

### Connectivity Testing & Host Discovery
The assessment began with basic connectivity testing via an ICMP echo request (ping). The target did not respond, suggesting either the machine was offline or a firewall was dropping ICMP traffic. 

Given the target was accessed through a VPN routing environment, Address Resolution Protocol (ARP) discovery could not be utilized since ARP packets do not traverse routers. Nmap reverted to ICMP-based discovery, which was being filtered.

To bypass Nmap's default host discovery phase and force a port scan, the `-Pn` flag was utilized.

### Port Scanning
With host discovery disabled, a detailed Nmap scan was executed to enumerate open services.

**Command:** `nmap -Pn TARGET_IP`

**Discovered Ports:**
* **135:** MSRPC
* **139:** NetBIOS
* **445:** SMB
* **3389:** RDP
* **49152 - 49165:** Dynamic RPC

The presence of open ports **139** and **445** immediately highlighted active SMB services, marking them as the primary vector for further investigation.

---

## Vulnerability Identification

To ascertain the target's susceptibility to known exploits, Nmap's Scripting Engine (NSE) was employed to actively interrogate the open services.

**Command:** `nmap -sV -vv --script vuln TARGET_IP`
* `-sV`: Service version detection
* `-vv`: Increased verbosity for real-time probe activity
* `--script vuln`: Executes the vulnerability detection script category

**Key Findings:**
1.  **MS17-010 (CVE-2017-0143):** A critical Remote Code Execution (RCE) vulnerability in Microsoft's SMBv1 implementation. This flaw, notoriously utilized in the WannaCry ransomware attacks, allows an unauthenticated attacker to execute arbitrary code.
2.  **CVE-2009-3103:** An SMBv2 negotiation vulnerability that can trigger an out-of-bounds memory access, potentially leading to RCE or Denial of Service (DoS).

Given its high reliability and severity, MS17-010 was selected as the target vulnerability.

---

## Exploitation

Exploitation was conducted utilizing the Metasploit Framework. 

### Module Selection & Configuration
A search for `MS17-010` within Metasploit yielded several auxiliary and exploit modules (e.g., EternalBlue, EternalRomance). The `exploit/windows/smb/ms17_010_eternalblue` module was selected to establish a reverse shell.

**Mandatory Parameters Configured:**
* `RHOSTS`: Set to the target machine's IP.
* `LHOST`: Set to the VPN tunnel interface (`tun0`).
* `LPORT`: Altered to `443` (HTTPS) to bypass potential egress filtering.

Upon execution, the exploit successfully triggered the vulnerability and provided a standard Windows command shell.

---

## Post-Exploitation & Privilege Escalation

### Shell Upgrade
While a basic command shell is functional, upgrading to a Meterpreter session is highly advantageous for post-exploitation tasks such as file management, credential dumping, and advanced enumeration.

The session was backgrounded, and the `post/multi/manage/shell_to_meterpreter` module was executed, successfully spawning a fully functional Meterpreter session.

### Process Migration
System enumeration revealed the initial shell was operating within an unstable process context. Utilizing Meterpreter's `ps` command, running processes were analyzed to identify stable services executing under `NT AUTHORITY\SYSTEM`. 

The session was successfully migrated to a SYSTEM-owned process. This action achieved three main goals:
1.  Increased session stability.
2.  Maintained top-tier system privileges.
3.  Unlocked access to protected memory spaces for credential extraction.

---

## Credential Harvesting & Password Cracking

With SYSTEM privileges established within a stable process, the Security Account Manager (SAM) database was dumped to extract local account hashes.

**Extracted Accounts:**
* Administrator (RID: 500)
* Guest (RID: 501)
* Jon (RID: 1000)

The NTLM hash associated with the user **Jon** was isolated. NTLM, while a legacy protocol, is highly susceptible to offline attacks. The hash was submitted to an online password cracking utility, which swiftly recovered the plaintext password, underscoring the severe risk posed by inadequate password complexity policies.

---

## Root/User Flag Acquisition

* **Flag 1:** Discovered during initial manual filesystem enumeration, located directly within the root directory of the `C:\` drive.
* **Flag 2 & Flag 3:** To optimize the search process, Meterpreter's native search functionality (`search -f flag*.txt`) was deployed. This automated approach bypassed the need for exhaustive manual directory traversal and rapidly successfully located the remaining two flags.

---

## Troubleshooting & Challenges

Several technical hurdles were encountered and overcome during this engagement:

1.  **Host Discovery Failures:** Initial Nmap scans failed because ICMP traffic was blocked, and standard ARP discovery is impossible across a routed VPN connection. *Solution:* Bypassing the discovery phase with `-Pn` allowed the port scan to proceed successfully.
2.  **Target Instability:** Repeated exploitation attempts caused the target's SMB service to crash, leading to a destabilized system state. *Solution:* A fresh target instance was deployed to ensure a clean state before retrying with refined parameters.
3.  **Payload Delivery Issues:** Default listener configurations failed to establish a reverse connection. *Solution:* The `LHOST` was explicitly bound to the OpenVPN `tun0` interface, and the `LPORT` was adjusted to standard web ports (443/80) to circumvent potential outbound firewall restrictions.

---

## Key Learnings

* **Network Fundamentals:** Understanding the constraints of host discovery over VPNs (ARP vs. ICMP) is critical for effective reconnaissance.
* **Exploitation Nuances:** Identifying a vulnerability does not guarantee instant success. Payload tuning, specifically regarding network interfaces and egress ports, is often required.
* **Target Stability:** Older vulnerabilities like MS17-010 can heavily impact system stability; limiting payload execution attempts prevents service crashes.
* **Post-Exploitation Efficiency:** Leveraging framework capabilities—such as migrating to SYSTEM processes for stability and utilizing automated search features—drastically streamlines the post-exploitation phase.

# Day 2 

## VAPT Overview
**VAPT (Vulnerability Assessment & Penetration Testing)** is a dual-layered cybersecurity approach used to identify, mitigate, and evaluate risks across systems.

### Core Phases
1. **Identify (Discovery Phase):**
   * **Goal:** Create a complete list of known vulnerabilities.
   * **Examples:** Outdated software versions, misconfigured servers, SQL injections.
2. **Evaluate:**
   * **Goal:** Establish context to determine which vulnerabilities pose a real, critical threat. Note that automated scanners cannot reach every server; manual evaluation prevents findings from becoming purely theoretical.
3. **Mitigate (Risk Reduction):**
   * **Goal:** Close the security gap and provide a roadmap for remediation.

**Importance of VAPT:**
* Identify vulnerabilities.
* Meet compliance requirements.
* Mitigate risk.

---

## Vulnerability Assessment (VA) vs. Penetration Testing (PT)

| Feature | Vulnerability Assessment (VA) | Penetration Testing (PT) |
| :--- | :--- | :--- |
| **Definition** | A process used to identify, evaluate, and report on security weaknesses in an organization's digital systems. | A mock, simulated cyberattack to find actionable vulnerabilities in a computer system. |
| **Method** | Automated Scanning (e.g., OpenVAS, Nessus) $\rightarrow$ Known vulnerabilities $\rightarrow$ Categorization (prioritized by severity) $\rightarrow$ Reporting. | Simulated attacks by testers (attackers) using automated and manual testing to demonstrate real-world impact. |
| **Output** | Detailed list of security weaknesses. | Actionable remediation steps based on actual exploitation. |
| **Building Analogy** | Routine inspection to find potential gaps (e.g., checking how many doors, vents, or access points are open). | A simulated break-in to determine exactly *how* an attacker could bypass or defeat security mechanisms. |

---

## Importance & Challenges of VAPT

### Importance
* **Proactive Security:** Helps manage potential gaps before they are actively exploited.
* **Real-World Impact:** PT demonstrates the actual impact of a breach.
* **Compliance & Standards:** Adherence to standards like PCI DSS and NIST 800-53. Continuous monitoring and automated scanning reduce audit findings and penalties.
* **Faster Remediation:** Quicker deployment of patch releases and updates to mitigate cyber threats.
* **Business Value:** Builds stakeholder trust.

### Challenges
* **High Volume of Findings:** Can lead to alert fatigue.
* **False Positives:** Scanners may flag non-issues.
* **"The Snapshot" Problem:** Assessments are only a point-in-time reflection.
* **Blind Spots:** Limited visibility into continuous updates or configuration changes.
* **Service Disruption:** Aggressive scanning or testing can cause outages.
* **Disconnect:** Often a gap between VA findings and PT execution.

---

## Methodologies

### Vulnerability Assessment Methodology
1. **Discovery & Assessment:** Identify IT assets (workstations, endpoints, apps) and use automated scanners to look for weak points.
2. **Analysis & Prioritization:** Analyze flaws to determine potential impact, relevance, and exploitability using VulnDB, OSINT, and threat feeds.
3. **Resolution:**
   * *Remediation:* Patch management / config updates.
   * *Mitigation:* Deploying firewalls, isolating affected systems.
   * *Acceptance:* Accepting lower-risk vulnerabilities.
4. **Confirmation & Monitoring:** Continuous monitoring and confirmation of fixes.
5. **Reporting:** Document findings, including the scanning tools used.
   * **Metrics:** MTTD (Mean Time To Detect), MTTR (Mean Time To Respond).

### Penetration Testing Methodology
1. **Reconnaissance:** Gathering info on the target system (e.g., OSINT).
2. **Target Discovery & Development:** Identification of exploits (e.g., using Nmap).
3. **Exploitation:** The actual attack phase (SQL Injection, Cross-Site Scripting (XSS), DDoS attacks).
4. **Escalation:** Chaining multiple vulnerabilities, maintaining access, and escalating privileges without triggering alarms.
5. **Cleanup & Reporting:** Cleaning up traces of the test and preparing a report detailing the vulnerabilities found, exploits used, and descriptions.

---

## Frameworks and Standards

### Core Frameworks
* **PTES (Penetration Testing Execution Standard):** A formal standard outlining the entire lifecycle across 7 main sections:
  1. Pre-engagement Interactions
  2. Intelligence Gathering
  3. Threat Modelling
  4. Vulnerability Analysis
  5. Exploitation
  6. Post Exploitation
  7. Reporting
* **OWASP (Open Web Application Security Project):** Framework for securing web applications. Provides the OWASP Top 10 list of critical vulnerabilities and secure coding practices (also includes an OWASP Top 10 for mobile).
* **NIST 800-115 / NIST CSF:** National Institute of Standards and Technology frameworks focusing on components like Identify, Govern, Control, and Communicate.
* **OSSTMM:** Open Source Security Testing Methodology Manual, published by ISECOM.

### Compliance Standards
* **PCI DSS:** Payment Card Industry Data Security Standard.
* **HIPAA:** Health Insurance Portability and Accountability Act.
* **GDPR:** General Data Protection Regulation.
* **CERT-In Guidelines:** Computer Emergency Response Team guidelines mandating regular technical testing.

---

## Scanning & Tooling in Penetration Testing

### Testing Approaches
* **Automated Scanning:** Uses software tools to automatically map networks, discover active devices, and identify known vulnerabilities. 
  * *Limitations:* False positives, lack of human logic, blind spots.
* **Manual Pentesting:** Human-led, hands-on security assessment where ethical hackers simulate real-world cyberattacks.

### Common Tools Used
1. **Network Scanners (Discovery and Mapping):**
   * **Nmap:** Open-source tool for network exploration and security auditing. Determines what hosts are available, what services/OS are running, and firewall usage.
   * **Nessus:** Vulnerability scanning tool that produces alerts upon discovering flaws (runs ~1200 checks on a given target).
2. **Web Application Scanners (DAST):** Dynamic Application Security Testing tools that interact with web apps to find flaws like SQL injection and XSS.
   * **OWASP ZAP (Zed Attack Proxy):** Manipulator-in-the-middle proxy.
   * **Burp Suite:** Developed by PortSwigger for web security audits. Features include Interception Proxy, Repeater (replaying requests), Intruder, and a Vulnerability Scanner.
3. **Exploitation Frameworks:**
   * **Metasploit:** Ruby-based penetration testing platform to write, test, and execute exploit code.
   * **SQLMap:** Automated tool that detects and exploits SQL injection flaws in databases.

---

## Network Pentesting & Terminologies

### Network Pentesting Types
* **Internal Network Test:** Pentesters act as internal attackers (or a malicious actor with stolen credentials) to find flaws from within the organization.
* **External Network Test:** Meant to mimic outside attackers trying to break into the network.

### Testing Terminologies
* **Testing Methods:** Black Box Testing, White Box Testing, Gray Box Testing.
* **Checklist:** A structured list of steps, controls, and tasks that guide a cybersecurity professional through an entire evaluation. Usually consists of 4 phases: Pre-Assessment, Vulnerability Assessment, Pentesting, and Post Assessment.
  * *Web App Checklist Examples:*
    * Authentication (verify password policies, MFA).
    * Input Validation (test for injection flaws in search fields).
    * Data Encryption (confirm transit uses HTTPS/TLS 1.3).
    * Session Management.
* **Test Cases:** Specific actionable instructions or scenarios used to verify if a security vulnerability exists.
  * *Key Components:* Test ID & Name (e.g., `TC-WEB-004: SQL injection on login`), Objective, Prerequisites, Test Steps, Expected Results, Actual Result, Status (Pass/Fail).
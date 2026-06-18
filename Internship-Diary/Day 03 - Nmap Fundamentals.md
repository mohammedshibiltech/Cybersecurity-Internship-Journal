# Day 03 - Nmap Fundamentals and Enumeration

**Date:** <Add Date>
**Internship:** VAPT Intern
**Topic:** Network Scanning and Enumeration using Nmap

---

## Objectives

- Understand Nmap architecture and use cases
- Learn host discovery techniques
- Explore TCP/IP-based scanning methods
- Study Nmap Scripting Engine (NSE)
- Learn firewall evasion techniques
- Understand service and OS detection

---

## Topics Covered

### 1. Vulnerability Assessment Lifecycle

1. Information Gathering
2. Scanning & Enumeration
3. Exploitation
4. Reporting

Nmap plays a major role during the scanning and enumeration phase.

---

### 2. Introduction to Nmap

Nmap (Network Mapper) is an open-source network discovery and security auditing tool.

Common use cases:

- Host discovery
- Port scanning
- Service enumeration
- OS detection
- Version detection
- Vulnerability identification using NSE

---

### 3. Host Discovery

Learned methods used by Nmap to identify active hosts:

- ICMP Echo Requests
- TCP SYN Ping
- TCP ACK Ping
- UDP Ping

Example:

```bash
nmap -sn 192.168.1.0/24
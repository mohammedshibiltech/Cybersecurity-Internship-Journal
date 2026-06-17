# Nmap

## Purpose

Network discovery and security auditing tool.

## Common Uses

- Host discovery
- Port scanning
- Service enumeration
- OS detection

## Example Commands

### Host Discovery

```bash
nmap 192.168.1.0/24
```

### Service Detection

```bash
nmap -sV target-ip
```

### OS Detection

```bash
nmap -O target-ip
```
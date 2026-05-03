# Nmap Reconnaissance Scan

## Overview
Network reconnaissance performed from Kali Linux attacker machine targeting Ubuntu victim machine.

## MITRE ATT&CK
- Technique: T1046 — Network Service Scanning
- Tactic: Reconnaissance

## Attacker Machine
- OS: Kali Linux
- IP: 192.168.18.132

## Target Machine
- OS: Ubuntu 24.04
- IP: 192.168.18.128

## Commands Executed

### Scan 1 — Host Discovery
```bash
nmap -sn 192.168.18.128
```

### Scan 2 — SYN Stealth Scan
```bash
sudo nmap -sS 192.168.18.128
```

### Scan 3 — Service Version Detection
```bash
sudo nmap -sV 192.168.18.128
```

### Scan 4 — Aggressive Full Scan
```bash
sudo nmap -A 192.168.18.128
```

## Results
- Host is up
- Port 22/tcp open — OpenSSH 9.6p1 Ubuntu
- OS detected: Linux 4.15 - 5.19
- MAC Address: 00:0C:29:13:02:E4 (VMware)

## Evidence
See screenshots/Kali_Nmap.png and screenshots/Kali_Nmap_1.png
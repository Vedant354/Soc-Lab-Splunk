# SSH Brute Force Attack

## Overview
Credential attack performed against SSH service on Ubuntu victim machine using Hydra after reconnaissance revealed port 22 open.

## MITRE ATT&CK
- Technique: T1110.001 — Brute Force: Password Guessing
- Tactic: Credential Access

## Attacker Machine
- OS: Kali Linux
- IP: 192.168.18.132
- Tool: Hydra v9.6

## Target Machine
- OS: Ubuntu 24.04
- IP: 192.168.18.128
- Service: SSH (port 22)

## Command Executed

```bash
hydra -l vegion-target -P /usr/share/wordlists/rockyou.txt ssh://192.168.18.128 -t 4 -V
```

## Attack Parameters
- Username targeted: vegion-target
- Wordlist: rockyou.txt (14,344,399 passwords)
- Threads: 4
- Protocol: SSH

## Results
- Total failed attempts: 123
- Successful login: 1
- Compromise time: 2026-05-02T15:20:59

## Evidence
See screenshots/Kali_Hydra.png and screenshots/Kali_Success_SSH.png
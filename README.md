# SOC Lab — Splunk Detection Engineering

![Status](https://img.shields.io/badge/Status-Complete-brightgreen)
![SIEM](https://img.shields.io/badge/SIEM-Splunk-orange)
![Platform](https://img.shields.io/badge/Platform-VMware-blue)
![ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-red)

A functional mini SOC environment built to simulate real attacks, ingest logs, write detection queries, and document investigations — targeting junior SOC analyst skill demonstration.

---

## Lab Architecture

| Machine | OS | Role | IP |
|---|---|---|---|
| Attacker | Kali Linux | Attack simulation | 192.168.18.132 |
| Victim | Ubuntu 24.04 | Target machine | 192.168.18.128 |
| Monitor | Ubuntu + Splunk | Log collection & detection | 192.168.18.129 |

---

## Environment

- Virtualized on **VMware Workstation** (Windows 11 host)
- Ubuntu target auto-forwards logs to Splunk via **Universal Forwarder**
- Logs indexed under `index=linux` `sourcetype=linux_secure`
- Live log ingestion confirmed and running

---

## Project Phases

| Phase | Description | Status |
|---|---|---|
| Phase 1 | Lab setup & log ingestion | ✅ Complete |
| Phase 2 | Attack simulation (Nmap + SSH Brute Force) | ✅ Complete |
| Phase 3 | SPL detection queries | ✅ Complete |
| Phase 4 | Splunk dashboard | ✅ Complete |
| Phase 5 | Incident report & published findings | ✅ Complete |

---

## Attack Simulation

### Stage 1 — Reconnaissance (T1046)
Nmap was used to perform network scanning and service discovery against the target machine.

```bash
nmap -sn 192.168.18.128       # Host discovery
nmap -sS 192.168.18.128       # SYN stealth scan
nmap -sV 192.168.18.128       # Service version detection
nmap -A  192.168.18.128       # Aggressive scan
```

### Stage 2 — Credential Access (T1110.001)
Hydra was used to perform SSH brute force using the rockyou wordlist.

```bash
hydra -l vegion-target -P /usr/share/wordlists/rockyou.txt ssh://192.168.18.128 -t 4 -V
```

### Stage 3 — Initial Access (T1078)
Successful SSH login achieved after brute force — unauthorized access confirmed.

---

## Detection Queries (SPL)

**SSH Brute Force Detection:**
```spl
index=linux sourcetype=linux_secure "Failed password"
| stats count as failed_attempts by host
| where failed_attempts > 10
| sort -failed_attempts
```

**Successful Login Detection:**
```spl
index=linux sourcetype=linux_secure "Accepted password"
| table _time, host, _raw
| sort _time
```

**Full Attack Timeline:**
```spl
index=linux sourcetype=linux_secure ("Failed password" OR "Accepted password")
| eval status=if(match(_raw,"Failed"),"FAILED","SUCCESS")
| table _time, host, status, _raw
| sort _time
```

---

## MITRE ATT&CK Mapping

| Tactic | Technique | ID | Tool |
|---|---|---|---|
| Reconnaissance | Network Service Scanning | T1046 | Nmap |
| Credential Access | Brute Force: Password Guessing | T1110.001 | Hydra |
| Initial Access | Valid Accounts | T1078 | SSH |

---

## Dashboard

A 5-panel Splunk dashboard was built to visualize the full attack chain:

- Nmap Reconnaissance Activity (line chart)
- SSH Brute Force Attempts (column chart)
- Total Failed Login Attempts (single value)
- Successful Logins Detected (single value)
- Full Attack Timeline (table)

📄 [View Dashboard PDF](dashboard/soc_dashboard.pdf)

---

## Incident Report

A full incident report documenting the attack chain, IOCs, MITRE mapping, detection methods, and remediation recommendations.

📄 [View Incident Report](INCIDENT_REPORT.md)

---

## Screenshots

| Screenshot | Description |
|---|---|
| ![](screenshots/Kali_Nmap.png) | Nmap scans running from Kali |
| ![](screenshots/Kali_Hydra.png) | Hydra brute force running |
| ![](screenshots/Kali_Success_SSH.png) | Successful SSH login |
| ![](screenshots/Splunk_see_Hydra.png) | Brute force detected in Splunk |
| ![](screenshots/Splunk_Success_SSH.png) | Successful login detected in Splunk |
| ![](screenshots/Splunk_Dashboard.png) | SOC Detection Dashboard |

---

## Tools Used

- **Splunk Free** — SIEM and log analysis
- **Kali Linux** — Attack platform
- **Nmap** — Network reconnaissance
- **Hydra** — SSH brute force
- **VMware Workstation** — Virtualization
- **Universal Forwarder** — Log shipping

---

*Built as a portfolio project to demonstrate SOC analyst skills including attack simulation, log analysis, detection engineering, and incident documentation.*

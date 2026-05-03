# Incident Report — SSH Brute Force & Unauthorized Access

**Incident ID:** SOC-2026-001  
**Date:** May 2, 2026  
**Severity:** High  
**Status:** Resolved  
**Analyst:** Vedant  

---

## 1. Incident Summary

A multi-stage attack was detected originating from IP `192.168.18.132` (Kali Linux attacker) targeting the internal Ubuntu server at `192.168.18.128`. The attacker performed network reconnaissance using Nmap followed by an SSH brute force attack using Hydra. The attack resulted in **one successful unauthorized SSH login**, confirming a full compromise of the target machine.

---

## 2. Attack Timeline

| Time (IST) | Event |
|---|---|
| 14:34 | Attacker runs Nmap host discovery (`-sn`) against target |
| 14:34 | Attacker runs Nmap SYN scan (`-sS`) — port 22 SSH discovered open |
| 14:34 | Attacker runs Nmap service scan (`-sV`) — OpenSSH 9.6p1 identified |
| 14:37 | Attacker runs aggressive Nmap scan (`-A`) — OS fingerprinting performed |
| 15:15 | Hydra SSH brute force begins — 123 failed login attempts recorded |
| 15:20:59 | **Successful SSH login — target compromised** |

---

## 3. Indicators of Compromise (IOCs)

| IOC | Value |
|---|---|
| Attacker IP | `192.168.18.132` |
| Target IP | `192.168.18.128` |
| Target Hostname | `vegion-target-VMware-Virtual-Platform` |
| Attacked Service | SSH (port 22) |
| Attack Tool — Recon | Nmap 7.95 |
| Attack Tool — Brute Force | Hydra v9.6 |
| Wordlist Used | `rockyou.txt` |
| Failed Login Count | 123 |
| Successful Logins | 1 |
| Compromise Time | 2026-05-02T15:20:59 |

---

## 4. MITRE ATT&CK Mapping

| Tactic | Technique | ID | Tool |
|---|---|---|---|
| Reconnaissance | Network Service Scanning | T1046 | Nmap |
| Credential Access | Brute Force: Password Guessing | T1110.001 | Hydra |
| Initial Access | Valid Accounts | T1078 | SSH |

---

## 5. Detection Method

All detections were performed using **Splunk SIEM** with logs forwarded from the Ubuntu target via Universal Forwarder.

**Query 1 — SSH Brute Force Detection:**
```spl
index=linux sourcetype=linux_secure "Failed password"
| stats count as failed_attempts by host
| where failed_attempts > 10
| sort -failed_attempts
```

**Query 2 — Successful Login Detection:**
```spl
index=linux sourcetype=linux_secure "Accepted password"
| table _time, host, _raw
| sort _time
```

**Query 3 — Full Attack Timeline:**
```spl
index=linux sourcetype=linux_secure ("Failed password" OR "Accepted password")
| eval status=if(match(_raw,"Failed"),"FAILED","SUCCESS")
| table _time, host, status, _raw
| sort _time
```

---

## 6. Evidence

| Evidence | File |
|---|---|
| Nmap scan running | `screenshots/Kali_Nmap.png` |
| Nmap aggressive scan results | `screenshots/Kali_Nmap_1.png` |
| Hydra brute force running | `screenshots/Kali_Hydra.png` |
| Successful SSH login | `screenshots/Kali_Success_SSH.png` |
| Splunk Nmap events | `screenshots/Splunk_Nmap_read.png` |
| Splunk brute force events | `screenshots/Splunk_see_Hydra.png` |
| Splunk brute force count | `screenshots/Splunk_see_Host_Hydra.png` |
| Splunk successful login | `screenshots/Splunk_Success_SSH.png` |
| SOC Dashboard | `dashboard/soc_dashboard.pdf` |

---

## 7. Root Cause Analysis

The target machine had SSH exposed with **password authentication enabled** and no brute force protection mechanism such as `fail2ban`. This allowed the attacker to attempt unlimited password guesses until finding the correct credentials.

---

## 8. Recommendations

| Recommendation | Priority |
|---|---|
| Disable SSH password authentication — use SSH keys only | Critical |
| Install and configure `fail2ban` to block IPs after 5 failed attempts | High |
| Implement firewall rules to restrict SSH access to trusted IPs only | High |
| Set up real-time Splunk alerts for brute force threshold breaches | Medium |
| Regularly audit successful SSH logins from external IPs | Medium |

---

## 9. Lessons Learned

- Nmap reconnaissance and SSH brute force are detectable through auth.log analysis in Splunk
- A single exposed service with weak authentication controls can lead to full system compromise
- Detection without alerting is insufficient — automated response mechanisms should be in place
- Log forwarding must be verified before attack simulation to ensure evidence is captured

---

*Report generated as part of a controlled SOC home lab simulation. All attacks were performed in an isolated VMware environment.*

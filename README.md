# Soc-Lab-Splunk
Splunk-based Mini SOC Lab | Log Ingestion · Attack Simulation · Detection Engineering
Mini SOC Lab — detection engineering project using Splunk.
# SOC Lab — Splunk Detection Engineering

## Overview
A functional mini SOC environment built to simulate real attacks,
ingest logs, write detection queries, and document investigations.

## Lab Architecture
| Machine | OS | Role |
|---|---|---|
| Attacker | Kali Linux | Attack simulation |
| Victim | Ubuntu | Target machine |
| Monitor | Ubuntu + Splunk | Log collection & detection |

## Environment
- Virtualized on VMware (Windows 11 host)
- Ubuntu target auto-forwards logs to Splunk
- Live log ingestion confirmed and running

## Project Phases
- [x] Phase 1 — Lab setup & log ingestion ✅
- [x] Phase 2 — Attack simulation (Nmap, brute force)
- [x] Phase 3 — Detection queries in Splunk
- [x] Phase 4 — Investigation & incident report
- [x] Phase 5 — Published findings

## Tools
- Splunk Free
- Kali Linux
- VMware Workstation
- Ubuntu Server

# Enterprise Security Monitoring & Incident Response Lab

**Hands-on cybersecurity portfolio project | Windows 11 · Ubuntu Server 24.04.5 LTS · VMware Workstation · Wazuh · Sysmon · Wireshark · Nmap**

> **Lab scope:** All monitoring and testing took place on personally controlled systems. The simulated events are not evidence of an actual intrusion. Screenshots and private network information are intentionally not included in this public-ready starter repository.

## Overview

Built a self-hosted security monitoring environment with the Wazuh manager, indexer and dashboard on an Ubuntu Server virtual machine and a Windows 11 endpoint monitored by the Wazuh agent and Microsoft Sysmon. Implemented two validated custom Wazuh detections, investigated packet captures, checked Sysmon network telemetry forwarding, and assessed the Ubuntu server's exposed TCP services with Nmap.

## Architecture

```text
Windows 11 endpoint / VMware host
  |-- Windows Security log (Event ID 4625)
  |-- Microsoft Sysmon (Event IDs 1 and 3)
  |-- Wazuh Windows agent
  |           |
  |           v
  +----> Ubuntu Server VM (VMware NAT)
              |-- Wazuh Manager / rules engine
              |-- Wazuh Indexer
              |-- Wazuh Dashboard

Windows 11 Wireshark: independent packet capture and analysis
Ubuntu Nmap: authorized localhost scan; Windows: VM reachability checks
```

## Validated results

| Exercise | Evidence | Outcome |
| --- | --- | --- |
| SOC #001 — failed Windows logons | Windows Security Event ID 4625; built-in Wazuh Rule 60122; custom Rule **100100** (5 events / 60 seconds, Level 10) | **Alert validated** |
| SOC #002 — PowerShell process creation | Sysmon Event ID 1; native Wazuh Rule 92027; marker `SOC-LAB-TEST-002`; custom Rule **100101** (Level 7) | **Alert validated** |
| SOC #003 — network analysis | Wireshark DNS, ICMP, TCP and protocol/conversation analysis; Sysmon Event ID 3 received in Wazuh JSON archives | **Telemetry validated; custom Rule 100102 alert unresolved** |
| Assessment #004 — service exposure | Nmap localhost/service scan, `ss`, UFW inspection, Windows-to-VM reachability | **Baseline assessment complete; no exploited CVE demonstrated** |

## Key technical findings

**Network monitoring:** An authorized PowerShell TCP connection to destination port 443 was logged by Sysmon Event ID 3 and arrived at Wazuh (`windows_eventchannel` decoder and `archives.json`). Rule **100102** passed configuration validation but produced **no alert** in `alerts.json` or Threat Hunting. This is documented as an unresolved limitation—not a successful detection.

**Exposure assessment:** Windows could connect to the Ubuntu VM on TCP ports **22, 443, 1514, 1515, and 55000**. Ports **80 and 9200** did not accept Windows connections. The Indexer on port 9200 listened only on loopback, whereas several Wazuh components listened on network interfaces. The UFW status was **inactive**. These are **service-exposure/hardening observations, not confirmed vulnerabilities**.

## Investigation notes

- [SOC #001 — Windows authentication monitoring](investigations/SOC-001-failed-logons.md)
- [SOC #002 — Sysmon process execution](investigations/SOC-002-process-execution.md)
- [SOC #003 — network traffic and Sysmon correlation](investigations/SOC-003-network-monitoring.md)
- [Assessment #004 — Nmap exposure review](investigations/Assessment-004-nmap.md)
- [Example detection rules](detection-rules/README.md)
- [Lab command reference](documentation/commands.md)

## Evidence and report

The complete report and original screenshots are retained **privately**. Add only redacted screenshots to `evidence/` and an appropriately sanitized final report to `reports/`. Screenshots can expose Windows usernames, workstation names, internal IPs, hashes and browser session identifiers. Do **not** publicly commit raw `.pcapng` captures, log archives, agent credentials, certificate keys or Wazuh configuration files containing secrets.

## Lessons learned and next steps

- Validate Windows telemetry locally before debugging SIEM ingestion.
- Distinguish raw archived events from generated Wazuh alerts.
- Use process image, parent, command line and ProcessGuid to give events context.
- Check service exposure from **both localhost and a separate host**.
- Tune network detection in a future iteration; restrict exposed management interfaces only after planning access rules and testing continuity.

**Ethics:** Only authorized lab systems were tested. No exploitation, malware execution, or unauthorized scans were carried out.

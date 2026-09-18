# Home SOC / SIEM

## Overview

The HomeLab was extended with a dedicated Wazuh-based Home SOC.

The environment includes:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard
- Windows 11 endpoint agent
- Ubuntu Home Server agent
- Sysmon telemetry on Windows

The basic detection flow is:

**Logs → Analysis → Rule → Alert → Investigation**

## Windows Telemetry

Windows telemetry includes:

- Security events
- Application and System events
- Microsoft Defender Operational events
- PowerShell Operational events
- Sysmon events

Sysmon provides additional endpoint telemetry such as process creation, selected network activity, DNS activity, file creation and registry activity.

## Linux Telemetry

The Ubuntu Home Server provides security and operational telemetry including:

- authentication activity
- sudo activity
- system logs
- Auditd events
- package-management activity
- selected system-state information
- file integrity monitoring

## Detection Engineering

Custom detection rules were created only where built-in Wazuh coverage was not already sufficient.

Examples include:

- Scheduled Task executing PowerShell
- suspicious Windows service path in a user-writable location
- targeted false-positive tuning for known legitimate Docker-related audit activity

Positive and negative tests were used to verify that detections trigger on the intended behavior while reducing unnecessary alerts.

Where Wazuh already provided suitable built-in detection, the built-in rule was retained instead of creating unnecessary duplicate logic.

## MITRE ATT&CK

Relevant detections are mapped to MITRE ATT&CK techniques.

This makes the detections easier to explain using a standardized attacker-behavior framework rather than describing each event in isolation.

## Incident Investigation

The SOC has been used for real troubleshooting and investigation, not only simulated alerts.

Evidence sources used during investigations have included:

- Wazuh alerts
- Sysmon
- SSH authentication logs
- Auditd
- sudo logs
- firewall logs
- Windows telemetry
- Microsoft Defender
- file signatures
- process and network inspection

## Security Design

The SOC is intentionally designed with limited exposure.

Key decisions include:

- private access through Tailscale
- minimal published host ports
- endpoint agents restricted to the required ingestion path
- no unnecessary public management exposure
- no automatic Wazuh Active Response on production endpoints

## Indexer Health

The Wazuh Indexer runs as a single-node environment.

A yellow cluster state can therefore be expected when replica shards cannot be assigned to another node.

The important health condition is that primary shards remain available and there are no unassigned primary shards.

## Alert Retention

A retention policy is configured for Wazuh alert indices with a 90-day retention target.

## Recovery Status

The recovery design protects important Wazuh Manager state and also creates automated OpenSearch snapshots of alert indices through the Snapshot API.

Snapshot retention keeps the latest 14 snapshots, and a selected-index restore test has passed.

This proves automated snapshot creation and selected-index recovery. A full clean-system disaster-recovery restore of the complete SOC has not yet been performed.

## Detailed SOC Repository

The dedicated SOC repository contains additional detection, incident, recovery and stability documentation:

https://github.com/nick1-creator/HomeLab-SOC

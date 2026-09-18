# Interview Guide

## 30-Second Version

> I built a HomeLab that started as a virtual lab and evolved into a 24/7 Ubuntu home server. I use Docker for self-hosted services, Tailscale and private HTTPS for secure access, several monitoring layers for availability and health, encrypted Restic backups with restore testing, AIDE for file integrity monitoring, and Wazuh with Sysmon for endpoint monitoring, custom detections and incident investigation.

## Easy Memory Method

Remember:

**Server → Docker → Services → Security → Monitoring → Backup → SOC**

That sequence is enough to rebuild the full explanation naturally in an interview.

## Why Docker?

Docker made it possible to separate services into manageable application stacks with repeatable configuration and controlled networking.

It also made it easier to understand which services communicate with each other and which ports actually need host exposure.

## Why Tailscale?

The goal was remote access without exposing HomeLab management services directly to the public Internet.

Tailscale provides a private access path between approved devices.

## Why a Private CA?

The Private CA allows internal HomeLab services to use trusted HTTPS certificates without requiring those internal services to be publicly reachable.

Simple explanation:

**Private CA = my private authority that confirms that my internal sites are genuine.**

## Why LUKS and Restic Together?

They protect different layers.

- LUKS protects the physical backup disk while it is locked.
- Restic protects and manages the backup repository and snapshots.

The backup disk is opened for the backup process and closed again afterward.

## Why Restore Testing?

A backup that has never been restored is not fully proven.

The project therefore includes restore validation instead of relying only on a successful backup command.

## What Is AIDE?

AIDE is file integrity monitoring.

It stores a trusted baseline of important files and later reports when files are added, removed or changed.

When AIDE reported many differences during development, the differences were investigated before creating a new trusted baseline.

## What Is Wazuh?

Wazuh collects and analyzes security telemetry from endpoints.

Simple flow:

**Logs → Analysis → Alert**

It does not mean that Wazuh sees every packet on the network.

## What Is Sysmon?

Sysmon provides more detailed Windows endpoint telemetry than the normal event logs alone.

For example, it can record process creation and other selected system activity that can then be analyzed by Wazuh.

## What Is a Custom Detection Rule?

A custom detection rule is a rule written for a specific behavior or pattern.

Simple explanation:

**Event → Rule → Alert**

Custom rules were created only where built-in Wazuh detection was not already sufficient.

## What Is MITRE ATT&CK?

MITRE ATT&CK is a standardized framework describing attacker tactics and techniques.

A simple way to remember it:

**MITRE ATT&CK = a dictionary of common attacker techniques.**

## Example Detection

One custom detection identifies a Windows Scheduled Task that executes PowerShell.

Another looks for suspicious Windows service paths located in user-writable locations.

Both were tested with positive and negative cases.

## Example Incident

A scheduled Restic backup reported failure because the repository contained a stale lock.

The investigation checked:

- authentication activity
- sudo and Auditd evidence
- Wazuh
- firewall activity
- active processes
- Docker exposure
- Windows telemetry

The evidence strongly correlated the stale lock with authorized maintenance activity, and no evidence of compromise was identified.

This is a useful interview example because it demonstrates the difference between immediately assuming an attack and performing evidence-based incident investigation.

## Known Limitation

The Wazuh recovery design protects important Manager state and uses automated OpenSearch snapshots for alert indices. A selected-index restore test has passed.

What has not yet been proven is a full clean-system disaster-recovery restore of the complete SOC or independent off-site recovery.

These limitations are documented rather than hidden.

That is an important engineering principle:

**Know what the system protects, and also know what it does not protect.**

## Strong Interview Closing

> The biggest thing I learned from the project was not just how to install tools. I learned how the different layers connect: infrastructure, networking, security, monitoring, backup, detection and incident response. I also learned to validate assumptions with tests instead of treating a configuration as correct just because it looks correct.

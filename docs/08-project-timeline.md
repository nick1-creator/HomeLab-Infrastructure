# Project Timeline

The project evolved in stages rather than being designed as one large system from the beginning.

## Phase 1 — Virtual Lab

The project started as a virtualized learning environment used for Linux, Windows, networking and infrastructure practice.

## Phase 2 — Ubuntu Home Server

The environment moved toward a dedicated Ubuntu server intended to operate continuously.

This introduced real server administration responsibilities such as:

- storage
- permissions
- networking
- updates
- services
- remote administration

## Phase 3 — Docker Infrastructure

Docker and Docker Compose became the main application platform.

Services were separated into manageable stacks and private container networks.

## Phase 4 — Self-Hosted Services

Practical services were deployed for real daily use, including:

- private photo management
- DNS filtering
- dashboards
- monitoring
- documentation
- RSS
- bookmarks
- subscription tracking
- identity services

## Phase 5 — Security Hardening

Security controls were added around the infrastructure:

- Tailscale private access
- restricted host exposure
- reverse proxy
- internal HTTPS
- private CA
- LUKS encryption
- firewall restrictions
- SSH hardening
- Docker exposure reduction

## Phase 6 — Monitoring

Multiple monitoring layers were added:

- service availability monitoring
- server and container resource monitoring
- physical disk health monitoring
- dashboards and observability

## Phase 7 — Backup & Recovery

The HomeLab gained:

- automated Restic backups
- encrypted backup storage
- retention
- stateful application backup preparation
- restore testing
- recovery documentation

## Phase 8 — Home SOC

Wazuh and Sysmon were added to turn the infrastructure into a small operational SOC environment.

This phase introduced:

- endpoint telemetry
- SIEM analysis
- detection engineering
- MITRE ATT&CK mapping
- false-positive tuning
- incident investigation
- alert retention

## Phase 9 — Integrity Monitoring

AIDE was added for host-based file integrity monitoring of sensitive infrastructure and security files.

A controlled re-baseline procedure was later performed after legitimate project changes were investigated and verified.

## Phase 10 — Incident Response

A real Restic stale-lock event was investigated as an operational incident.

The investigation used Linux, Windows, Wazuh, firewall, audit and authentication evidence to distinguish an operational problem from a cybersecurity compromise.

## Phase 11 — Portfolio & QA

The final phase focuses on:

- sanitized public documentation
- architecture diagrams
- screenshots
- incident reports
- checksum validation
- secret scanning
- stability validation
- final recovery testing

The project story can be summarized as:

**Virtual Lab → Server → Docker → Services → Security → Monitoring → Backup → SOC → Incident Response → Portfolio**

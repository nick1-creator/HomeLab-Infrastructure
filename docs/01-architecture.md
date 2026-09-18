# Architecture

The environment evolved from a VMware-based lab into a dedicated Ubuntu home server.

## Main Components

- Windows 11 administration laptop
- Ubuntu 24.04 LTS home server
- Docker and Docker Compose
- Separate application-data storage
- Separate encrypted backup disk
- Tailscale private network
- Nginx Proxy Manager
- Monitoring stack
- Wazuh Home SOC

## Storage Design

The operating system, application data, and backups are separated. Sensitive system and backup storage use encryption.

# Infrastructure

## Ubuntu Server

The HomeLab runs continuously on Ubuntu Server.

Administration includes service management, storage, permissions, systemd, logging, resource monitoring and Docker lifecycle management.

## Docker

Most applications run in Docker Compose stacks.

Design goals:
- persistent data outside containers
- private internal networks
- minimal host port publishing
- centralized reverse proxy
- repeatable deployment

# Security

Security decisions include:

- Tailscale instead of public management exposure
- minimal host port publishing
- private Docker networks
- reverse proxy access
- private HTTPS using a HomeLab CA
- LUKS encryption
- encrypted backup storage
- restricted access paths
- no unnecessary direct Docker socket exposure
- no automatic Wazuh Active Response on production systems

## File Integrity Monitoring

AIDE is used for host-based file integrity monitoring.

The monitored areas include:

- SSH and authentication configuration
- local account and group files
- sudo configuration
- firewall and selected kernel security settings
- HomeLab infrastructure configuration
- protected application secrets
- private PKI material

AIDE runs automatically through a systemd timer and compares the current filesystem state against a trusted baseline.

During project development, the original baseline became outdated after multiple legitimate infrastructure and security changes. Before creating a new baseline, the reported differences were reviewed and correlated with known administrative activity and package updates.

The previous baseline was preserved for recovery and investigation purposes before a controlled re-baseline was performed.

After the re-baseline:

- AIDE reported no filesystem differences
- the check completed with exit code `0`
- the automated systemd service completed successfully
- the baseline and AIDE configuration are included in the encrypted Restic backup scope

A non-zero AIDE result is treated as a file-integrity finding that requires investigation rather than automatically being assumed to be a software failure.

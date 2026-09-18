# Incident Report: Restic Stale Repository Lock

## Status

**Resolved — Operational Incident**

No evidence of compromise was identified during the investigation.

## Summary

On September 4, 2026, the scheduled HomeLab backup service reported a failure during the Restic retention phase.

The actual backup completed successfully and created snapshot:

`9f8f55db`

The service failed afterward because the Restic repository contained a stale lock created on:

`2026-09-03 14:04:16 IDT`

The lock referenced PID `665139`, running as root.

## Impact

- No backup data was lost.
- The September 4 snapshot was created successfully.
- Restic repository integrity remained intact.
- Retention initially did not complete.
- The encrypted backup disk was safely closed after the failed service run.
- Production services returned to normal operation.

## Investigation

The investigation included multiple independent evidence sources:

- SSH authentication logs
- Linux login history
- sudo logs
- Auditd events
- shell history
- systemd timers and services
- cron persistence checks
- SSH authorized keys
- UFW firewall rules and logs
- active processes and network connections
- Docker container exposure
- Wazuh alerts
- Windows Sysmon telemetry
- Microsoft Defender
- Windows Authenticode signatures

### Key Findings

The stale lock strongly correlated with authorized manual maintenance activity.

At `13:57:17`, an SSH session was established from the authorized Windows laptop through Tailscale.

At `14:04:10`, the authenticated user opened a root shell with `sudo bash`.

The Restic lock was created six seconds later at:

`14:04:16`

The root shell closed at:

`14:04:17`

Several additional manual root-shell and backup-disk mount/unmount sequences followed.

No scheduled HomeLab backup or Restic maintenance timer was running at the time the stale lock was created.

The exact Restic command that created the lock was not captured inside the root shell, so the attribution is high-confidence rather than absolute.

## Cybersecurity Review

Additional investigation found:

- No unauthorized SSH login
- No unknown SSH key
- No unauthorized privileged account
- SSH password authentication disabled
- Direct root SSH disabled
- SSH restricted through UFW to the authorized Tailscale laptop
- No suspicious systemd or cron persistence during the incident window
- No suspicious privileged Docker containers
- No evidence of HomeServer compromise in Wazuh
- Windows high-severity alerts were attributed to legitimate software including Tailscale, Microsoft Edge, PowerShell, Google Chrome and Microsoft Bing Wallpaper
- Microsoft Defender reported no detected threats
- Bing Wallpaper `UnInstDaemon.exe` was verified with a valid Microsoft Authenticode signature

**Conclusion: No evidence of compromise was identified.**

## Recovery

After confirming that PID `665139` no longer existed and that no Restic process was active:

1. The encrypted backup vault was opened manually.
2. `restic unlock` removed one stale lock.
3. `restic check` completed successfully with no errors.
4. Retention completed successfully using:
   - 7 daily snapshots
   - 4 weekly snapshots
   - 6 monthly snapshots
5. Snapshot `9f8f55db` was confirmed as the latest successful backup.
6. The backup filesystem was unmounted.
7. The LUKS mapper was closed.
8. The failed systemd state was reset.
9. The daily backup timer remained enabled.

## Root Cause

**Stale Restic repository lock left during authorized manual Restic verification/maintenance activity.**

This was classified as an operational incident rather than a cybersecurity incident.

## Lessons Learned

- Avoid interrupting manual Restic operations while the repository is open.
- Verify that no Restic process is running before removing a stale lock.
- Do not automatically run `restic unlock` blindly inside the production backup script.
- Preserve detailed Auditd, SSH and systemd logs for incident reconstruction.
- Use the next normal scheduled backup to validate that the issue does not recur.

## Follow-Up

The next automatic backup run will be used as the final validation that normal unattended backup operation remains healthy.

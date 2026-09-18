# Backup & Recovery

## Backup Design

The HomeLab uses Restic for automated backups.

The Restic repository is stored on a dedicated backup disk protected with LUKS encryption.

The normal backup workflow is:

1. unlock the encrypted backup storage
2. mount the backup filesystem
3. prepare stateful application backups
4. create a Restic snapshot
5. apply the retention policy
6. validate the operation
7. unmount the backup filesystem
8. close the encrypted storage

The backup disk is not intended to remain mounted during normal server operation.

## Backup Scope

The backup includes the main HomeLab infrastructure and important recovery material, including:

- Docker infrastructure and application configuration
- persistent HomeLab data
- private PKI recovery material
- selected operating-system security configuration
- SSH and firewall configuration
- systemd units and maintenance scripts
- AIDE configuration and integrity baselines
- recovery documentation and recovery artifacts

Stateful applications are prepared before the Restic snapshot so that important application data can be recovered consistently.

## Retention

The current Restic retention policy keeps:

- 7 daily snapshots
- 4 weekly snapshots
- 6 monthly snapshots

## Wazuh Recovery

The Wazuh Manager state is included in the backup workflow.

The hardened Manager-state procedure creates a quiesced copy:

1. temporarily suppress automatic Manager-container restart
2. gracefully stop Filebeat
3. stop Wazuh services
4. fully stop the Wazuh Manager container
5. verify that the container is stopped and no Manager/Filebeat writers are active
6. copy the required state from the stopped container
7. create and validate the archive
8. start the Manager container
9. verify Wazuh, Filebeat and Indexer connectivity
10. restore the normal container restart policy

The procedure was validated in a live integration test and then confirmed by consecutive scheduled backups on 2026-09-09 and 2026-09-10.

Separately, the backup-notification history shows successful scheduled HomeLab backups on every date from **2026-09-06 through 2026-09-18**. The latest confirmed success was **2026-09-18 03:31:56 IDT**.

These notifications provide continuing operational success evidence. The deeper component-level proof for Wazuh Manager quiescing, Filebeat recovery, Indexer snapshots and vault closure remains the validated 2026-09-09 and 2026-09-10 runs described above.

### Wazuh Indexer Recovery Status

Automated OpenSearch snapshots protect Wazuh alert indices through the Snapshot API.

The current process retains the latest 14 snapshots. Scheduled validation confirmed successful snapshots with zero failed shards, and a selected-index restore test has also passed.

### Independent / Offline USB Recovery

A separate encrypted Restic repository was created on a dedicated USB device formatted as NTFS.

The USB filesystem itself is not whole-device encrypted; the backup data inside the Restic repository is encrypted by Restic.

Validation included:

- copying a complete HomeLab snapshot to the independent repository
- `restic check --read-data` across the complete USB repository
- successful validation of all repository packs
- an independent restore on the Windows administration laptop
- SHA256 comparison of selected restored files against their source copies
- safe removal and disconnected storage after validation

This proves an independent/offline recovery path for the tested scope.

The USB is stored in the same home as the server and therefore is **not physically off-site**.

A full clean-system restore of the complete HomeLab/SOC has not been claimed.

## Restore Validation

Restore testing has been performed because a backup is only useful if it can actually be recovered.

Validation has included:

- successful Restic snapshots
- repository integrity checking
- file recovery testing
- application/state recovery validation
- verification that encrypted backup storage is closed after use

## File Integrity Recovery

AIDE configuration and its current baseline are included in the encrypted backup scope.

Before a controlled AIDE re-baseline, the previous trusted baseline was preserved as a recovery and investigation artifact.

## Operational Incident

A scheduled backup later reported a failure because of a stale Restic repository lock.

The backup snapshot itself had completed successfully, and the failure occurred during the retention phase.

The incident was investigated using multiple evidence sources and was attributed with high confidence to authorized manual maintenance activity.

No evidence of compromise was identified.

See:

[Restic Stale Repository Lock Incident](10-restic-stale-lock-incident.md)

## Design Principle

The backup design follows a simple rule:

**Backup → Verify → Restore Test → Protect the Recovery Path**

A successful backup command alone is not treated as proof of recoverability.

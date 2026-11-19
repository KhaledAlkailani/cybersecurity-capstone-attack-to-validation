# Evidence Index

This directory documents the evidence model used during the investigation.

The public repository intentionally does **not** publish raw sensitive evidence such as full packet captures, credentials, session material, or configuration archives.

## Evidence Sources by Server

### Server 1

| Source | Purpose |
|---|---|
| Samba configuration | Confirm guest-access settings |
| Samba service logs | Correlate SMB activity and guest access |
| Server 1 PCAP | Support SMB2 and HTTP network activity |
| Apache / CubeCart logs | Reconstruct administrative and product-management activity |
| Horilla source code | Identify unsafe dynamic evaluation |
| Filesystem artifact | Confirm controlled command execution |
| Podman metadata | Establish container execution context |
| Authentication history | Check for relevant host-login activity |

### Server 2

| Source | Purpose |
|---|---|
| vsftpd logs | Confirm anonymous login and file downloads |
| Apache / phpMyFAQ logs | Reconstruct login and backup requests |
| Filesystem metadata | Confirm presence of exposed and generated files |
| Application source | Identify missing/insufficient permission enforcement |
| Backup artifact metadata | Support successful configuration-backup creation |

## Evidence Handling

Relevant artifacts were preserved before remediation where practical.

SHA-256 was used to record integrity references for preserved files.

```text
Collect
  ↓
Record metadata
  ↓
Hash artifact
  ↓
Analyze
  ↓
Correlate with other sources
```

## Why Raw Evidence Is Not Published

The objective of this repository is to demonstrate investigation quality without publishing unnecessary sensitive material.

Excluded public artifacts include:

- raw PCAPs that may contain sensitive traffic;
- reusable credentials;
- session cookies or tokens;
- full configuration backups;
- database secrets;
- unnecessary exploit payloads.

For the evidence methodology, see:

- [Evidence Guide](../docs/evidence-guide.md)
- [Attack Timeline](../docs/timeline.md)
- [Remediation and Validation](../docs/remediation-and-validation.md)

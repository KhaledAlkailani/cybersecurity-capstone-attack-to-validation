# KF-001 — Unauthenticated Sensitive SMB Access

**Severity:** High  
**CWE:** CWE-732 — Incorrect Permission Assignment for Critical Resource  
**System:** Server 1 — `192.168.100.250`

## Summary

Server 1 exposed a Samba `public` share that could be accessed without valid SMB credentials.

The issue was not simply that SMB was running. The security impact came from guest access being enabled on a share containing legacy application files and internal operational information.

The share became the starting point for both Server 1 attack paths.

## Discovery

Share enumeration was performed with:

```bash
smbclient -L //192.168.100.250 -N
```

The public share was then accessed without providing credentials:

```bash
smbclient //192.168.100.250/public -N
```

The exposed content included legacy application material under:

```text
IT/old/store/
```

and internal deployment information related to the HR application.

## Defensive Evidence

The finding was supported from several directions:

- Samba configuration confirmed guest access was permitted.
- Samba service logs recorded activity associated with the investigated source.
- Guest activity was associated with the `nobody` account.
- The Server 1 PCAP showed SMB2 session activity and file reads.
- SMB2 traffic supported access to legacy files including `database.php` and `auth.php`.

Useful investigation commands included:

```bash
sudo testparm -s
sudo journalctl -u smbd --no-pager
```

## Root Cause

The root cause was a combination of two problems:

1. Improper SMB access control.
2. Sensitive legacy and operational information stored inside a guest-accessible share.

Guest access alone increased exposure, but the presence of authentication material and deployment information made the issue significantly more useful to an attacker.

## Attack-Path Role

```text
Guest SMB Access
      ↓
Sensitive Files
      ├── Legacy Authentication Material
      └── HR Deployment Information
```

The finding was an **enabler** for later compromise rather than direct code execution.

## Remediation

Before remediation, relevant Samba settings included:

```text
map to guest = Bad User
usershare allow guests = Yes
guest ok = Yes
```

Guest access was disabled:

```text
map to guest = Never
usershare allow guests = No
guest ok = No
```

Sensitive legacy content was also removed from the exposed path after evidence preservation.

## Validation

The original unauthenticated content-access attempt was repeated.

**Result:** PASS

The original guest-access path to the sensitive content could no longer be reproduced.

> Note: Share enumeration and share-content access are separate behaviors. Validation focused on whether unauthenticated users could still access the sensitive share content.

## Framework Mapping

- **MITRE ATT&CK:** T1083 — File and Directory Discovery
- **MITRE D3FEND:** D3-UAP — User Account Permissions

## Takeaway

An open port is not automatically a vulnerability. The security problem was the access policy and the sensitive information exposed through it.

# KF-006 — Anonymous Sensitive FTP Access

**Severity:** High  
**CWE:** CWE-732 — Incorrect Permission Assignment for Critical Resource  
**System:** Server 2 — `192.168.100.107`

## Summary

The FTP service permitted anonymous authentication and exposed internal operational files without requiring a valid user account.

Anonymous FTP is not automatically a vulnerability.

The security issue in this case was that the anonymously accessible directory contained information that was not intended to be public.

## Exposed Content

The FTP root contained content such as:

```text
README.txt
docs/
backup/
```

The backup directory included operational files that later became important to the attack path.

## Evidence

Server-side vsftpd logs recorded successful anonymous authentication from the investigated source address:

```text
192.168.100.113
```

The logs also recorded successful downloads of internal files, including:

- `README.txt`
- deployment notes
- application inventory
- `maintenance-account.txt`

The maintenance credential file was downloaded at approximately:

```text
2026-10-02 08:01:22
```

## Root Cause

The root cause was not simply that anonymous FTP was enabled.

It was the combination of:

- anonymous access;
- operational content stored under the FTP root;
- backup-related material available to unauthenticated users.

Although FTP uploads were disabled, read-only anonymous access was still a confidentiality risk.

## Attack-Path Role

```text
Anonymous FTP
      ↓
Internal Files
      ↓
maintenance-account.txt
      ↓
Valid Application Credential
```

This finding created the initial entry point for the Server 2 attack chain.

## Remediation

Anonymous FTP access was disabled:

```text
anonymous_enable=NO
```

The vsftpd service was restarted after the configuration change.

For future secure file-transfer requirements, authenticated transfer mechanisms should be used instead of anonymously exposing internal content.

## Validation

Anonymous authentication was attempted again.

The server returned:

```text
530 Login incorrect.
```

**Result:** PASS

## Framework Mapping

- **MITRE ATT&CK:** T1083 — File and Directory Discovery
- **MITRE D3FEND:** D3-UAP — User Account Permissions

## Takeaway

A service can be intentionally public without being vulnerable.

It becomes a security issue when its access policy exposes information that should not be public.

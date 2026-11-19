# KF-007 — Plaintext Application Credentials

**Severity:** High  
**CWE:** CWE-312 — Cleartext Storage of Sensitive Information  
**System:** Server 2 — `192.168.100.107`

## Summary

A maintenance file accessible through anonymous FTP contained an active application credential in plaintext.

The affected file was:

```text
/srv/ftp/backup/maintenance-account.txt
```

The public repository intentionally does not include the password value.

## Why This Was Significant

The exposed credential was not historical or expired.

It remained active and reusable against phpMyFAQ.

That turned an information-disclosure issue into valid authenticated application access.

## Evidence

vsftpd logs confirmed that `maintenance-account.txt` was downloaded by the investigated source address.

Host-side examination confirmed that the file existed under the FTP-accessible backup directory.

The file contained a support/testing account and a plaintext password.

Later application logs showed successful phpMyFAQ authentication using the exposed account.

## Attack Path

```text
Anonymous FTP
      ↓
maintenance-account.txt
      ↓
Plaintext Credential
      ↓
Valid phpMyFAQ Login
```

## Root Cause

The root cause was insecure credential handling:

- a valid credential was stored in plaintext;
- the credential was stored in an operational maintenance file;
- the file was placed in a directory exposed through anonymous FTP;
- the associated account remained active.

## Why Deleting the File Was Not Enough

Once a password has been exposed, removing the file that contained it does not make the password secret again.

The remediation therefore required two separate actions:

```text
Remove the exposed file
+
Rotate the exposed credential
```

## Remediation

Before removal, an evidence copy was preserved outside the FTP-accessible path.

The original file was then removed from:

```text
/srv/ftp/backup/
```

The associated application credential was rotated.

## Validation

Two checks were performed:

1. The plaintext credential file was no longer available through the original FTP path.
2. The previously exposed password no longer authenticated to the application.

**Result:** PASS

## Framework Mapping

- **MITRE ATT&CK:** T1552.001 — Credentials In Files
- **MITRE ATT&CK:** T1078 — Valid Accounts, when the exposed credential was used
- **MITRE D3FEND:** D3-CRO — Credential Rotation

## Takeaway

Plaintext credentials are immediately usable.

Unlike a password hash, an attacker does not need an additional recovery step before trying the credential against the target application.

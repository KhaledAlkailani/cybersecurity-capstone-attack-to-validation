# KF-002 — Weak Legacy Password Hashing / Credential Exposure

**Severity:** High  
**CWE:** CWE-916 — Use of Password Hash With Insufficient Computational Effort  
**System:** Server 1 — `192.168.100.250`

## Summary

A legacy authentication component exposed through the SMB share used MD5 for password verification and contained stored administrator authentication material.

The affected file was located under:

```text
/srv/samba/public/IT/old/store/auth.php
```

The important combination was:

```text
Weak Password Storage
+
Exposed Authentication File
=
Offline Credential-Recovery Opportunity
```

## Vulnerable Behavior

The legacy code followed the pattern:

```php
$storedHash = '<redacted>';
$passwordHash = hash('md5', $password);
return hash_equals($storedHash, $passwordHash);
```

MD5 is a fast general-purpose hash function. That makes it unsuitable for password storage because an attacker who obtains the hash can test password candidates at high speed offline.

## Evidence

The SMB investigation established that the legacy authentication file was reachable through the exposed share.

The Server 1 packet capture also supported actual SMB reads of `auth.php`.

Host-side review confirmed the legacy implementation and the MD5-based password verification logic.

Useful evidence commands included:

```bash
sudo grep -nE 'storedHash|md5|authenticateAdmin|username' /srv/samba/public/IT/old/store/auth.php
sudo stat /srv/samba/public/IT/old/store/auth.php
sudo sha256sum /srv/samba/public/IT/old/store/auth.php
```

SHA-256 was used here for **evidence integrity**, not as a replacement password-storage algorithm.

## Attack-Path Role

The exposed MD5 material was attacked offline.

The recovered credential was then successfully used against the CubeCart administrative interface.

```text
SMB Exposure
    ↓
auth.php
    ↓
MD5 Password Material
    ↓
Offline Recovery
    ↓
Valid CubeCart Login
```

Offline recovery is important because it does not depend on repeatedly authenticating to the target application. Controls such as account lockout and online rate limiting do not protect the exposed hash itself.

## Root Cause

The root cause was not only the use of MD5.

It was the combination of:

- weak legacy password storage;
- hard-coded authentication material;
- the legacy file being retained;
- the file being accessible through an unauthenticated share.

## Remediation

The affected legacy files were preserved for evidence and removed from the SMB-accessible path.

The project did **not** claim that CubeCart's active password-storage mechanism itself was MD5. The evidence concerned the exposed legacy authentication component.

For an active modern authentication system, password storage should use a password-specific algorithm such as:

- Argon2id
- bcrypt
- scrypt

## Validation

The original SMB exposure path was checked again after remediation.

**Result:** PASS

The legacy authentication material was no longer available through the original guest-accessible path.

## Framework Mapping

- **MITRE ATT&CK:** T1552.001 — Credentials In Files
- **MITRE ATT&CK:** T1078 — Valid Accounts, when the recovered credential was later used
- **MITRE D3FEND:** D3-FEV — File Eviction

## Takeaway

A hash is not encryption, and a fast hash is not automatically a safe password hash.

The larger lesson was that weak credential material becomes far more dangerous when it is also exposed to unauthenticated users.

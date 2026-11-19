# Findings Summary

| ID | Finding | Severity | Root Cause | Remediation | Validation |
|---|---|:---:|---|---|---|
| KF-001 | Unauthenticated Sensitive SMB Access | High | Guest access enabled on a share containing sensitive operational and legacy content | Disabled guest access and required authenticated access | Anonymous SMB content access could no longer be reproduced |
| KF-002 | Weak Legacy Password Hashing / Credential Exposure | High | Legacy authentication code used MD5 and was exposed through SMB | Preserved evidence and removed the legacy authentication files from the SMB-accessible path | Legacy credential material was no longer available through the original path |
| KF-003 | CubeCart Stored XSS | Medium | Insufficient handling of untrusted product content | Upgraded CubeCart from 6.7.4 to 6.8.3 | The same Stored XSS test no longer executed JavaScript |
| KF-004 | Horilla Default Administrative Credentials | Critical | Default administrative credentials remained valid after deployment | Changed the default admin credential and invalidated the old access path | Previous default credential was rejected |
| KF-005 | Horilla Unsafe Dynamic Evaluation / RCE | Critical | User-controlled `is_active` input was passed to Python `eval()` | Upgraded Horilla from 1.3 to 1.3.1 and removed unsafe dynamic evaluation | Invalid input was rejected and the original code-execution path could not be reproduced |
| KF-006 | Anonymous Sensitive FTP Access | High | Anonymous FTP was enabled for a directory containing internal files | Set `anonymous_enable=NO` and restarted vsftpd | Anonymous login returned `530 Login incorrect` |
| KF-007 | Plaintext Application Credentials | High | A valid application credential was stored in plaintext in an FTP-accessible maintenance file | Preserved evidence, removed the file, and rotated the credential | The file was no longer exposed and the previously exposed password no longer authenticated |
| KF-008 | phpMyFAQ Insufficient Backup Authorization | High | Backup functionality checked authentication but did not sufficiently enforce backup permission | Upgraded phpMyFAQ from 4.0.16 to 4.0.17 with corrected authorization checks | Low-privileged user could still authenticate but could no longer perform the backup operation |

---

## Root-Cause Groups

### Access Control

- KF-001 — SMB guest access
- KF-006 — Anonymous FTP access

### Credential Management

- KF-002 — Weak legacy password storage
- KF-004 — Default administrative credential
- KF-007 — Plaintext active credential

### Application Security

- KF-003 — Stored XSS
- KF-005 — Unsafe dynamic evaluation / RCE

### Authorization

- KF-008 — Privileged backup functionality accessible beyond the intended role

---

## Key Observation

The highest impact did not come from isolated vulnerabilities alone.

It came from the ability to connect them.

> A small exposure can become a major compromise when it reveals the information needed to reach the next weakness.

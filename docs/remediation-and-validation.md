# Remediation and Validation

The project treated remediation and validation as two separate steps.

A change was not considered complete simply because a configuration was edited or a software package was upgraded.

The original attack behavior was repeated after remediation.

```text
Original Behavior
      ↓
Remediation
      ↓
Same Test Repeated
      ↓
Original Security Impact No Longer Reproduced
```

---

## KF-001 — SMB Guest Access

### Before

The Samba configuration allowed unauthenticated guest access to the public share.

```text
map to guest = Bad User
usershare allow guests = Yes
guest ok = Yes
```

### Remediation

```text
map to guest = Never
usershare allow guests = No
guest ok = No
```

### Validation

The original unauthenticated SMB access attempt was repeated.

**Result:** PASS — the original content-access path could no longer be reproduced.

---

## KF-002 — Legacy MD5 Credential Exposure

### Before

Legacy authentication files under the SMB-accessible path exposed MD5-based password verification material.

### Remediation

The affected files were preserved as evidence and removed from the exposed legacy directory.

### Validation

The original SMB path was checked again.

**Result:** PASS — the authentication material was no longer available through the original exposure path.

---

## KF-003 — CubeCart Stored XSS

### Before

CubeCart 6.7.4 allowed the tested product-description content to be stored and later executed in the browser.

### Remediation

CubeCart was upgraded to 6.8.3, which includes the vendor security correction.

### Validation

The same Stored XSS test was submitted again.

**Result:** PASS — JavaScript no longer executed.

---

## KF-004 — Horilla Default Administrative Credential

### Before

The original deployment credential remained valid for the Horilla administrative account.

### Remediation

The default credential was replaced with a unique non-default credential.

### Validation

The previously valid default credential was tested again.

**Result:** PASS — the old credential was rejected.

---

## KF-005 — Horilla Unsafe Dynamic Evaluation / RCE

### Before

Horilla 1.3 used unsafe dynamic evaluation in the affected functionality:

```python
is_active = eval(request.GET.get("is_active"))
```

### Remediation

Horilla was upgraded to 1.3.1.

The patched implementation removed direct `eval()` usage and restricted `is_active` to the expected Boolean values.

### Validation

Unexpected input was submitted to the same functionality.

```text
Invalid value for 'is_active'. Use 'true' or 'false'.
```

**Result:** PASS — the original code-execution path could no longer be reproduced.

---

## KF-006 — Anonymous FTP Access

### Before

```text
anonymous_enable=YES
```

### Remediation

```text
anonymous_enable=NO
```

The vsftpd service was restarted.

### Validation

Anonymous authentication was attempted again.

```text
530 Login incorrect.
```

**Result:** PASS.

---

## KF-007 — Plaintext Credential Exposure

### Before

A valid phpMyFAQ support/test credential was stored in plaintext inside an FTP-accessible maintenance file.

### Remediation

The exposed file was removed after preserving an evidence copy, and the associated credential was rotated.

### Validation

The original file path and the previously exposed password were tested again.

**Result:** PASS — the file was no longer exposed and the old credential no longer authenticated.

---

## KF-008 — phpMyFAQ Backup Authorization

### Before

phpMyFAQ 4.0.16 required authentication for the backup endpoint, but a low-privileged authenticated account could still trigger the privileged backup function.

### Remediation

phpMyFAQ was upgraded to 4.0.17.

The patched behavior enforces the required backup permission rather than relying on authentication alone.

### Validation

The same low-privileged account was used again.

The account could still authenticate normally, but the backup operation was denied.

**Result:** PASS.

---

# Final Validation Status

| Finding | Result |
|---|:---:|
| KF-001 | PASS |
| KF-002 | PASS |
| KF-003 | PASS |
| KF-004 | PASS |
| KF-005 | PASS |
| KF-006 | PASS |
| KF-007 | PASS |
| KF-008 | PASS |

## 8 / 8 Validated

> A patch is not proof. Retesting is proof.

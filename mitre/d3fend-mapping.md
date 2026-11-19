# MITRE D3FEND Mapping

MITRE D3FEND was used to describe defensive actions applied during remediation.

| D3FEND ID | Technique | Used For |
|---|---|---|
| D3-UAP | User Account Permissions | KF-001, KF-006 |
| D3-FEV | File Eviction | KF-002 |
| D3-CDP | Change Default Password | KF-004 |
| D3-CRO | Credential Rotation | KF-007 |
| D3-SU | Software Update | KF-003, KF-005, KF-008 |

## Mapping Logic

### Access-Control Problems

```text
KF-001 SMB Guest Access
KF-006 Anonymous FTP
        ↓
D3-UAP — User Account Permissions
```

### Exposed Legacy Files

```text
KF-002 Legacy Authentication Files
        ↓
D3-FEV — File Eviction
```

### Default Credential

```text
KF-004 Default Horilla Credential
        ↓
D3-CDP — Change Default Password
```

### Exposed Active Credential

```text
KF-007 Plaintext Credential
        ↓
D3-CRO — Credential Rotation
```

### Vulnerable Software

```text
KF-003 CubeCart
KF-005 Horilla
KF-008 phpMyFAQ
        ↓
D3-SU — Software Update
```

## ATT&CK vs D3FEND

A simple way to remember the difference:

```text
ATT&CK  → What the attacker did
D3FEND  → What the defender did about it
```

The project used both because the assessment included both offensive behavior and defensive remediation.

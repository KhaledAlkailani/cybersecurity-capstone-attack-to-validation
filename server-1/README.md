# Server 1 — 192.168.100.250

Server 1 produced the more complex of the two attack stories because a single information-exposure issue created two separate paths.

## Environment

Main services and applications:

- Apache — 80/443
- Samba — 139/445
- OpenLDAP — 389
- MariaDB — 3306
- PostgreSQL — 5432
- Horilla / Gunicorn — 8000
- CubeCart
- Podman-hosted Horilla application stack

## Attack Story

```text
                         ┌── Legacy Authentication Files
                         │          ↓
                         │    Credential Recovery
                         │          ↓
Open SMB Share ──────────┤     CubeCart Admin
                         │          ↓
                         │       Stored XSS
                         │
                         └── HR Deployment Information
                                    ↓
                             Default Admin Login
                                    ↓
                             Horilla Function
                                    ↓
                              Unsafe eval()
                                    ↓
                           Code Execution / RCE
```

The SMB share did **not** directly provide code execution. Its significance was the information it exposed and the additional weaknesses that information made reachable.

## Findings

| ID | Finding | Severity |
|---|---|:---:|
| [KF-001](KF-001-smb-access.md) | Unauthenticated Sensitive SMB Access | High |
| [KF-002](KF-002-legacy-md5.md) | Weak Password Hashing / Legacy Credential Exposure | High |
| [KF-003](KF-003-cubecart-stored-xss.md) | CubeCart Stored XSS | Medium |
| [KF-004](KF-004-horilla-default-credentials.md) | Horilla Default Administrative Credentials | Critical |
| [KF-005](KF-005-horilla-rce.md) | Horilla Unsafe Dynamic Evaluation / RCE | Critical |

## Investigation Sources

- Samba configuration and service logs
- Server 1 packet capture
- SMB2 file-access activity
- Apache / CubeCart logs
- Horilla application source
- Filesystem artifacts
- Podman container metadata
- Authentication history

## Key Lesson

> Information disclosure is often an enabling weakness. The real impact appears when the exposed information can be connected to credentials, applications, and additional vulnerabilities.

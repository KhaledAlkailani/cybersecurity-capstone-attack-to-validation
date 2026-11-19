# Server 2 — 192.168.100.107

Server 2 followed a more linear attack path than Server 1, but it demonstrated how a simple information-exposure issue can lead to valid application access and then to a sensitive authorization failure.

## Environment

Main services and applications:

- vsftpd — 21
- OpenSSH — 22
- Apache — 80
- MariaDB — 3306
- phpMyFAQ

FTP root:

```text
/srv/ftp
```

## Attack Story

```text
Anonymous FTP
      ↓
Sensitive Internal Files
      ↓
maintenance-account.txt
      ↓
Plaintext Valid Credential
      ↓
phpMyFAQ Login
      ↓
Low-Privilege Session
      ↓
Insufficient Backup Authorization
      ↓
Sensitive Configuration Archive
```

## Findings

| ID | Finding | Severity |
|---|---|:---:|
| [KF-006](KF-006-anonymous-ftp.md) | Anonymous Sensitive FTP Access | High |
| [KF-007](KF-007-plaintext-credentials.md) | Plaintext Application Credentials | High |
| [KF-008](KF-008-phpmyfaq-authorization.md) | phpMyFAQ Insufficient Backup Authorization | High |

## Investigation Sources

Server 2 did not have a usable packet capture for the investigated period.

The reconstruction therefore relied on:

- vsftpd logs
- Apache / phpMyFAQ logs
- filesystem artifacts
- file metadata
- preserved evidence copies
- application source
- generated backup artifacts

The absence of a PCAP reduced one corroborating data source, but it did not prevent the attack sequence from being reconstructed from server-side evidence.

## Key Lesson

> Authentication alone is not enough. A valid user should only be able to perform actions allowed by that user's role.

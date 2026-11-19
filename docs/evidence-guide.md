# Evidence Guide

This file describes the main evidence sources used during the defensive investigation and what each source could prove.

The repository intentionally contains summarized and sanitized evidence rather than raw sensitive logs, credentials, or configuration archives.

## Server 1

### Samba Configuration

Used to determine whether guest access was intentionally permitted.

Relevant review areas included:

```text
map to guest
usershare allow guests
guest ok
```

### Samba Logs

Used to support:

- SMB session activity;
- guest-user activity;
- access associated with the investigated source.

### Packet Capture

Server 1 included a packet capture recorded at:

```text
/var/log/evdin/s1.pcap
```

The capture contained approximately 155,921 packets.

It was used to support:

- reconnaissance;
- SMB2 session activity;
- tree connections;
- file-access operations;
- HTTP activity to the Horilla service.

A PCAP can show that network activity occurred, but it does not always prove application-level or host-level impact by itself.

### Apache / CubeCart Logs

Used to support the web sequence around:

- administrative login;
- product-management access;
- later application activity.

Important limitation:

> Web-server logs showing a request do not by themselves prove browser-side JavaScript execution.

The Stored XSS impact was validated through application/browser behavior.

### Horilla Source Code

Source review identified unsafe dynamic evaluation in the affected function.

```python
is_active = eval(request.GET.get("is_active"))
```

This established the technical root cause.

### Filesystem Artifact

A controlled proof file created through the vulnerable Horilla behavior provided host-side evidence that operating-system command execution had occurred inside the application container.

This was stronger evidence of execution than an HTTP `200` response.

### Podman Metadata

Container inspection was used to establish the application execution environment.

The demonstrated shell ran as root **inside the Horilla container**.

No container escape was claimed.

---

## Server 2

### vsftpd Logs

Used to support:

- anonymous login;
- file downloads;
- source address;
- timestamps.

The logs documented retrieval of internal files including the maintenance credential file.

### Apache / phpMyFAQ Logs

Used to reconstruct:

```text
Unauthenticated backup request → 401
Login → 200
Authenticated backup request → 200
```

This sequence helped distinguish authorization failure from authentication bypass.

### Filesystem Evidence

Used to support:

- presence of the exposed maintenance file;
- backup artifact creation;
- timestamps and metadata.

### Application Source

Used to compare the backup endpoint's authentication check with the permission check expected for privileged functionality.

### Backup Artifact

The generated configuration archive provided evidence that the backup operation did more than return a successful HTTP response.

The public repository does not include the actual sensitive archive.

---

## Evidence Integrity

SHA-256 hashing was used when preserving relevant artifacts.

This was done to support evidence integrity:

```text
Artifact at collection time
        ↓
SHA-256 digest
        ↓
Reference value for later integrity checks
```

SHA-256 in this context was not being used for password storage.

## Evidence Principle

> A finding tells you there is a problem. Evidence tells you what actually happened.

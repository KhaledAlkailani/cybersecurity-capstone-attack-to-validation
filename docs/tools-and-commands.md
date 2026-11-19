# Tools and Commands

This file lists representative commands used during the assessment and defensive investigation.

It is not intended as an exploitation playbook. Sensitive credentials and reusable exploitation payloads are intentionally excluded.

## Network and Service Discovery

### Nmap

Used for service discovery and attack-surface identification.

```bash
nmap <target>
```

Purpose:

- identify listening services;
- guide service-specific enumeration;
- establish the initial network view.

## SMB

### List SMB Shares

```bash
smbclient -L //192.168.100.250 -N
```

### Access the Public Share

```bash
smbclient //192.168.100.250/public -N
```

### Validate Samba Configuration

```bash
sudo testparm -s
```

### Review Samba Service Logs

```bash
sudo journalctl -u smbd --no-pager
```

## File and Evidence Review

### Search Source Files

```bash
grep -nE 'storedHash|md5|authenticateAdmin|username' /srv/samba/public/IT/old/store/auth.php
```

### Review File Metadata

```bash
stat <file>
```

### Record Evidence Hash

```bash
sha256sum <file>
```

Purpose:

- preserve evidence metadata;
- record an integrity reference;
- correlate filesystem artifacts with the incident timeline.

## Packet Analysis

### TShark

Representative workflow:

```bash
tshark -r /var/log/evdin/s1.pcap
```

Used to inspect:

- SMB2 activity;
- HTTP traffic;
- source/destination relationships;
- request timing.

Filtering was applied during analysis to isolate relevant conversations.

## Horilla / Container Review

### Search for Unsafe Evaluation

```bash
grep -Rni "eval(request.GET.get" /app/project/
```

### Review Function Context

```bash
grep -n -A 15 -B 10 "def project_bulk_archive" /app/project/views.py
```

### Enter Application Container

```bash
podman exec -it horilla-hr-server-1 /bin/bash
```

### Inspect Container Metadata

```bash
podman inspect horilla-hr-server-1
```

## FTP

The FTP client was used to validate anonymous access before remediation.

After remediation, the same authentication attempt was repeated to confirm that anonymous access was rejected.

## Web Logs

### Apache Log Search

Representative pattern:

```bash
grep '<search-term>' /var/log/apache2/<log-file>
```

For compressed historical logs:

```bash
zgrep -E '<pattern>' /var/log/apache2/<log-file>.gz
```

Purpose:

- identify login activity;
- correlate endpoint access;
- reconstruct request order;
- validate the source address and timestamps.

## Important Principle

Commands were selected to answer specific investigative questions.

The project did not use tools simply to increase the number of technologies listed.

> Every command should help prove or disprove something.

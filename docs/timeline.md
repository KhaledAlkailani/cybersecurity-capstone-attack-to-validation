# Attack Timeline

The following timeline summarizes the main confirmed events used to reconstruct the two attack stories.

Times are kept in the local timezone recorded on each server.

---

## Server 2 — 02 October 2026

**Timezone:** America/New_York (UTC-04)

| Time | Event |
|---|---|
| 07:53:06 | Network reconnaissance observed |
| 07:59:06 | Anonymous FTP login |
| 07:59:19 | `README.txt` downloaded |
| 07:59:42 | Deployment notes downloaded |
| 08:01:15 | Application inventory downloaded |
| 08:01:22 | `maintenance-account.txt` downloaded |
| 08:23:13 | phpMyFAQ accessed |
| 08:39:37 | Application/API enumeration activity began |
| 09:04:29 | `/api/setup/backup` discovered |
| 09:27:07 | phpMyFAQ authentication succeeded |
| 09:30:15 | Backup request succeeded |
| 09:30:15 | Configuration backup created |
| 09:44:50 | Configuration backup retrieved |

### Condensed Story

```text
Reconnaissance
→ Anonymous FTP
→ Credential File Download
→ phpMyFAQ Login
→ Backup Endpoint
→ Configuration Backup
```

---

## Server 1 — 07 October 2026

**Timezone:** Asia/Amman (UTC+03)

| Time | Event |
|---|---|
| 21:24:59 | Network reconnaissance observed |
| 21:25:55 | SMB session established |
| 21:25:57 | Public SMB share enumerated |
| 21:26:27 | `IT/old/store` accessed |
| 21:26:44 | `database.php` accessed |
| 21:26:48 | `auth.php` accessed |
| 21:34:57 | CubeCart admin login succeeded |
| 21:35:29 | Product edit functionality accessed |
| After 21:35:29 | Stored XSS demonstrated |
| 21:37:00 | Horilla login page accessed |
| 21:51:31 | Horilla authentication succeeded |
| 21:56:53 | Project functionality accessed |
| 22:00:46 | `is_active` manipulation began |
| 22:03:37 | Controlled OS command execution demonstrated |
| 22:06:32 | Reverse shell established inside the Horilla container |

### Condensed Story

```text
Reconnaissance
→ SMB Exposure
→ Sensitive Files
→ CubeCart / Horilla Access
→ Application Exploitation
→ Code Execution
```

---

## Timeline Correlation Notes

The two servers did not use the same timezone.

For that reason, timestamps were interpreted together with their recorded UTC offsets rather than compared as if both systems used the same local clock.

The timeline was reconstructed from multiple evidence sources including service logs, application logs, filesystem metadata, packet capture where available, and preserved artifacts.

# MITRE ATT&CK Mapping

MITRE ATT&CK was used to describe **observed adversary behavior**.

The mapping was based on what the evidence showed the attacker doing, rather than assigning techniques simply because a vulnerability existed.

## Server 1

| Observed Activity | ATT&CK Technique | ID |
|---|---|---|
| Network and service enumeration | Network Service Discovery | T1046 |
| Browsing and retrieving files through SMB | File and Directory Discovery | T1083 |
| Authentication material found inside exposed files | Credentials In Files | T1552.001 |
| Recovered credential used against CubeCart | Valid Accounts | T1078 |
| Default Horilla administrator credential used | Default Accounts | T1078.001 |
| CubeCart / Horilla application weakness exploitation | Exploit Public-Facing Application | T1190 |
| Command execution through vulnerable Horilla behavior | Command and Scripting Interpreter | T1059 |

### Condensed Server 1 Sequence

```text
T1046
  ↓
T1083
  ↓
T1552.001
  ↓
T1078 / T1078.001
  ↓
T1190
  ↓
T1059
```

## Server 2

| Observed Activity | ATT&CK Technique | ID |
|---|---|---|
| Network and service enumeration | Network Service Discovery | T1046 |
| Anonymous FTP browsing and file retrieval | File and Directory Discovery | T1083 |
| Plaintext credential obtained from maintenance file | Credentials In Files | T1552.001 |
| Exposed account used against phpMyFAQ | Valid Accounts | T1078 |
| Configuration backup data obtained | Data from Local System | T1005 |

### Condensed Server 2 Sequence

```text
T1046
  ↓
T1083
  ↓
T1552.001
  ↓
T1078
  ↓
T1005
```

## Why ATT&CK Was Used

CWE and ATT&CK answer different questions.

```text
CWE     → What kind of software/security weakness existed?
ATT&CK  → What adversary behavior was observed?
```

For example:

- CWE-312 describes cleartext storage of sensitive information.
- T1552.001 describes the adversary behavior of obtaining credentials from files.

Both can describe the same incident from different perspectives.

## Mapping Principle

> Map behavior supported by evidence, not every technique that might theoretically be possible.

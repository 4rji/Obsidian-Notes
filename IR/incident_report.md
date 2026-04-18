# Incident Report – Unauthorized Access and Data Exfiltration

## Summary
An attacker gained unauthorized access to the network through brute force authentication and successfully logged in as the Administrator. The attacker laterally moved across internal systems, deployed tools, accessed the Microsoft Exchange server, exported a user mailbox to a PST file, and cleared Windows event logs to evade detection.

---

## Timeline of Events

| Time | Event |
|------|------|
| External | Brute force attempts from 211.56.98.146 |
| 08:46 | Successful SSH login as **administrator** from **10.10.254.1** |
| 08:55 | Malware/tool **procduwp.exe** created in `C:\Users\Public\` |
| 08:55–08:59 | PowerShell executed to access Exchange |
| 08:59 | Mailbox exported to PST file |
| 09:02 | Windows Security logs cleared (Event ID 1102) |

---

## Evidence Collected

### 1. Initial Access
OpenSSH log:
```
Accepted password for administrator from 10.10.254.1 port 42526 ssh2
```
Indicates successful login using Administrator credentials.

---

### 2. Lateral Movement
SMB connection observed:
```
IpAddress: 172.16.5.102
IpPort: 445
ProcessId: 0x4 (SYSTEM)
```
Indicates remote SMB access, likely PsExec or remote service execution.

---

### 3. Malware / Tool Deployment
Files created during attack window:
```
C:\Users\Public\procduwp.exe
C:\Users\Public\out
```
Public directory is commonly used by attackers to store tools and output files.

---

### 4. PowerShell Activity (Exchange Access & Mail Export)
PowerShell command executed:
```
New-PSSession -ConfigurationName Microsoft.Exchange
New-MailboxExportRequest -Mailbox jefferson.livingston -FilePath \\mailserver\C$\backup.pst
```

This shows the attacker:
- Authenticated to Exchange as Administrator
- Exported mailbox **jefferson.livingston**
- Saved email archive to **backup.pst**

This is **email data exfiltration**.

---

### 5. Defense Evasion
Windows Event Log cleared:
```
Event ID 1102 – Security log cleared
User: Administrator
Time: 09:02 AM
```
This indicates the attacker attempted to remove forensic evidence.

---

## MITRE ATT&CK Mapping

| Stage | Technique |
|------|----------|
| Initial Access | T1110 – Brute Force |
| Persistence | T1543 – Create or Modify System Process |
| Lateral Movement | T1021.002 – SMB |
| Execution | T1059 – PowerShell |
| Collection | T1114 – Email Collection |
| Exfiltration | T1048 – Exfiltration Over Network |
| Defense Evasion | T1070 – Clear Windows Event Logs |

---

## Indicators of Compromise (IOCs)

| Type | Value |
|------|------|
| External IP | 211.56.98.146 |
| Internal IP | 10.10.254.1 |
| Internal IP | 172.16.5.102 |
| File | C:\Users\Public\procduwp.exe |
| File | C:\Users\Public\out |
| File | C:\backup.pst |

---

## Conclusion
The attacker successfully compromised the Administrator account, moved laterally across internal systems, accessed the Exchange server, exported sensitive email data, and attempted to cover tracks by clearing event logs. This incident resulted in unauthorized access and potential data exfiltration of corporate email.

---

## Recommended Actions
- Reset Administrator credentials
- Disable SSH access or restrict by IP
- Review all administrator group members
- Block external IP 211.56.98.146
- Check for persistence (services, scheduled tasks)
- Search for additional tools in:
  - `C:\Users\Public\`
  - `C:\ProgramData\`
  - `C:\Windows\Temp\`
- Review outbound traffic for PST file exfiltration

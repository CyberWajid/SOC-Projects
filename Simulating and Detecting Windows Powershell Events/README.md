# 🖥️ Windows PowerShell Attack Simulation & Detection 🔐

## soc home lab project | Splunk SIEM | Windows Logging | MITRE ATTACK T1059.001
---
## 📌 Project Summary
This project demonstrate how to simulate suspicious Powershell activity on a windows and detect it using both Event Viewer and Splunk SIEM. 
Powershell is commonly abused by attackers for execution, persistent and lateral movement. This lab enable logging, executes test commands and analyze the generated events to identify malicious behavior.

---
## 🏗️ Lab Architecture
+----------------------+
| Windows 10 Target |
| (PowerShell Events) |
+----------------------+
|
Event Logs (Security + PowerShell)
|
v
+----------------------------+
| Splunk Universal Forwarder |
+----------------------------+
|
v
+----------------------+
| Splunk SIEM |
| localhost:8000 |
+----------------------+

---
## 🛠️ Tools Used
| Tool | Purpose |
|------|----------|
| Windows 11 | Target machine |
| PowerShell | Attack simulation |
| Event Viewer | Log analysis |
| Splunk Enterprise |	Detection & monitoring |
| Splunk Universal Forwarder | Log collection |

---
## ⚙️ Configuration (Enable PowerShell Logging)
### Step 1: Open Group Policy Editor

Run:

```bash
gpedit.msc
```

### Step 2: Enable Script Block Logging

Navigate to:
```bash
Computer Configuration → Administrative Templates → Windows Components → Windows PowerShell
```
Enable:

✅ Turn on PowerShell Script Block Logging

### Step 3: Enable Module Logging

Enable:

✅ Turn on Module Logging
Add module: *

### Step 4: Enable Process Creation Logging

Go to:
```bash
Computer Configuration → Windows Settings → Security Settings → Advanced Audit Policy Configuration → Detailed Tracking
```
Enable:

✅ Audit Process Creation

### Step 5: Enable Command Line Logging

Run in PowerShell (Admin):
```bash
reg add HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Audit /v ProcessCreationIncludeCmdLine_Enabled /t REG_DWORD /d 1 /f
```
---
### 💣 Attack Simulation (PowerShell Commands)
Run these commands in PowerShell:
```bash
whoami
```
```bash
net user
```
```bash
 ipconfig
```
```bash
Get-Process
```
---
### Simulate Suspicious Behavior:
powershell -enc <base64 encoded command>

OR

Invoke-Expression "whoami"
---
### 🔍 Detection — Event Viewer

Open:
```bash
Event Viewer → Windows Logs → Security
```
---
### Key Events:
| Event ID | Description |
|----------|-------------|
| 4688 | Process creation |
| 4624 | Successful logon |
| 4625 | Failed logon |
---
### PowerShell Logs:
Applications and Services Logs → Microsoft → Windows → PowerShell → Operational
| Event ID | Description |
|----------|-------------|
| 4103 | Module logging |
| 4104 | Script block logging |
---
### 🚨 Detection — Splunk (SPL Queries)
#### 🔹 PowerShell Execution Detection
```bash
index=windows EventCode=4104
```

### 🔹 Suspicious Encoded Commands
```bash
index=windows EventCode=4104 "enc"
```
### 🔹 Process Creation (PowerShell)
```bash
index=windows EventCode=4688 process_name=powershell.exe
```

### 🔹 High Volume Activity
```bash
index=windows EventCode=4104
| stats count by user
| sort -count
```
# 📸 Screenshots
PowerShell commands executed
Event Viewer logs (Event ID 4104, 4688)
Splunk search results
Detection queries

### 📊 Key Findings
PowerShell activity successfully logged after enabling Group Policy
Script Block Logging (Event ID 4104) captured executed commands
Process Creation logs (4688) confirmed PowerShell execution
Splunk successfully ingested and detected PowerShell events
Encoded and suspicious commands were identifiable

### 🧠 MITRE ATT&CK Mapping
| Technique |	Description |
|-----------|-------------|
| T1059.001 |	PowerShell execution |

### 🛡️ Mitigation Recommendations
| Control |	Description |
|---------|-------------|
| Enable Logging |	Always enable PowerShell logging |
| Restrict PowerShell |	Limit usage via policies |
| Endpoint Monitoring |	Use SIEM for detection |
| Least Privilege | Avoid admin rights |
| Alerting |	Create alerts for Event ID 4104 |

### 💼 Skills Demonstrated
Windows logging configuration (Group Policy)
PowerShell attack simulation
Event Viewer analysis
Splunk log ingestion & detection
SPL query development
MITRE ATT&CK mapping
SOC investigation workflow

### 📚 Lessons Learned

PowerShell is a powerful administrative tool but also a common attack vector. Proper logging and monitoring significantly improve detection capabilities. Splunk enhances visibility by correlating events and enabling real-time detection of suspicious activity.

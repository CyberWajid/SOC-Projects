# Incident Report — Suspicious PowerShell Activity Detection
**Date:** April 2026

**Severity:** MEDIUM

**Status:** Resolved
---
## 1. Incident Summary
Suspicious PowerShell activity was identified on a Windows 11 system after enabling advanced logging through Group Policy. The activity included execution of system reconnaissance commands and potentially obfuscated PowerShell execution techniques.

Windows Event Logs were collected using Event Viewer, exported as .evtx files, and analyzed in Splunk SIEM. Script Block Logging (Event ID 4104) and Process Creation Logs (Event ID 4688) provided visibility into executed commands.

The behavior is consistent with early-stage attacker activity such as discovery and execution.

## 2. Timeline
| Time (UTC) | Event |
|------------|-------|
| 20:10:05 |	PowerShell process started (Event ID 4688) |
| 20:10:07 |	Script block execution logged (Event ID 4104) |
| 20:10:10 |	Multiple reconnaissance commands executed |
| 20:10:12 |	Encoded/obfuscated command observed |
| 20:10:15 |	Analysis performed in Splunk |

## 3. Attack Details
**Attack Type:** Suspicious PowerShell Execution

**MITRE Technique:** T1059.001 — PowerShell

**Host:** Windows 11

**User:** Local User

**Process:** powershell.exe

**Execution Method:** Interactive / Encoded / Obfuscated

## 4. Detection Method (Splunk Analysis)
index=windows EventCode=4104

### Detection Logic:
Analyzed PowerShell Script Block Logging (Event ID 4104)
Identified execution of commands via PowerShell
Correlated multiple events from same user
Highlighted abnormal or repeated activity

## 5. Evidence
### PowerShell Script Block Logs (Event ID 4104)
| Field |	Value |	Meaning |
| Event ID |	4104 | Script execution |
| Command	| whoami |	User discovery |
| Command	| ipconfig |	Network enumeration |
| Command	| Get-Process |	Process discovery |

## Key Indicators
Execution of multiple system discovery commands
Presence of encoded/obfuscated PowerShell commands
Repeated PowerShell activity in a short timeframe
Use of administrative tools for enumeration

## 6. Analyst Assessment

The observed activity is indicative of suspicious PowerShell usage commonly associated with attacker reconnaissance techniques. Commands such as whoami, ipconfig, and Get-Process are frequently used during the discovery phase of an attack.

The presence of encoded commands suggests an attempt to evade detection, which is a known attacker technique.

No persistence mechanisms or lateral movement activity were observed during this investigation.

Verdict: TRUE POSITIVE (Suspicious Activity)

## 7. Recommendations
**1.Enable PowerShell Logging**
Ensure Script Block Logging (Event ID 4104) is always enabled

**2.Monitor Encoded Commands**
Create alerts for -enc and obfuscation patterns

**3.Restrict PowerShell Usage**
Use Group Policy or AppLocker to limit execution

**4.Centralized Monitoring**
Integrate logs into SIEM for continuous monitoring

**5.Least Privilege Principle**
Limit administrative access to reduce misuse

## 8. Lessons Learned

PowerShell provides powerful administrative capabilities but is frequently abused by attackers. Enabling detailed logging significantly improves visibility into system activity.

Even without real-time log forwarding, offline analysis using exported .evtx logs in Splunk can effectively detect suspicious behavior and support incident investigation.

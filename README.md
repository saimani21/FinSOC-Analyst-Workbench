
# FinSOC Analyst Workbench  
## SIEM Alert Investigation, Threat Hunting & Incident Reporting Platform

![Python](https://img.shields.io/badge/Python-3.10%2B-blue)
![Streamlit](https://img.shields.io/badge/Streamlit-Dashboard-red)
![SQLite](https://img.shields.io/badge/Database-SQLite-lightgrey)
![MITRE ATT&CK](https://img.shields.io/badge/MITRE-ATT%26CK-orange)
![SOC](https://img.shields.io/badge/Domain-SOC%20%7C%20IR%20%7C%20Threat%20Hunting-darkgreen)

---

## Overview

**FinSOC Analyst Workbench** is a SOC-style security investigation platform designed for alert monitoring, log analysis, threat hunting, incident triage, root-cause analysis, MITRE ATT&CK mapping, and incident report generation.

The platform ingests security logs from multiple sources including Linux authentication logs, Windows/Sysmon events, Suricata IDS alerts, WAF-style web attack logs, and DLP-style data movement events. These logs are normalized into a unified alert model, processed using rule-based detection logic, mapped to MITRE ATT&CK techniques, and displayed through an interactive Streamlit dashboard.

The project simulates real-world Security Operations Center workflows using public security datasets, synthetic enterprise-style WAF/DLP logs, and controlled local lab-generated events.

---

## Problem Statement

Security Operations Center analysts receive alerts from different tools such as SIEM, IDS, endpoint logging, WAF, DLP, and network monitoring systems. These alerts usually come in different formats and require manual investigation, triage, correlation, root-cause analysis, and reporting.

This project solves that problem by providing a single analyst workbench where different security logs are collected, normalized, detected, investigated, mapped to MITRE ATT&CK, and converted into structured incident reports.

---

## Objectives

The objective of this project is to demonstrate a complete SOC analyst workflow:

- Ingest security logs from multiple sources
- Normalize different log formats into a common alert schema
- Detect suspicious activity using rule-based logic
- Map alerts to MITRE ATT&CK techniques
- Perform alert triage and investigation
- Search and filter alerts for threat hunting
- Track incident status and analyst notes
- Generate PDF incident investigation reports
- Simulate SIEM, WAF, DLP, IDS, and endpoint alert workflows safely

---

## Key Features

### Multi-Source Log Ingestion

The platform supports multiple security log sources:

- Linux authentication logs
- Windows/Sysmon security events
- Suricata IDS `eve.json` alerts
- WAF-style web attack logs
- DLP-style sensitive data movement events
- MITRE ATT&CK mapping data

Input files:

```text
data/
├── linux_auth.log
├── windows_sysmon.json
├── suricata_eve.json
├── waf_events.json
├── dlp_events.csv
└── mitre_mapping.csv
````

---

### Unified Alert Normalization

All log sources are converted into a common schema for easier investigation and correlation.

Normalized alert schema:

```text
alert_id
timestamp
source_type
host
username
src_ip
dst_ip
event_type
raw_message
severity
mitre_technique_id
mitre_technique_name
mitre_tactic
status
recommendation
```

This allows alerts from authentication logs, endpoint logs, IDS logs, WAF logs, and DLP events to be analyzed together in a single dashboard.

---

### Rule-Based Detection Engine

The detection engine uses YAML-based rules inspired by Sigma-style detection logic.

Example rule:

```yaml
title: Multiple Failed SSH Logins
source: linux_auth
description: Detects repeated failed SSH login attempts from the same source IP.
severity: high
condition:
  event_type: failed_login
  threshold: 5
  timeframe_minutes: 10
mitre:
  tactic: Credential Access
  technique_id: T1110
  technique_name: Brute Force
recommendation: Investigate the source IP, block repeated offenders, verify account activity, and enforce MFA.
```

---

## Detection Use Cases

| Rule ID  | Detection Name                           | Log Source      | Severity    | MITRE Mapping                             |
| -------- | ---------------------------------------- | --------------- | ----------- | ----------------------------------------- |
| AUTH-001 | Multiple Failed SSH Logins               | Linux Auth Logs | High        | T1110 - Brute Force                       |
| AUTH-002 | Successful Login After Failed Attempts   | Linux Auth Logs | Critical    | T1078 - Valid Accounts                    |
| WIN-001  | Suspicious PowerShell Execution          | Windows/Sysmon  | High        | T1059.001 - PowerShell                    |
| NET-001  | Port Scan Activity                       | Suricata IDS    | Medium/High | T1046 - Network Service Discovery         |
| WAF-001  | SQL Injection Attempt                    | WAF Logs        | High        | T1190 - Exploit Public-Facing Application |
| WAF-002  | Cross-Site Scripting Attempt             | WAF Logs        | Medium      | T1190 - Exploit Public-Facing Application |
| DLP-001  | Sensitive File Sent Externally           | DLP Events      | High        | T1041 - Exfiltration Over C2 Channel      |
| DLP-002  | Large File Upload to Unknown Destination | DLP Events      | Medium/High | T1567 - Exfiltration Over Web Service     |

---

## Dashboard Capabilities

The Streamlit dashboard provides a complete SOC analyst view.

Dashboard metrics include:

* Total alerts
* Critical alerts
* High severity alerts
* Open incidents
* Alerts by source type
* Alerts by severity
* Top source IP addresses
* Top affected hosts
* Most frequent MITRE techniques
* Incident status distribution

Alert table fields:

```text
Alert ID
Timestamp
Severity
Source Type
Host
Username
Source IP
Destination IP
Event Type
MITRE Technique
Status
Action
```

---

## Alert Investigation View

Each alert has a dedicated investigation view containing:

* Alert summary
* Severity classification
* Source log type
* Affected host
* User involved
* Source IP and destination IP
* Event timeline
* Related logs
* Raw evidence
* MITRE ATT&CK mapping
* Possible root cause
* Security impact
* Recommended remediation
* Analyst notes
* Investigation status

Supported investigation statuses:

```text
Open
Investigating
Escalated
Closed
False Positive
```

---

## Threat Hunting Module

The threat hunting module allows analysts to search, filter, and investigate suspicious activity across all normalized events.

Supported filters:

* Source IP
* Destination IP
* Username
* Hostname
* Severity
* Event type
* MITRE technique
* Log source
* Time range
* Incident status

Example threat-hunting questions supported by the platform:

* Which source IP generated the highest number of failed login attempts?
* Was there a successful login after repeated authentication failures?
* Which hosts generated high-severity alerts?
* Are there repeated web attack attempts from the same IP?
* Are there suspicious PowerShell commands in Windows/Sysmon logs?
* Are sensitive files being transferred externally?
* Which MITRE ATT&CK techniques are most frequently observed?

---

## PDF Incident Report Generation

The platform generates analyst-style PDF incident reports.

Each report includes:

1. Incident title
2. Incident severity
3. Detection source
4. Affected asset
5. User involved
6. Source and destination IP
7. Event timeline
8. Evidence logs
9. Indicators of compromise
10. MITRE ATT&CK mapping
11. Root-cause analysis
12. Security impact
13. Recommended remediation
14. Analyst notes
15. Closure status

Sample report path:

```text
reports/sample_incident_report.pdf
```

---

## Architecture

```text
                 +----------------------+
                 |      Input Logs      |
                 |----------------------|
                 | Linux auth.log       |
                 | Windows Sysmon JSON  |
                 | Suricata eve.json    |
                 | WAF events           |
                 | DLP events           |
                 +----------+-----------+
                            |
                            v
                 +----------------------+
                 |   Parser Modules     |
                 |----------------------|
                 | Auth Parser          |
                 | Sysmon Parser        |
                 | Suricata Parser      |
                 | WAF Parser           |
                 | DLP Parser           |
                 +----------+-----------+
                            |
                            v
                 +----------------------+
                 |   Normalization      |
                 |----------------------|
                 | Unified Alert Schema |
                 +----------+-----------+
                            |
                            v
                 +----------------------+
                 |  Detection Engine    |
                 |----------------------|
                 | YAML Rules           |
                 | MITRE Mapping        |
                 | Severity Scoring     |
                 +----------+-----------+
                            |
                            v
                 +----------------------+
                 |    Alert Database    |
                 |----------------------|
                 | SQLite               |
                 +----------+-----------+
                            |
                            v
       +--------------------+--------------------+
       |                    |                    |
       v                    v                    v
+--------------+    +----------------+    +----------------+
| SOC Dashboard|    | Threat Hunting |    | PDF Reporting  |
+--------------+    +----------------+    +----------------+
```

---

## Project Structure

```text
finsoc-analyst-workbench/
│
├── app.py
├── requirements.txt
├── README.md
│
├── data/
│   ├── linux_auth.log
│   ├── windows_sysmon.json
│   ├── suricata_eve.json
│   ├── waf_events.json
│   ├── dlp_events.csv
│   └── mitre_mapping.csv
│
├── rules/
│   ├── auth_rules.yaml
│   ├── sysmon_rules.yaml
│   ├── suricata_rules.yaml
│   ├── waf_rules.yaml
│   └── dlp_rules.yaml
│
├── src/
│   ├── ingest.py
│   ├── normalize.py
│   ├── detect.py
│   ├── database.py
│   ├── mitre_mapper.py
│   ├── hunting.py
│   ├── report_generator.py
│   └── utils.py
│
├── reports/
│   └── sample_incident_report.pdf
│
├── screenshots/
│   ├── dashboard.png
│   ├── alert_triage.png
│   ├── investigation_view.png
│   ├── threat_hunting.png
│   └── incident_report.png
│
└── docs/
    ├── architecture.md
    ├── detection_rules.md
    └── sample_investigation.md
```

---

## Data Sources

This project uses a hybrid data strategy.

| Data Type                 | Source Type                          | Purpose                                            |
| ------------------------- | ------------------------------------ | -------------------------------------------------- |
| Linux authentication logs | Public dataset / local lab           | SSH brute-force and login investigation            |
| Windows/Sysmon events     | Public attack datasets               | Endpoint activity and suspicious process detection |
| Suricata IDS alerts       | Public sample `eve.json` / local lab | Network alert analysis                             |
| WAF logs                  | Synthetic enterprise-style logs      | SQL injection, XSS, and web attack detection       |
| DLP events                | Synthetic CSV                        | Sensitive data transfer simulation                 |
| MITRE mapping             | Manually created mapping             | ATT&CK technique classification                    |

The project does not use private enterprise data, leaked data, dark web data, or unauthorized live system logs.

---

## Sample Normalized Alert

```json
{
  "alert_id": "AUTH-001-20260531-0001",
  "timestamp": "2026-05-31 10:15:44",
  "source_type": "linux_auth",
  "host": "ubuntu-soc-lab",
  "username": "admin",
  "src_ip": "192.168.1.45",
  "dst_ip": "192.168.1.10",
  "event_type": "multiple_failed_ssh_logins",
  "raw_message": "Failed password for admin from 192.168.1.45 port 52214 ssh2",
  "severity": "high",
  "mitre_technique_id": "T1110",
  "mitre_technique_name": "Brute Force",
  "mitre_tactic": "Credential Access",
  "status": "Open",
  "recommendation": "Block repeated source IP, review account activity, and enforce MFA."
}
```

---

## Sample WAF Event

```json
{
  "timestamp": "2026-05-31 11:05:21",
  "src_ip": "192.168.1.88",
  "host": "web-app-01",
  "method": "GET",
  "url": "/login.php?id=1' OR '1'='1",
  "status_code": 403,
  "user_agent": "Mozilla/5.0",
  "rule_triggered": "SQL Injection Pattern",
  "severity": "high"
}
```

---

## Sample DLP Event

```csv
timestamp,user,host,action,file_name,file_type,destination,size_mb,sensitive_type
2026-05-31 10:20:11,rahul,FIN-PC-01,email_attachment,customer_accounts.xlsx,xlsx,external_email,18,PII
2026-05-31 10:25:44,neha,HR-PC-02,usb_copy,salary_data.pdf,pdf,usb_device,42,Confidential
2026-05-31 10:31:07,admin,FIN-SRV-01,cloud_upload,settlement_report.csv,csv,unknown_cloud,55,Financial
```

---

## Example Incident Scenario

### SSH Brute Force Followed by Successful Login

The platform detects repeated failed SSH login attempts from the same source IP. After multiple failed attempts, a successful login is observed from the same IP address.

The incident is classified as critical because it may indicate credential compromise.

MITRE ATT&CK mapping:

| Technique ID | Technique Name |
| ------------ | -------------- |
| T1110        | Brute Force    |
| T1078        | Valid Accounts |

Investigation output:

* Multiple failed login attempts detected
* Same source IP observed repeatedly
* Successful login occurred after failures
* User account identified
* Affected host identified
* Root cause marked as possible weak or compromised credentials
* Recommended remediation generated

Recommended actions:

1. Reset the affected user password.
2. Block the suspicious source IP.
3. Review successful login activity.
4. Check for privilege escalation or lateral movement.
5. Enable MFA for remote access.
6. Monitor the affected account for further suspicious activity.

---

## Installation

### Clone the repository

```bash
git clone https://github.com/YOUR_USERNAME/finsoc-analyst-workbench.git
cd finsoc-analyst-workbench
```

### Create virtual environment

```bash
python -m venv venv
```

Activate virtual environment:

Windows:

```bash
venv\Scripts\activate
```

Linux/macOS:

```bash
source venv/bin/activate
```

### Install dependencies

```bash
pip install -r requirements.txt
```

---

## Requirements

```text
streamlit
pandas
pyyaml
plotly
fpdf2
python-dateutil
```

Optional dependencies:

```text
sqlalchemy
evtx
```

---

## How to Run

Place the required log files inside the `data/` directory.

```text
data/linux_auth.log
data/windows_sysmon.json
data/suricata_eve.json
data/waf_events.json
data/dlp_events.csv
data/mitre_mapping.csv
```

Run the application:

```bash
streamlit run app.py
```

Open the local Streamlit URL:

```text
http://localhost:8501
```

---

## Dashboard Pages

The application contains the following pages:

### Overview Dashboard

Displays alert statistics, severity distribution, top source IPs, affected hosts, and MITRE technique distribution.

### Alert Triage

Displays detected alerts with severity, status, source, host, user, IP address, and MITRE mapping.

### Investigation View

Allows analysts to inspect an alert, review related evidence, identify root cause, add notes, and update investigation status.

### Threat Hunting

Provides filtering and searching across all normalized events.

### Incident Reports

Generates PDF incident reports for selected alerts and incidents.

---

## Screenshots

```markdown
## Dashboard Preview

![Dashboard](screenshots/dashboard.png)

## Alert Triage

![Alert Triage](screenshots/alert_triage.png)

## Investigation View

![Investigation](screenshots/investigation_view.png)

## Threat Hunting

![Threat Hunting](screenshots/threat_hunting.png)

## Incident Report

![Incident Report](screenshots/incident_report.png)
```

---

## MITRE ATT&CK Mapping

| Activity                    | MITRE Technique ID | Technique Name                    | Tactic                                                                |
| --------------------------- | ------------------ | --------------------------------- | --------------------------------------------------------------------- |
| SSH brute force             | T1110              | Brute Force                       | Credential Access                                                     |
| Valid login after failures  | T1078              | Valid Accounts                    | Initial Access / Persistence / Privilege Escalation / Defense Evasion |
| Suspicious PowerShell       | T1059.001          | PowerShell                        | Execution                                                             |
| Port scanning               | T1046              | Network Service Discovery         | Discovery                                                             |
| SQL injection / web exploit | T1190              | Exploit Public-Facing Application | Initial Access                                                        |
| Sensitive data transfer     | T1041              | Exfiltration Over C2 Channel      | Exfiltration                                                          |
| File upload abuse           | T1105              | Ingress Tool Transfer             | Command and Control                                                   |
| Suspicious external upload  | T1567              | Exfiltration Over Web Service     | Exfiltration                                                          |

---

## Severity Scoring

| Condition                                  | Severity    |
| ------------------------------------------ | ----------- |
| Successful login after brute-force pattern | Critical    |
| Multiple failed login attempts             | High        |
| Suspicious PowerShell command execution    | High        |
| SQL injection attempt                      | High        |
| Port scanning activity                     | Medium/High |
| Cross-site scripting attempt               | Medium      |
| Sensitive file sent externally             | High        |
| Large upload to unknown destination        | Medium/High |

---

## Skills Demonstrated

This project demonstrates:

* SOC alert triage
* SIEM-style log analysis
* Security event normalization
* Incident investigation
* Root-cause analysis
* Threat hunting
* Detection engineering basics
* MITRE ATT&CK mapping
* WAF alert simulation
* DLP alert simulation
* Digital forensic timeline analysis
* Security reporting
* Python automation
* Streamlit dashboard development
* SQLite-based alert storage
* YAML-based detection rules

---

## Limitations

This project is a local SOC simulation and learning platform. It is not a replacement for an enterprise SIEM, EDR, SOAR, DLP, or WAF solution.

Current limitations:

* Detection logic is rule-based.
* WAF and DLP logs are simulated for safe demonstration.
* Real-time streaming is not included.
* Enterprise authentication and role-based access control are not implemented.
* The platform is intended for local lab and educational use.

---

## Ethical and Legal Notice

This project is created strictly for educational, defensive security, and SOC analyst training purposes.

The project uses:

* Public security datasets
* Synthetic enterprise-style logs
* Controlled local lab-generated events

This project does not use:

* Real enterprise logs
* Private user data
* Unauthorized live system data
* Leaked credentials
* Dark web data
* Logs collected from public targets without permission

Do not use this project to attack, scan, monitor, or collect logs from systems you do not own or do not have explicit permission to test.

---

## Resume Description

**FinSOC Analyst Workbench — SIEM Alert Investigation & Threat Hunting Platform**
**Python, Streamlit, SQLite, YAML Rules, Suricata, Sysmon, MITRE ATT&CK**

Built a SOC analyst dashboard to ingest and correlate Linux authentication logs, Windows Sysmon events, Suricata IDS alerts, WAF-style logs, and DLP-style events. Implemented detection rules for brute-force attempts, suspicious PowerShell activity, port scanning, web attack indicators, and sensitive-data transfer alerts. Mapped alerts to MITRE ATT&CK techniques and created triage workflows for severity classification, root-cause analysis, incident tracking, and remediation notes. Generated PDF incident reports containing alert timelines, affected assets, indicators of compromise, investigation evidence, impact, and recommended actions.

---

## Author

**Sai Mani Kumar Pemmanaboina**

Cyber Security student interested in SOC, Incident Response, Threat Hunting, Cloud Security, Digital Forensics, and Offensive Security.

---

## References

* MITRE ATT&CK
* Sigma Detection Rules
* Suricata Documentation
* Streamlit Documentation
* OWASP Top 10
* Wazuh Documentation

---

## License

This project is released for educational and defensive security learning purposes.

Recommended license: MIT License.

```
```

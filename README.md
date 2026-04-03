# 🛡️ End-to-End SOC Automation & Detection Lab

## 📌 Project Overview
This project involved building a fully functional **Security Operations Center (SOC) Lab** to simulate real-world cyber attacks and monitor them in real-time. I configured a **Wazuh SIEM** to ingest telemetry from a Windows endpoint and successfully detected a **Brute-Force Attack** executed from a Kali Linux machine.

---

## 🏗️ Lab Architecture
* **Attacker (Host OS):** Kali Linux (`192.168.56.1`)
* **Victim (VM):** Windows 10 (`192.168.56.3`)
* **SIEM/XDR (VM):** Wazuh Manager (`192.168.56.4`)

> **Note:** The environment was built using **Oracle VirtualBox** in a dedicated **Host-Only Network** to ensure safe, isolated testing.

---

## 🛠️ Tools & Technologies Used
* **Wazuh (SIEM/XDR):** Log ingestion, analysis, and alerting.
* **Hydra:** SMB Protocol Brute-Force simulation.
* **PowerShell:** Endpoint hardening and Audit Policy configuration.
* **Linux/Bash:** System administration and attack execution.

---

## 🚀 Execution Phases

### Phase 1: Endpoint Configuration (Hardening)
1. **Telemetry Pipeline:** Installed the Wazuh Agent on Windows and configured `ossec.conf`.
2. **Audit Policy Tuning:** Enabled advanced auditing for "Logon" using:
   `auditpol /set /subcategory:"Logon" /success:enable /failure:enable`
3. **Troubleshooting:** Resolved kernel module conflicts (`/dev/vboxnetctl`) on the Kali Host.

### Phase 2: The Attack (SMB Brute Force)
* **Command:** `hydra -l vboxuser -P wordlist.txt smb://192.168.56.3 -t 1 -V`
* **Observation:** Initial "Invalid Reply" errors were mitigated by disabling SMB signing and reducing attack threads.

### Phase 3: Detection & Analysis
Captured high-fidelity alerts in the Wazuh Dashboard:
* **Rule 60122:** Multiple Windows Logon Failures.
* **Rule 60115:** **Account Lockout** detected (High Severity).
* **Rule 60118:** **Successful Logon** (The breach moment).

---

## 🔍 Forensic Investigation (JSON Analysis)
By analyzing the raw **JSON telemetry**, I confirmed:
* **Attacker IP:** `192.168.56.1`
* **Target User:** `vboxuser`
* **Logon Type:** Type 3 (Network Login)

---

## 📈 Next Steps (Phase 2)
I am currently implementing **Active Response** to automatically block attacker IPs upon detecting a Brute-Force signature.

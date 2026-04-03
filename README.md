🛡️ End-to-End SOC Automation & Detection Lab
📌 Project Overview

This project involved building a fully functional Security Operations Center (SOC) Lab to simulate real-world cyber attacks and monitor them in real-time. I configured a Wazuh SIEM (Security Information and Event Manager) to ingest telemetry from a Windows endpoint and successfully detected a Brute-Force Attack executed from a Kali Linux machine.

The goal was to move beyond simple log collection and into Active Detection Engineering, mapping attack patterns to the MITRE ATT&CK Framework.
🏗️ Lab Architecture

The lab environment was built using Oracle VirtualBox in a dedicated Host-Only Network (192.168.56.0/24) to ensure a safe, isolated testing environment.

    Attacker (Host OS): Kali Linux (192.168.56.1)

    Victim (Virtual Machine): Windows 10 (192.168.56.3)

    SIEM/XDR (Virtual Machine): Wazuh Manager (192.168.56.4)

🛠️ Tools & Technologies Used

    Wazuh (SIEM/XDR): For log ingestion, analysis, and alerting.

    Hydra: To simulate the SMB Brute-Force attack.

    Windows Event Viewer: For local log verification.

    PowerShell: For endpoint hardening and Audit Policy configuration.

    Linux/Bash: For system administration and attack execution.

🚀 Execution Phases
Phase 1: Endpoint Configuration (Hardening)

To ensure the SIEM could "see" the attack, I performed the following:

    Telemetry Pipeline: Installed the Wazuh Agent on Windows and configured ossec.conf to communicate with the Manager.

    Audit Policy Tuning: Enabled advanced auditing for "Logon" (Success/Failure) using:
    auditpol /set /subcategory:"Logon" /success:enable /failure:enable

    Network Troubleshooting: Resolved a kernel module conflict (/dev/vboxnetctl) on the Kali Host to establish a stable virtual network.

Phase 2: The Attack (SMB Brute Force)

I utilized Hydra to target the SMB protocol on the Windows machine.

    Scenario: Attempting to crack the vboxuser account.

    Command: hydra -l vboxuser -P /usr/share/wordlists/metasploit/unix_passwords.txt smb://192.168.56.3 -t 1 -V

    Challenge: Initially met with "Invalid Reply" errors. I resolved this by disabling SMB signing and reducing thread counts to bypass basic service-level protections.

Phase 3: Detection & Analysis

The attack triggered several high-fidelity alerts in the Wazuh Dashboard:

    Rule 60122 (Level 5): Multiple Windows Logon Failures.

    Rule 60115 (Level 9): Account Lockout detected due to excessive failures.

    Rule 60106 (Level 3): Successful Logon (The breach moment).

    Rule 92657 (Level 6): Suspicious Remote Logon (NTLM authentication).

🔍 Forensic Investigation (JSON Analysis)

By deep-diving into the raw JSON telemetry, I was able to confirm:

    Attacker Attribution: Source IP 192.168.56.1 identified.

    Target User: vboxuser account targeted.

    Logon Type 3: Confirmed the attack was a network-based logon, consistent with SMB.

🎯 Key Takeaways

    Visibility is King: Without proper Audit Policies, a successful breach (Event ID 4624) looks identical to a normal login.

    Correlation Matters: Identifying the transition from a "Lockout" (Rule 60115) to a "Success" (Rule 60106) is critical for identifying account takeovers.

    Lab Persistence: Troubleshooting VirtualBox network drivers and Windows ACL permissions was essential to maintaining a stable lab environment.

📈 Next Steps (Phase 2)

My next objective is to implement Active Response. I will configure the Wazuh Manager to automatically trigger a firewall block on the Attacker IP upon detecting a Brute-Force signature.

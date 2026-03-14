📌 Project Overview

This project demonstrates the architecture and deployment of a professional Security Operations Center (SOC) environment. The primary objective was to establish a centralized monitoring system capable of detecting real-time threats and analyzing deep-level system telemetry. By integrating Wazuh (SIEM/XDR) with Sysmon, I successfully created a robust defense-in-depth pipeline to identify adversarial behaviors.

🏗️ Lab Architecture

SIEM Manager: Wazuh Server (Ubuntu 22.04 LTS) – 192.168.32.130

Target Endpoint: Windows 10 Pro (Wazuh Agent + Sysmon) – 192.168.32.129

Attacker Machine: Kali Linux (Nmap, Hydra, Metasploit) – 192.168.32.128

Virtualization: VMware Workstation Pro (NAT Networking)

🛠️ Implementation & Configuration

Phase 1: 
SIEM Manager Deployment
Deployed the Wazuh Analyzer via OVA.
Initialized system services and verified the Manager IP using hostname -I.
Default Access: * CLI: wazuh-user / wazuh
Dashboard (Web UI): admin / admin (Initial setup)

Phase 2:
Advanced Endpoint Hardening (Sysmon)
Standard Windows logs often lack the granularity required for deep forensic analysis. To bridge this gap:
Installed Sysmon64 using the SwiftOnSecurity configuration to monitor process hollowing, network connections, and driver loads.

Command: .\Sysmon64.exe -i sysmonconfig-export.xml -accepteula

Phase 3:
Agent Authentication & Secure Connection
Installed the Wazuh Agent on the Windows Target.

Manual Registration: Due to network isolation, I performed manual agent authentication via PowerShell (Admin):cd "C:\Program Files (x86)\ossec-agent"
.\agent-auth.exe -m 192.168.32.130

Verified the "Valid key received" status, ensuring a secure handshake between the agent and manager.

Phase 4:
Telemetry Pipeline Integration (The "Wiring")
To ingest Sysmon logs into Wazuh, I manually modified the agent configuration:
Opened C:\Program Files (x86)\ossec-agent\ossec.conf using Notepad (Run as Administrator).
Appended the following XML block before the </ossec_config> tag to map the Sysmon Event Channel:
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>

Restarted the Wazuh Agent service to initialize log shipping.

⚔️ Cyber Attack Simulation & Detection

1. Network Reconnaissance (Nmap)
Attack: Executed nmap -A 192.168.32.129 from Kali Linux.
Detection: Wazuh triggered High Severity (Level 12) alerts, identifying host discovery and port scanning activity.

2. Brute Force Simulation (Hydra)
3. 
Attack: Simulated an RDP/SMB brute-force attack.
Detection: Captured multiple failed authentication attempts, visualized through real-time bar graphs on the dashboard.

📊 Results & Key Takeaways

Real-time Alerting: Successfully mapped system events to the MITRE ATT&CK framework.
Log Granularity: Sysmon integration provided visibility into Parent PIDs and Network Sockets that were previously invisible.
Infrastructure Management: Gained hands-on experience in SIEM administration, XML-based log management, and defensive security posture.

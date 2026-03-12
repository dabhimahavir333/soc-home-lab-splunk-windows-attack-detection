# SOC Home Lab – Splunk Windows Attack Detection

## Overview

This project demonstrates a **Security Operations Center (SOC) home lab** designed to simulate cyber attacks on a Windows system and detect them using **Sysmon and Splunk SIEM**.
The goal of this lab is to showcase real-world SOC analyst skills such as **log collection, threat detection, attack simulation, and incident investigation**.

The environment simulates an attacker using **Kali Linux** and a victim **Windows 10 machine**, with telemetry collected through Sysmon and analyzed using Splunk.

---

## Lab Architecture

```
Kali Linux (Attacker)
        ↓
Windows 10 (Victim)
        ↓
Sysmon Logs
        ↓
Splunk Universal Forwarder
        ↓
Splunk SIEM
        ↓
Detection Rules + Alerts
```

### Architecture Explanation

1. **Kali Linux (Attacker)**
   Used to simulate attacks such as network scans and suspicious command execution.

2. **Windows 10 (Victim)**
   The target machine where activity occurs and logs are generated.

3. **Sysmon**
   A Windows system monitoring tool that records detailed telemetry such as:

   * Process creation
   * Network connections
   * Command execution
   * Parent-child process relationships

4. **Splunk Universal Forwarder**
   Sends logs from the Windows machine to the Splunk SIEM server.

5. **Splunk SIEM**
   Collects, indexes, and analyzes logs to identify suspicious activity.

6. **Detection Rules & Alerts**
   Custom queries are used to detect suspicious behavior and generate alerts.

---

## Technologies Used

* Kali Linux
* Windows 10
* Sysmon
* Splunk Enterprise
* Splunk Universal Forwarder
* Nmap
* PowerShell

---

## Attack Simulations

### 1. Port Scanning (Reconnaissance)

The attacker performs a port scan against the Windows machine.

Example command executed on Kali Linux:

```
nmap -sS <windows-ip>
```

This simulates the **reconnaissance phase** of an attack.

---

### 2. Suspicious PowerShell Execution

The attacker runs a hidden PowerShell command on the Windows system.

Example command:

```
powershell -nop -w hidden -Command "whoami"
```

Attackers frequently use PowerShell to execute malicious commands or download payloads.

---

## Detection Rules

### 1. Network Scan Detection

Detects hosts attempting to connect to multiple ports.

```
EventCode=3
| stats dc(DestinationPort) as unique_ports by SourceIp
| where unique_ports > 5
| sort -unique_ports
```

**Explanation**

* `EventCode=3` represents network connections.
* The query counts how many unique ports each source IP connects to.
* If the number exceeds a threshold, it may indicate a port scan.

---

### 2. Process Execution Monitoring

Tracks frequently executed processes on the system.

```
EventCode=1
| stats count by Image
| sort -count
```

**Explanation**

* `EventCode=1` represents process creation.
* This query helps identify unusual processes running on the system.

---

### 3. Command Prompt Activity Detection

Detects usage of command-line tools.

```
EventCode=1 Image="*cmd.exe"
| table _time Image CommandLine ParentImage
```

**Explanation**
Shows when command prompt processes were executed and which process launched them.

---

### 4. Suspicious PowerShell Detection

Detects hidden or suspicious PowerShell commands.

```
EventCode=1
| search Image="*powershell.exe"
| search CommandLine="*-nop*" OR CommandLine="*-w hidden*"
| table _time Computer CommandLine ParentImage
```

**Explanation**
Attackers commonly use flags such as:

* `-nop` (no profile)
* `-w hidden` (hide window)

These indicators are often associated with malicious scripts.

---

## Investigation Workflow

When an alert is triggered, the SOC analyst can investigate by examining:

* Command line arguments
* Parent-child process relationships
* Network connections
* Timeline of events

This helps determine whether the activity is legitimate or malicious.

---

## Project Screenshots

Screenshots showing log ingestion, detection queries, and attack simulations are available in the **screenshots** folder.

Example:

```
screenshots/
01-port-scan-detection.png
02-powershell-detection.png
```

---

## Learning Outcomes

This project demonstrates the following cybersecurity skills:

* SIEM log analysis
* Endpoint telemetry monitoring
* Attack simulation
* Threat detection using Splunk queries
* Incident investigation workflow

These are core tasks performed by **SOC analysts in real-world security operations centers**.

---

## Future Improvements

Possible enhancements for this lab include:

* Adding email or Slack alert notifications
* Creating automated response scripts
* Building Splunk dashboards for threat visualization
* Simulating additional attacks (credential dumping, reverse shells)

---

## Author

Mahavirsinh Dabhi

Cybersecurity Enthusiast | SOC Analyst Aspirant


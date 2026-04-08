# Wazuh + Sysmon + File Integrity Monitoring Lab

## Overview

This lab demonstrates how to monitor file activity in real time using Wazuh and Sysmon on a Windows 10 system.

The goal is not just setup, but validating detection.

---

## Lab Architecture

Windows 10 VM (Agent + Sysmon)
        ↓
Wazuh Agent
        ↓
Wazuh Server (OVA)
        ↓
Wazuh Dashboard

---

## Tools Used

- Wazuh
- Sysmon
- VMware
- Windows 10

---

## Step-by-Step Implementation

### 1. Install Sysmon (On Windows 10 VM)

Download:
https://download.sysinternals.com/files/Sysmon.zip

Extract files.

Open PowerShell as Administrator:

Go to folder:
cd C:\Users\asifa\Downloads\Sysmon

Run:

.\Sysmon.exe -accepteula -i sysmonconfig.xml

Verify:

sysmon -c

---

### 2. Configure Wazuh Agent

Path:
C:\Program Files (x86)\ossec-agent\

Open:
win32ui.exe (Run as Administrator)

Go to:
View → View Config

---

### 3. Enable File Integrity Monitoring (FIM)

Find <syscheck> section and update:

<syscheck>
  <disabled>no</disabled>
  <scan_on_start>yes</scan_on_start>
  <alert_new_files>yes</alert_new_files>

  <directories check_all="yes" realtime="yes" report_changes="yes">
    C:\Users\*\Downloads
  </directories>
</syscheck>

---

### 4. Add Sysmon Logs to Wazuh

Above "Policy monitoring" section add:

<localfile>
  <log_format>eventchannel</log_format>
  <location>Microsoft-Windows-Sysmon/Operational</location>
</localfile>

---

### 5. Restart Wazuh Agent

Open Agent GUI:

Stop → Start again

Status should show:
Agent running

---

### 6. Validate Detection (Testing)

Inside Downloads:

1. Create file:
   test.txt

2. Modify file:
   add text

3. Delete file

---

### 7. Check Logs in Wazuh Dashboard

Open:
https://YOUR-IP

Go to:
Discover

Filter:
agent.id
agent.name
agent.ip

Set time:
Last 15 minutes

---

## Results

- File creation detected
- File modification detected
- File deletion detected

Logs successfully visible in Wazuh dashboard.

---

## Key Finding

Wazuh detects:
- File create
- File modify
- File delete

But does NOT detect:
- Folder deletion by default

---

## Important Learning

- Default config is not enough
- Sysmon improves visibility
- Real-time monitoring requires tuning
- Filtering reduces noise

---

## Screenshots

See /screenshots folder

---

## Conclusion

This lab shows how to build a basic detection pipeline using Wazuh and Sysmon, and validate real-time monitoring instead of just configuring tools.

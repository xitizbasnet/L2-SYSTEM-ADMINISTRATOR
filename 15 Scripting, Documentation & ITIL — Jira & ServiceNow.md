# 📘 LAB 15 | Scripting, Documentation & ITIL — Jira & ServiceNow

---

## 🧭 Lab Objectives

* Write PowerShell automation scripts for common sysadmin tasks
* Document system changes using proper change management process
* Use **Jira** and **ServiceNow** for incident and change ticket management

---

## 🧩 Task 1: PowerShell — Daily Health Check Script [Guided]

### 🖥️ Objective

Create a scheduled script that checks server health and emails a report.

---

### 💻 Script: `DailyHealthCheck.ps1`

```powershell id="h1p9lc"
$servers = @('SRV-DC01','SRV-APP01','SRV-FILE01')
$report = @()

foreach ($server in $servers) {

    $ping = Test-Connection -ComputerName $server -Count 1 -Quiet

    $disk = Get-WmiObject Win32_LogicalDisk -ComputerName $server `
    -Filter 'DriveType=3' | Select DeviceID,
    @{N='FreeGB';E={[math]::Round($_.FreeSpace/1GB,1)}},
    @{N='TotalGB';E={[math]::Round($_.Size/1GB,1)}}

    $cpu = (Get-WmiObject Win32_Processor -ComputerName $server).LoadPercentage

    $report += [PSCustomObject]@{
        Server = $server
        Online = $ping
        CPU_Pct = $cpu
        Disk = ($disk | Out-String)
    }
}

# Export report
$report | Export-Csv -Path 'C:\Reports\health.csv' -NoTypeInformation

# Send email report
Send-MailMessage -From 'sysadmin@corp.local' -To 'it-team@corp.local' `
-Subject 'Daily Server Health' -SmtpServer 'mail.corp.local' `
-Attachments 'C:\Reports\health.csv'
```

---

## ⏰ Task 2: Schedule Script via Task Scheduler [Guided]

### 🖥️ Procedure

1. Open **Task Scheduler** → **Create Task** (not Basic Task)

2. **General Tab:**

   * Name: `Daily-ServerHealthCheck`
   * Run whether user is logged on or not
   * Run as: `svc-monitoring`

3. **Triggers Tab:**

   * Daily at **7:00 AM**
   * Enabled

4. **Actions Tab:**

   * Program: `powershell.exe`
   * Arguments:

     ```text
     -File "C:\Scripts\DailyHealthCheck.ps1" -ExecutionPolicy Bypass
     ```

5. **Settings Tab:**

   * Allow task to run on demand
   * Stop task if it runs longer than 30 minutes

6. Click **OK**

7. Test: Right-click task → **Run**

---

### 💻 PowerShell: Create Scheduled Task

```powershell id="s7v2mn"
$action = New-ScheduledTaskAction -Execute 'powershell.exe' `
-Argument '-File C:\Scripts\DailyHealthCheck.ps1 -ExecutionPolicy Bypass'

$trigger = New-ScheduledTaskTrigger -Daily -At 7:00AM

Register-ScheduledTask -TaskName 'Daily-ServerHealthCheck' `
-Action $action -Trigger $trigger `
-RunLevel Highest -User 'corp\svc-monitoring' -Password 'P@ssword1'
```

---

## 🧩 Task 3: ITIL Change Management — Raise a Change Ticket [Practice]

### 📋 Change Ticket Structure

| Field               | Example Value                                                      |
| ------------------- | ------------------------------------------------------------------ |
| Change Type         | Standard / Normal / Emergency                                      |
| Summary             | Install Windows Security Patches — SRV-APP01                       |
| Impact              | Low — Single server, non-critical                                  |
| Urgency             | Medium — Monthly patch cycle                                       |
| Risk Assessment     | Low — Snapshot taken, rollback plan documented                     |
| Implementation Plan | 1. Snapshot VM 2. Apply patches 3. Reboot 4. Verify services       |
| Backout Plan        | Revert VM snapshot via Hyper-V/vSphere if services fail post-patch |
| Change Window       | Saturday 02:00–04:00 IST (maintenance window)                      |
| Approver            | IT Manager / CAB (Change Advisory Board)                           |
| Post-Implementation | Verify patch list, test app, update CMDB, close ticket             |

---

## 🎫 Task 4: Jira & ServiceNow — Ticket Types [Guided]

### 📊 Ticket Classification

| Ticket Type              | When to Use                                  | Example                                            |
| ------------------------ | -------------------------------------------- | -------------------------------------------------- |
| **Incident (INC)**       | Something is broken or service is down       | Server down, app crash, login failure              |
| **Service Request (SR)** | User requests something new                  | New user account, software installation            |
| **Change (CHG)**         | Planned infrastructure modification          | Patch server, update firewall rule, add DNS record |
| **Problem (PRB)**        | Root cause investigation of recurring issues | Repeated login failures over time                  |

---

## ⭐ Best Practice Tips

* Every production change must have a **ticket (No ticket = No change)**
* Implementation plans must be **clear enough for another engineer to execute**
* Always document **rollback plan before approval**
* Keep **CMDB updated after every change**
* Assign correct **CI (Configuration Item)** in ServiceNow for relationship mapping
* Use **Jira automation rules** to assign tickets based on labels (e.g., `server → L2 team`)

---

## 🔧 Alternative Tools / Methods

* **Freshservice** — Lightweight ITSM alternative to ServiceNow
* **Zendesk / Help Scout** — Simplified ticket management tools
* **Atlassian Confluence** — Documentation and SOP management with Jira integration
* **Ansible** — Replace manual scripts with automated, repeatable playbooks

---

## 📌 Notes

This lab introduces IT automation, scheduled scripting, and ITIL-based service management workflows using Jira and ServiceNow, aligning technical operations with structured enterprise change control processes.

# 07 — Patching & Updates Management (WSUS)

## 🎯 Lab Objectives

This lab focuses on implementing **centralized patch management using WSUS (Windows Server Update Services)**.

You will:

* Install and configure WSUS infrastructure
* Create computer groups for patch segmentation
* Approve and deploy updates in controlled rings
* Monitor patch compliance and update status

---

# 🔧 Task 1: Install and Configure WSUS

## 🖥️ Role Installation (Server Manager)

### Step-by-Step Procedure

1. Open:

```text id="wsus1"
Server Manager → Add Roles and Features
```

2. Select:

* Role: **Windows Server Update Services**

3. Required Components:

* WID Connectivity
* WSUS Services

---

## 💾 Storage Configuration

* Content Directory:

```text id="wsus2"
D:\WSUS
```

> ⚠️ Recommended: Dedicated disk (can exceed 100 GB in production)

---

## 🚀 Installation Process

* Install WSUS role
* Launch **Post-Installation Tasks**

  * Configures IIS
  * Configures WSUS database

---

## ⚙️ WSUS Configuration Wizard

Open WSUS Console → Run Wizard:

### Synchronization Source

* Microsoft Update

---

### Products Selection

* Windows Server 2016
* Windows Server 2019
* Microsoft Office 2021

---

### Update Classifications

* Critical Updates
* Security Updates
* Service Packs
* Update Rollups

---

### Sync Schedule

* Frequency: Daily
* Time: **3:00 AM**

---

### Initial Sync

* Begin Initial Synchronization
* May take several hours

---

# 🧩 Task 2: Computer Groups & Patch Approval

## 👥 Create Computer Groups

1. Open WSUS Console:

```text id="wsus3"
Computers → All Computers → Add Computer Group
```

2. Create groups:

* Test-Servers
* Production-Servers

---

## 🧭 Configure GPO for WSUS

Navigate:

```text id="wsus4"
Computer Configuration → Administrative Templates → Windows Update
```

---

### WSUS Server Configuration

* Intranet update service:

```text id="wsus5"
http://WSUS-Server:8530
```

---

### Enable Client-Side Targeting

* Group Name: `Test-Servers`

---

## 📦 Patch Approval Workflow

### Step 1 — Testing Phase

* WSUS → Updates → All Updates → Not Approved

* Filter by:

  * Critical Updates
  * Security Updates

* Approve for:

  * `Test-Servers`

---

### Step 2 — Production Phase

* Wait: **48 hours validation**
* Then approve same updates for:

  * `Production-Servers`

---

## ⚙️ Force Update Check (Client)

```cmd id="wsus6"
wuauclt /detectnow /updatenow
```

---

## 📊 PowerShell Patch Management

### Install Module

```powershell id="wsus7"
Install-Module PSWindowsUpdate -Force
```

---

### List Updates

```powershell id="wsus8"
Get-WUList -ComputerName SRV-APP01
```

---

### Install Updates

```powershell id="wsus9"
Install-WindowsUpdate -AcceptAll -AutoReboot
```

---

### Check Installed Updates

```powershell id="wsus10"
Get-HotFix | Sort-Object InstalledOn -Descending | Select -First 5
```

---

# 📌 Best Practice Tips

## 🔁 Patch Lifecycle Strategy

Follow structured ring deployment:

* Dev → Test → UAT → Staging → Production

> ❗ Never patch production first

---

## 🕒 Maintenance Windows

* Schedule reboots during approved downtime
* Coordinate with application owners

---

## 📸 Snapshot Policy

* Always take VM snapshots before patching critical systems

---

## 🧪 Validation After Patching

* Test application functionality after update cycles
* Verify service stability before marking compliant

---

## 🧹 WSUS Maintenance

* Automatically decline superseded updates
* Prevent database bloat

---

## 📊 Monitoring Events

Track:

* Event ID 19 → Update installed successfully
* Event ID 20 → Update installation failed

---

# 🧰 Alternative Tools / Methods

* 🖥️ Microsoft Endpoint Configuration Manager (MECM/SCCM)

  * Enterprise patch orchestration

* 🧪 Ivanti / Qualys Patch Management

  * Vulnerability-driven patch automation

* ⚡ PDQ Deploy

  * Lightweight patch and software deployment

* ☁️ Azure Update Manager

  * Cloud-native patching for hybrid environments

---

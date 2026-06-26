# 05 — Server Provisioning (Windows Server & Virtual Machines)

## 🎯 Lab Objectives

This lab covers **virtual machine provisioning, domain integration, and post-deployment hardening** for Windows Server environments.

You will:

* Deploy Windows Server 2019 VMs on Hyper-V and VMware vSphere/ESXi
* Join servers to Active Directory domain
* Apply initial configuration and hardening settings

---

# 🖥️ Task 1: Create a VM on Hyper-V

## ⚙️ Hyper-V Manager Deployment

### Step-by-Step Procedure

1. Open:

```text id="srv1"
Hyper-V Manager
```

2. Create new virtual machine:

* Right-click host → **New → Virtual Machine**

---

## 🧩 VM Configuration

### General Settings

* Name: `SRV-APP01`
* Generation: **Generation 2 (UEFI + Secure Boot support)**

---

### Memory Configuration

* Startup RAM: **4096 MB**
* Enable Dynamic Memory:

  * Minimum: **2048 MB**
  * Maximum: **8192 MB**

---

### Networking

* Virtual Switch: `External-vSwitch`

---

### Storage

* Disk Type: **VHDX**
* Size: **100 GB**
* Location:

```text id="srv2"
D:\VMs\SRV-APP01\
```

---

### Installation Media

* Attach ISO:

  * Windows Server 2019 Datacenter

---

### Security Settings

* Enable **Secure Boot**
* Template: Microsoft UEFI Certificate Authority

---

## 🚀 Deployment

* Finish VM creation
* Start VM
* Install Windows Server (Desktop Experience)

---

# ☁️ Task 2: Create VM on VMware vSphere / ESXi

## 🖥️ vSphere Deployment

### Step-by-Step

1. Open:

```text id="srv3"
vSphere Client
```

2. Create VM:

* Right-click Host/Cluster → **New Virtual Machine**

---

## 🧩 VM Configuration

### General Settings

* Name: `SRV-APP02`
* OS Type: Windows Server 2019 (64-bit)

---

### Compute Resources

* CPU: **2 vCPU**
* Memory: **4 GB**

---

### Storage

* Disk Size: **80 GB**
* Provisioning: **Thin Provisioned**
* Datastore: must have **>120 GB free**

---

### Networking

* Network Adapter:

  * Port Group: `VLAN-Production`

---

### Installation Media

* Attach ISO:

  * Windows Server 2019 ISO (Datastore)

---

## 🚀 Final Steps

* Power on VM
* Complete OS installation
* Install **VMware Tools**

---

# 🔗 Task 3: Post-Deployment Configuration

## 🏷️ Rename + Domain Join

### GUI Method

1. Open:

```text id="srv4"
Server Manager → Local Server → Computer Name → Change
```

2. Rename system:

* `SRV-APP01`

---

### Domain Join

* Domain: `corp.local`
* Authenticate using Domain Admin credentials
* Restart system

---

## 🔍 Verification

```powershell id="srv5"
Get-WmiObject Win32_ComputerSystem | Select Name, Domain
```

---

## 🔐 Enable Remote Access

* Enable RDP:

  * Server Manager → Local Server → Remote Desktop → Enable

* Allow through Windows Firewall

---

## 🔄 Enable Windows Updates

```text id="srv6"
sconfig → Option 5 → A (All Updates)
```

---

## ⚙️ PowerShell: Combined Rename + Domain Join

```powershell id="srv7"
Add-Computer -DomainName corp.local -NewName SRV-APP01 `
-Credential (Get-Credential) -Restart
```

---

# 📌 Best Practice Tips

## 🧭 VM Deployment Standards

* Always use **Generation 2 VMs (Hyper-V)** for modern workloads
* Follow naming convention:

  * `SITE-ROLE-NUMBER`
  * Example: `MUM-APP-01`, `MUM-DC-01`

---

## 📸 Snapshot Strategy

* Take snapshot BEFORE:

  * Domain join
  * Major configuration changes

---

## ⚙️ Performance Optimization

* Install integration tools immediately:

  * VMware Tools / Hyper-V Integration Services

---

## ⏰ Time Synchronization (Critical)

Configure NTP:

```cmd id="srv8"
w32tm /config /manualpeerlist:time.windows.com /syncfromflags:manual /update
```

---

# 🧰 Alternative Tools / Methods

* 🧱 Windows Deployment Services (WDS)

  * PXE-based OS deployment

* 🧩 Microsoft Deployment Toolkit (MDT)

  * Automated imaging with task sequences

* ☁️ Terraform (vSphere Provider)

  * Infrastructure-as-Code VM provisioning

* 🖥️ SCCM / MECM

  * Enterprise OS deployment + patch management

---

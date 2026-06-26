# 04 — Network Configuration

## 🎯 Lab Objectives

This lab covers foundational **server network configuration and redundancy design**, including:

* Static and dynamic IP configuration on Windows Server NICs
* NIC Teaming (LBFO) for redundancy and load balancing
* VLAN tagging and network segmentation concepts

---

# 🌐 Task 1: Assign Static IP to Server NIC

## 🖥️ Method — GUI Configuration (Server Manager)

### Step-by-Step Procedure

1. Open:

```text id="nc1"
Server Manager → Local Server → Ethernet → Properties
```

2. Select:

```text id="nc2"
Internet Protocol Version 4 (TCP/IPv4)
```

3. Click **Properties**

---

## ⚙️ Configure Static IP Settings

Set the following values:

* IP Address: `10.0.1.50`
* Subnet Mask: `255.255.255.0`
* Default Gateway: `10.0.1.1`
* Preferred DNS: `10.0.0.1` (Domain Controller)

---

## ✅ Verification Steps

### Check IP configuration

```cmd id="nc3"
ipconfig /all
```

### Test connectivity

```cmd id="nc4"
ping 10.0.1.1   # Gateway
ping 10.0.0.1   # Domain Controller
```

---

## ⚙️ PowerShell Configuration

### Set Static IP

```powershell id="nc5"
New-NetIPAddress -InterfaceAlias 'Ethernet' -IPAddress 10.0.1.50 `
-PrefixLength 24 -DefaultGateway 10.0.1.1
```

---

### Configure DNS Servers

```powershell id="nc6"
Set-DnsClientServerAddress -InterfaceAlias 'Ethernet' `
-ServerAddresses ('10.0.0.1','10.0.0.2')
```

---

### Verify Configuration

```powershell id="nc7"
Get-NetIPAddress -InterfaceAlias 'Ethernet'
Get-NetRoute -InterfaceAlias 'Ethernet'
```

---

# 🔗 Task 2: Configure NIC Teaming (LBFO)

## 🧩 Purpose

NIC Teaming provides:

* High availability (failover)
* Load balancing
* Increased throughput

---

## 🖥️ GUI Configuration

### Step-by-Step

1. Open:

```text id="nc8"
Server Manager → Local Server → NIC Teaming
```

2. Click:

* **Tasks → New Team**

3. Configure:

* Team Name: `Team-Production`
* Adapters: `Ethernet`, `Ethernet 2`
* Teaming Mode: **Switch Independent**
* Load Balancing: **Dynamic**

---

## ⏱️ Status Verification

* Wait ~30 seconds for initialization
* Status should show: **Up**

---

## ⚙️ PowerShell Configuration

### Create NIC Team

```powershell id="nc9"
New-NetLbfoTeam -Name 'Team-Production' `
-TeamMembers 'Ethernet','Ethernet 2' `
-TeamingMode SwitchIndependent `
-LoadBalancingAlgorithm Dynamic
```

---

### Verify Team Status

```powershell id="nc10"
Get-NetLbfoTeam
Get-NetLbfoTeamMember
```

---

# 📌 Best Practice Tips

## 🧭 IP Addressing Design

* Plan IP ranges before deployment:

  * IP address
  * Hostname
  * Role
  * VLAN
  * Subnet

---

## 🔌 NIC Segmentation Strategy

Use dedicated NICs for:

* Management traffic
* Production traffic
* Storage traffic

---

## 🔀 NIC Teaming Considerations

* Switch Independent mode: no switch configuration required
* Switch Dependent mode (LACP): requires network team coordination

---

## 🌐 IPv6 Considerations

* Do NOT disable IPv6 unless explicitly required
* Disabling may break:

  * ISATAP
  * Internal services
  * Modern Windows networking features

---

## ⚡ NIC Performance Settings

* Use **Auto-Negotiation** for speed/duplex unless instructed otherwise

---

# 🧰 Alternative Tools / Methods

* ⚙️ PowerShell DSC

  * Declarative network configuration at scale

* 🖧 Hyper-V Virtual Switch

  * Preferred for VM networking instead of LBFO in virtual environments

* 🔗 SET (Switch Embedded Teaming)

  * Modern replacement for LBFO in Hyper-V systems

---

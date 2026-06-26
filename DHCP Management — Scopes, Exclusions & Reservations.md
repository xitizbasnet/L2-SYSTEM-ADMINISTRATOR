# 📘 08 | DHCP Management — Scopes, Exclusions & Reservations

---

## 🧭 Lab Objectives

* Create and activate a DHCP scope for a subnet
* Configure exclusion ranges and static reservations
* Set DHCP options (gateway, DNS, domain name) per scope

---

## 🧩 Task 1: Create a DHCP Scope [Guided]

### 🖥️ Procedure (DHCP Manager)

1. Open DHCP Manager (`dhcpmgmt.msc`).
2. Expand server → right-click **IPv4** → **New Scope**.
3. Name: **Mumbai-Office-VLAN10**. Description: Client VLAN 10 subnet.
4. Start IP: **10.0.10.100**. End IP: **10.0.10.200**. Subnet Mask: **255.255.255.0**.
5. Add Exclusion range: **10.0.10.100 – 10.0.10.110** (reserved for network devices).
6. Lease duration:

   * **8 days** for fixed desktops
   * **8 hours** for guest networks
7. Configure Scope Options:

   * **003 Router = 10.0.10.1**
   * **006 DNS Servers = 10.0.0.1, 10.0.0.2**
8. Configure:

   * **015 DNS Domain Name = corp.local**
9. Click **Activate Scope**

---

## 💻 PowerShell: Create DHCP Scope with Options

```powershell
Add-DhcpServerv4Scope -Name 'Mumbai-Office-VLAN10' `
-StartRange 10.0.10.100 -EndRange 10.0.10.200 `
-SubnetMask 255.255.255.0 -LeaseDuration '8.00:00:00' -State Active
```

### 🚫 Add Exclusion

```powershell
Add-DhcpServerv4ExclusionRange -ScopeId 10.0.10.0 `
-StartRange 10.0.10.100 -EndRange 10.0.10.110
```

### ⚙️ Set Scope Options

```powershell
Set-DhcpServerv4OptionValue -ScopeId 10.0.10.0 `
-Router 10.0.10.1 -DnsServer 10.0.0.1,10.0.0.2 -DnsDomain corp.local
```

---

## 🧪 Task 2: Create a DHCP Reservation [Practice]

### 🖥️ Procedure (DHCP Manager)

1. Find the MAC address of the target device: run `ipconfig /all` or `arp -a`.
2. In DHCP Manager → expand the scope → **Reservations**.
3. Right-click → **New Reservation**.
4. Configure:

   * Name: **SRV-APP01-NIC**
   * IP: **10.0.10.50**
   * MAC: **00-1A-2B-3C-4D-5E** (no colons)
5. Supported types: **Both**. Click **Add**.
6. Verify: on the device, run:

   * `ipconfig /release`
   * `ipconfig /renew`
     → should receive **10.0.10.50**

---

## 💻 PowerShell: Add DHCP Reservation

```powershell
Add-DhcpServerv4Reservation -ScopeId 10.0.10.0 `
-IPAddress 10.0.10.50 -ClientId '00-1A-2B-3C-4D-5E' `
-Description 'SRV-APP01 primary NIC'
```

---

## 📊 View Active DHCP Leases

```powershell
Get-DhcpServerv4Lease -ScopeId 10.0.10.0 | Sort-Object IPAddress
```

---

## ⭐ Best Practice Tips

* Always configure **DHCP Failover** (hot-standby or load-balance) between two DHCP servers to avoid single point of failure.
* Reserve IPs **1–20** in every scope for infrastructure: gateway, switches, servers. Use exclusions for these.
* Document all DHCP reservations in an **IPAM spreadsheet or tool** alongside MAC addresses.
* Set short lease times (**4–8 hours**) for wireless/guest VLANs — devices roam and release slowly.
* Enable DHCP audit logging:

  * DHCP Manager → right-click server → **Properties** → **General** → Enable audit logging

---

## 🔧 Alternative Tools / Methods

* **DHCP Failover (Windows Server 2012+)** — Built-in hot-standby or load-balance between two DHCP servers.
* **ISC DHCP / Kea DHCP** — Open-source DHCP servers for Linux environments.
* **Infoblox** — Unified DNS, DHCP, IPAM management for enterprise networks.
* **SolarWinds IPAM** — IP address management with DHCP/DNS discovery.

---

## 📌 Notes

This lab covers foundational DHCP administration tasks used in enterprise network environments, including scope design, address control, and client assignment consistency.

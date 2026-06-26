# 06 — Role Configuration (DNS, DHCP, Print Server & IIS)

## 🎯 Lab Objectives

This lab focuses on deploying and validating core **Windows Server infrastructure roles**, including:

* DNS Server configuration and zone management
* DHCP Server setup and authorization
* Print and Document Services configuration
* IIS (Internet Information Services) web hosting

---

# ⚙️ Task 1: Install Server Roles

## 🖥️ Method — Server Manager (GUI)

### Step-by-Step Procedure

1. Open:

```text id="role1"
Server Manager → Manage → Add Roles and Features
```

2. Select:

* Role-based or feature-based installation
* Choose: **This server**

---

## 📦 Select Roles

Install the following:

* DNS Server
* DHCP Server
* Print and Document Services
* Web Server (IIS)

---

## 🌐 IIS Feature Extensions

Expand **Web Server (IIS)** and enable:

* ASP.NET 4.8
* URL Rewrite module (Feature)

---

## 🚀 Installation

* Accept dependencies
* Click **Install**
* Wait for completion

---

## ⚠️ Post-Installation Task

Click the notification flag in Server Manager:

* Complete DHCP configuration
* Authorize DHCP in Active Directory

---

## ⚙️ PowerShell Installation

```powershell id="role2"
Install-WindowsFeature -Name DNS, DHCP, Print-Services, Web-Server `
-IncludeManagementTools -Restart
```

---

## 🔐 DHCP Authorization in Active Directory

```powershell id="role3"
Add-DhcpServerInDC -DnsName 'srv-app01.corp.local' -IPAddress 10.0.1.50
```

---

# 🌐 Task 2: Configure DNS Zone

## 🧭 DNS Manager Configuration

1. Open:

```text id="role4"
DNS Manager (dnsmgmt.msc)
```

2. Navigate:

* Forward Lookup Zones → New Zone

---

## 🧩 Zone Configuration

* Type: Primary Zone
* Replication Scope: All DNS servers in domain
* Zone Name: `corp.local`

---

## 🧾 Add DNS Records

### A Record

* Name: `webserver`
* IP: `10.0.1.60`

---

### CNAME Record

* Alias: `www`
* Target:

```text id="role5"
webserver.corp.local
```

---

## 🔁 Reverse Lookup Zone

* Network ID: `10.0.1`

---

### PTR Record

* Maps IP → FQDN

---

# 🌍 Task 3: Configure IIS Web Server

## 🖥️ IIS Manager Setup

1. Open:

```text id="role6"
IIS Manager (inetmgr)
```

2. Expand:

* Sites → Add Website

---

## 🌐 Website Configuration

* Site Name: `CorpIntranet`
* Physical Path: `C:\inetpub\CorpIntranet`
* Binding:

  * Protocol: HTTP
  * Port: 80
  * Hostname: `intranet.corp.local`

---

## 📄 Create Test Page

```text id="role7"
C:\inetpub\CorpIntranet\index.html
```

Content:

```html id="role8"
Corp Intranet
```

---

## 🌍 Verification

Open browser:

```text id="role9"
http://intranet.corp.local
```

* Confirm page loads successfully

---

## 🔐 Enable HTTPS (Lab Setup)

* Bind SSL certificate (self-signed)
* Port: 443

---

## ⚙️ PowerShell IIS Configuration

```powershell id="role10"
New-Item -ItemType Directory -Path 'C:\inetpub\CorpIntranet'

Set-Content -Path 'C:\inetpub\CorpIntranet\index.html' -Value 'Corp Intranet'

New-WebSite -Name 'CorpIntranet' -Port 80 `
-PhysicalPath 'C:\inetpub\CorpIntranet' `
-HostHeader 'intranet.corp.local'
```

---

# 📌 Best Practice Tips

## 🔐 IIS Security

* Avoid using default service accounts in production
* Create dedicated service accounts with least privilege

---

## 💾 Log Management

* Store IIS logs on **D:\ drive**, not C:\
* Prevent system disk exhaustion

---

## 🖨️ Print Server Best Practices

* Use **v4 printer drivers** for modern compatibility
* Drivers auto-distribute to clients from server

---

## 📡 DHCP Best Practices

* Always reserve static IPs for servers
* Avoid DHCP assignment for infrastructure systems

---

## ⚙️ IIS Performance Optimization

* Configure application pool recycling during off-peak hours
* Enable request logging for monitoring and auditing

---

# 🧰 Alternative Tools / Methods

* 🌐 NGINX / Apache on Windows

  * Alternative web server platforms

* 🖨️ PaperCut MF / NG

  * Enterprise print management solutions

* 🌐 Infoblox / BlueCat

  * Enterprise-grade DNS, DHCP, and IPAM (DDI) systems

---


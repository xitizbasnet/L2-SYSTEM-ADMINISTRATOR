# 📘 14 | FTP Server Setup — IIS with Authentication & Firewall

---

## 🧭 Lab Objectives

* Create and configure an FTP site in IIS
* Configure Basic and Windows Authentication for FTP
* Set firewall rules to allow FTP active and passive mode traffic

---

## 🧩 Task 1: Install FTP Role and Create FTP Site [Guided]

### 🖥️ Procedure (Server Manager + IIS)

1. Open **Server Manager** → **Add Roles and Features**
2. Under **Web Server (IIS)** → expand **FTP Server**:

   * Enable **FTP Service**
   * Enable **FTP Extensibility**
3. Click **Install**

---

### 📁 Create FTP Directory

* Create folder:

  * `D:\FTPRoot\Uploads`

---

### 🌐 Create FTP Site in IIS

1. Open **IIS Manager**
2. Go to **Sites → Add FTP Site**
3. Configure:

   * Name: **CorpFTP**
   * Physical Path: `D:\FTPRoot`
4. Binding:

   * IP: All Unassigned
   * Port: **21**
   * SSL: **No SSL** (or Require SSL for production environments)

---

### 🔐 Authentication & Authorization

5. Authentication:

   * Enable: **Basic Authentication**

6. Authorization:

   * Allow specified users: **ftpuser01**
   * Permissions:

     * Read
     * Write

7. Finish setup

---

### 🧪 Test FTP Access

* Use **FileZilla Client**

  * Host: Server IP
  * Port: 21
  * Username: `ftpuser01`
  * Password: (configured password)

---

## 💻 PowerShell: Create FTP Site

```powershell id="f1x9ab"
Import-Module WebAdministration

New-WebFtpSite -Name 'CorpFTP' -Port 21 `
-PhysicalPath 'D:\FTPRoot'
```

---

## 🔐 Enable FTP Authentication

```powershell id="b7m2qn"
Set-WebConfigurationProperty -Filter `
system.ftpServer/security/authentication/basicAuthentication `
-PSPath 'IIS:\Sites\CorpFTP' -Name 'enabled' -Value $true
```

---

## 👤 Grant FTP Access

```powershell id="k4p8ld"
Add-WebConfiguration -Filter system.ftpServer/security/authorization `
-PSPath 'IIS:\Sites\CorpFTP' `
-Value @{accessType='Allow'; users='ftpuser01'; permissions='Read,Write'}
```

---

## 🧪 Task 2: Configure Passive Mode and Firewall [Practice]

### ⚙️ Configure IIS Passive Mode

1. Open **IIS Manager** → select FTP site
2. Go to **FTP Firewall Support**
3. Configure:

   * Data Channel Port Range: **5000–5050**
   * External IP: (server NAT/public IP if applicable)
4. Click **Apply**
5. Restart FTP service:

```powershell id="r3v7mz"
Restart-Service ftpsvc
```

---

## 🔥 Configure Windows Firewall Rules

### 🌐 Allow FTP Control Port (21)

```powershell id="c9m1xp"
New-NetFirewallRule -DisplayName 'FTP Control TCP 21' `
-Direction Inbound -Protocol TCP -LocalPort 21 -Action Allow
```

---

### 📡 Allow Passive FTP Data Ports (5000–5050)

```powershell id="t6n4qz"
New-NetFirewallRule -DisplayName 'FTP Passive Data 5000-5050' `
-Direction Inbound -Protocol TCP -LocalPort 5000-5050 -Action Allow
```

---

## 🔎 Verify FTP Service

### Check service status:

```powershell id="v8k2ld"
netstat -an | findstr ':21'
```

---

### Test FTP passive mode:

* FileZilla Settings:

  * Transfer Mode: **Passive**
* Connect and verify:

  * Directory listing works
  * Upload/download successful

---

## 📄 FTP Logs Location

* `C:\inetpub\logs\LogFiles\FTPSVC*`

---

## ⭐ Best Practice Tips

* ALWAYS use **FTPS (FTP over SSL/TLS)** or **SFTP** in production
* Plain FTP sends credentials in clear text and is insecure
* Enable **FTP User Isolation**:

  * IIS → FTP User Isolation → User name directory
* Use a **dedicated service account** for FTP access
* Avoid using domain admin accounts for FTP authentication
* Monitor FTP logs daily for:

  * Brute-force login attempts
  * Large or unusual file transfers
* Restrict FTP access by **source IP address** where possible

---

## 🔧 Alternative Tools / Methods

* **FileZilla Server** — Lightweight alternative to IIS FTP
* **OpenSSH SFTP** — Recommended secure replacement for FTP
* **WinSCP** — Client supporting FTP, FTPS, SFTP, SCP
* **Azure Blob Storage / Amazon S3** — Modern cloud-based replacement for FTP workflows

---

## 📌 Notes

This lab covers FTP server deployment using IIS, including authentication configuration, firewall setup for active/passive modes, and security best practices for production-grade file transfer services.

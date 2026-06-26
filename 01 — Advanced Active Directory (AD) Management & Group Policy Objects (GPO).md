# 01 — Advanced Active Directory (AD) Management & Group Policy Objects (GPO)

## 🎯 Lab Objectives

This lab focuses on practical administration of **Active Directory (AD)** and **Group Policy Objects (GPOs)**, including creation, linking, and troubleshooting.

You will:

* Create and manage **Organizational Units (OUs)**, users, and groups
* Create and link **GPOs** at OU level
* Troubleshoot GPO application failures using Windows tools
* Use both **GUI and PowerShell** for AD administration

---

# 🧱 Task 1: Create an Organizational Unit (OU) and Users

## 🖥️ Method 1 — GUI (Active Directory Users and Computers)

> Tool: **Active Directory Users and Computers (ADUC)**

### Step-by-Step

1. Open:

   ```text id="ad1"
   Server Manager → Tools → Active Directory Users and Computers
   ```

2. Right-click your domain (e.g., `corp.local`) →
   **New → Organizational Unit**

3. Create OU:

   * Name: `Mumbai-IT`
   * Uncheck: **Protect container from accidental deletion** (lab environment only)

4. Inside `Mumbai-IT`:

   * Right-click → **New → User**

5. Fill in:

   * First name / Last name
   * User logon name

6. Set:

   * Temporary password
   * Enable: **User must change password at next logon**

7. Repeat for test users:

   * `user01`
   * `user02`
   * `user03`

---

## ⚙️ Method 2 — PowerShell

### Create OU

```powershell id="ad2"
New-ADOrganizationalUnit -Name 'Mumbai-IT' -Path 'DC=corp,DC=local'
```

---

### Create Users in OU

```powershell id="ad3"
New-ADUser -Name 'user01' -SamAccountName 'user01' `
-UserPrincipalName 'user01@corp.local' `
-Path 'OU=Mumbai-IT,DC=corp,DC=local' `
-AccountPassword (ConvertTo-SecureString 'P@ssword1' -AsPlainText -Force) `
-Enabled $true -ChangePasswordAtLogon $true
```

---

# 🧩 Task 2: Create and Link a GPO

## 🖥️ Steps (GPMC GUI)

1. Open:

   ```text id="ad4"
   gpmc.msc (Group Policy Management)
   ```

2. Navigate:

   ```text id="ad5"
   Forest → Domains → corp.local
   ```

3. Right-click:

   * `Mumbai-IT` OU → **Create a GPO in this domain, and Link it here**

4. Name GPO:

   * `Mumbai-IT-Policy`

5. Edit GPO:

   * Right-click → **Edit**

---

## 🔐 Configure Password Policy

Navigate:

```text id="ad6"
Computer Configuration → Windows Settings → Security Settings → Account Policies → Password Policy
```

Set:

* Minimum password length: **10**
* Password complexity: **Enabled**

---

## 🔄 Apply Policy on Client

```cmd id="ad7"
gpupdate /force
```

---

# 🔧 Task 3: Troubleshoot GPO Application Failure

## 🧪 Step-by-Step Troubleshooting Workflow

---

### 📊 1. Check Applied GPOs

```cmd id="ad8"
gpresult /r
```

---

### 🌐 2. Generate Full Report

```cmd id="ad9"
gpresult /h C:\gpreport.html
```

* Open HTML file in browser

---

### 👤 3. Verify User Location in AD

```powershell id="ad10"
Get-ADUser user01 | Select DistinguishedName
```

---

### 🔗 4. Verify GPO Link Status

* In GPMC:

  * Ensure **Link Enabled = True**

---

### 🧪 5. Check WMI Filters

* Ensure filter evaluates to **TRUE**
* Otherwise GPO will not apply

---

### 🧭 6. Check Domain Controller Health

```cmd id="ad11"
dcdiag /test:netlogons
```

---

### 📡 7. Check SYSVOL Replication

```powershell id="ad12"
Get-DfsrBacklog -DestinationComputerName DC02 -Verbose
```

---

## ⚡ Quick GPO Commands Reference

```cmd id="ad13"
gpresult /r                          # Show applied GPOs
gpresult /scope computer /v          # Detailed computer policy report
gpupdate /force                      # Force policy update
```

---

### 🔄 Remote GPO Update

```powershell id="ad14"
Invoke-GPUpdate -Computer PC01 -RandomDelayInMinutes 0
```

---

# 📌 Best Practice Tips

* Link GPOs at **OU level** for granular control
* Avoid domain-level GPOs unless global policy is required
* Use **Block Inheritance sparingly** and document usage
* Backup GPOs before changes:

```powershell id="ad15"
Backup-GPO -All -Path C:\GPOBackup
```

* Prefer **Security Filtering** over Block Inheritance
* Always test GPOs in a **staging OU before production rollout**

---

# 🧰 Alternative Tools / Methods

* 🖥️ **RSAT (Windows 10/11)** — Remote AD management tools
* 🧭 **Active Directory Administrative Center (ADAC)** — Modern AD GUI
* ☁️ **Microsoft Entra Connect** — Sync AD to Azure AD
* 🏢 **Quest ActiveRoles / ManageEngine ADManager** — Enterprise AD governance with audit trails

---


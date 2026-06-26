# 02 — Active Directory Permissions & Security (Delegation, FGPP, Auditing)

## 🎯 Lab Objectives

This lab focuses on **secure delegation, password policy hardening, and auditing in Active Directory (AD)**.

You will:

* Delegate administrative permissions at OU level
* Configure **Fine-Grained Password Policies (FGPP / PSO)**
* Enable and analyze **Active Directory auditing via security logs**

---

# 🔐 Task 1: Delegate Control Over an OU

## 🖥️ Method — Active Directory Users and Computers (ADUC)

> **Purpose:** Assign limited administrative privileges using least-privilege principles.

---

### Step-by-Step Procedure

1. Open:

```text id="adp1"
Active Directory Users and Computers (ADUC)
```

2. Navigate to:

* `Mumbai-IT` OU

3. Right-click OU →
   **Delegate Control**

---

### 👥 Add Delegated Group

* Click **Next**
* Click **Add**
* Select group:

  * `HelpDesk-L1`

---

### 🧩 Select Delegation Tasks

Enable the following permissions:

* Reset user passwords and force password change at next logon
* Read all user information

---

### ✅ Finish Delegation

* Click **Next → Finish**

---

### 🧪 Validation Test

Test using a HelpDesk-L1 user:

✔ Should succeed:

* Reset user password

❌ Should fail:

* Delete OU (confirms least-privilege enforcement)

---

# 🔑 Task 2: Configure Fine-Grained Password Policy (FGPP / PSO)

## 🖥️ Method 1 — ADAC (GUI)

> **Purpose:** Apply different password policies to specific groups.

---

### Navigate to Password Settings Container

```text id="adp2"
ADAC → corp.local → System → Password Settings Container
```

---

### Create New Password Policy

* Right-click → **New → Password Settings**

---

### Configuration

* Name: `Admin-PSO`
* Precedence: `5`

  > (Lower number = higher priority)

---

### Password Policy Settings

* Minimum password length: **14**
* Password history: **24**
* Maximum password age: **60 days**

---

### Account Lockout Policy

* Threshold: **3 attempts**
* Lockout duration: **30 minutes**

---

### Apply Policy To

* Group:

  * `Domain Admins`

---

## ⚙️ Method 2 — PowerShell

```powershell id="adp3"
New-ADFineGrainedPasswordPolicy -Name 'Admin-PSO' -Precedence 5 `
-MinPasswordLength 14 -PasswordHistoryCount 24 `
-MaxPasswordAge '60.00:00:00' `
-LockoutThreshold 3 -LockoutDuration '00:30:00' `
-ComplexityEnabled $true
```

---

### Apply Policy to Group

```powershell id="adp4"
Add-ADFineGrainedPasswordPolicySubject -Identity 'Admin-PSO' `
-Subjects 'Domain Admins'
```

---

# 📊 Task 3: Enable Active Directory Auditing via GPO

## 🛡️ Purpose

Track authentication events, user changes, and directory modifications.

---

### Step 1: Create GPO

* Name: `AD-Auditing-Policy`
* Link to:

  * **Domain Controllers OU**

---

### Step 2: Configure Audit Policies

Navigate:

```text id="adp5"
Computer Configuration → Policies → Windows Settings → Security Settings → Advanced Audit Policy Configuration
```

Enable:

* 👤 Audit Account Management:

  * Success + Failure

* 🗂️ Audit Directory Service Changes:

  * Success

* 🔐 Audit Logon Events:

  * Success + Failure

---

### Step 3: Apply Policy

```cmd id="adp6"
gpupdate /force
```

---

### Step 4: Validation Test

* Perform action:

  * Change user password

* Check logs:

```text id="adp7"
Event Viewer → Windows Logs → Security
```

Filter for:

* Event ID: **4723**

  * Password change attempt

---

# 📌 Best Practice Tips

* Always use **Security Groups**, not individual users, for permissions
* Document all delegation:

  * Who
  * What access
  * Which OU
  * Why

---

### 🔁 FGPP Governance

* Review FGPP policies quarterly
* Use:

```powershell id="adp8"
Get-ADFineGrainedPasswordPolicySubject
```

---

### 📡 Centralized Logging

* Forward logs to SIEM platforms:

  * Splunk
  * Microsoft Sentinel

---

### 📊 Critical Event IDs to Monitor

* 4720 → User created
* 4726 → User deleted
* 4728 → Group membership added
* 4732 → Group membership changed

---

# 🧰 Alternative Tools / Methods

* 🔐 Microsoft Entra ID PIM

  * Just-in-time privileged access

* 📊 Netwrix Auditor / ADAudit Plus

  * Advanced AD change auditing

* 🕵️ BloodHound (Blue Team Mode)

  * Visualizes privilege escalation paths

---

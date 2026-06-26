# 📘 LAB 13 | Network Drive Mapping via GPO Preferences

---

## 🧭 Lab Objectives

* Map department drives automatically using GPO Preferences
* Use Item-Level Targeting to assign drives based on group membership
* Troubleshoot drive mapping failures at logon

---

## 🧩 Task 1: Map a Drive via GPO Preferences [Guided]

### 🖥️ Procedure (Group Policy Management)

1. Open **Group Policy Management**

2. Edit (or create) a GPO linked to the **Mumbai-IT OU**

3. Navigate to:

   * User Configuration → Preferences → Windows Settings → **Drive Maps**

4. Right-click → **New → Mapped Drive**

5. Configure drive mapping:

   * Action: **Create**
   * Location: `\\SRV-FILE01\Finance$`
   * Drive letter: **F:**
   * Label: **Finance Share**
   * Check: **Reconnect**

6. Open **Common tab**:

   * Enable **Item-level targeting**
   * Click **Targeting**

7. Configure targeting:

   * New Item → **Security Group**
   * Select group: **Finance-Staff**
   * Click **OK**

8. Apply GPO

9. Verification:

   * Log in as **Finance-Staff user**
   * Confirm drive **F:** is mapped automatically

---

## 💻 Script-Based Alternative (Logon Script)

### ⚙️ GPO Logon Script Method

* Location:

  * User Configuration → Scripts → Logon

---

### 🧾 PowerShell / Script Example

```powershell id="g1v8lm"
# Script-based alternative: map drive in logon script

$group =
[ADSI]"LDAP://CN=Finance-Staff,OU=Mumbai-IT,DC=corp,DC=local"

$user =
[System.Security.Principal.WindowsIdentity]::GetCurrent().Name

# Check group membership and map drive
if (net localgroup 2>&1 | Select-String 'Finance') {
    net use F: \\SRV-FILE01\Finance$ /persistent:yes
}
```

---

## 🧪 Task 2: Troubleshoot Failed Drive Mappings [Practice]

### 🔍 Step-by-Step Troubleshooting

1. Check GPO application:

```cmd id="g9p2xz"
gpresult /r
```

* Verify GPO appears in **Applied Group Policy Objects**

---

2. Verify network access to file server:

```powershell id="t3m7qn"
Test-NetConnection -ComputerName SRV-FILE01 -Port 445
```

---

3. Check mapped drives:

```cmd id="n8c4ld"
net use
```

* Look for disconnected or error states

---

4. Review Event Viewer:

* Applications and Services Logs → Microsoft → Windows → **GroupPolicy → Operational**

---

5. Fix Fast Logon issues:

* Enable policy:

  * Computer Configuration → Administrative Templates → System → Logon
  * **Always wait for the network at computer startup and logon = Enabled**

---

6. Verify permissions:

* Ensure share and NTFS permissions include the user’s security group

---

## ⭐ Best Practice Tips

* Use **Replace** action (not Create) in GPO Drive Maps to prevent stale mappings
* Enable **“Always wait for the network at startup and logon”** for consistent mapping behavior
* Prefer **DFS Namespace paths** (e.g., `\\corp\shares\finance`) instead of direct server names
* Test mappings using a **non-admin user account** to ensure accurate results
* Use **Item-Level Targeting per security group** to manage multiple department drives from a single GPO

---

## 🔧 Alternative Tools / Methods

* **Logon Scripts (PowerShell/VBScript)** — Legacy method for drive mapping when GPO Preferences fail
* **Microsoft Endpoint Manager (Intune)** — Cloud-based deployment of drive mapping scripts for hybrid devices
* **OneDrive Known Folder Move** — Replaces traditional network drives by syncing Desktop/Documents/Pictures to cloud storage

---

## 📌 Notes

This lab demonstrates automated network drive deployment using Group Policy Preferences, enabling centralized and role-based access to shared storage resources in enterprise environments.

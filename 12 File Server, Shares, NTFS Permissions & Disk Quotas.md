# 📘 12 | File Server, Shares, NTFS Permissions & Disk Quotas

---

## 🧭 Lab Objectives

* Create network shares with appropriate Share and NTFS permissions
* Implement least-privilege NTFS permission structure for departments
* Configure disk quotas to control storage usage per user

---

## 🧩 Task 1: Create a Network Share with NTFS Permissions [Guided]

### 🖥️ Procedure (File Server Configuration)

1. Create folder:

   * `D:\Shares\Finance`

2. Right-click folder → **Properties** → **Sharing tab** → **Advanced Sharing**

3. Configure Share settings:

   * Check: **Share this folder**
   * Share name: **Finance$** (the `$` hides it from network browse list)

4. Click **Permissions**:

   * Remove **Everyone**
   * Add group **Finance-Staff** → Allow **Change**
   * Add group **Finance-Managers** → Allow **Full Control**

5. Go to **Security tab (NTFS permissions)**:

   * Remove **Users** from ACL

6. Add NTFS permissions:

   * **Finance-Staff** → Allow:

     * Read & Execute
     * List Folder Contents
     * Read
   * **Finance-Managers** → Full Control
   * **CREATOR OWNER** → Full Control (Subfolders and Files only)

7. Test access:

   * Map drive as **Finance-Staff user**
   * Verify:

     * Can read/write files
     * Cannot delete folders (based on inheritance rules)

---

## 💻 PowerShell: Create Share and Set Permissions

### 📁 Create Folder

```powershell id="f1k8lm"
New-Item -ItemType Directory -Path 'D:\Shares\Finance'
```

---

### 🌐 Create SMB Share

```powershell id="s9v2xq"
New-SmbShare -Name 'Finance$' -Path 'D:\Shares\Finance' `
-FullAccess 'Finance-Managers' -ChangeAccess 'Finance-Staff' `
-NoAccess 'Everyone'
```

---

### 🔐 Set NTFS Permissions (icacls)

```powershell id="n4p7rt"
icacls 'D:\Shares\Finance' /grant 'Finance-Managers:(OI)(CI)F'
icacls 'D:\Shares\Finance' /grant 'Finance-Staff:(OI)(CI)RX'
icacls 'D:\Shares\Finance' /remove 'BUILTIN\Users'
```

---

## 🧪 Task 2: Configure Disk Quotas [Practice]

### 🖥️ Procedure (GUI Method)

1. Open **File Explorer** → right-click **D: drive** → **Properties**
2. Go to **Quota tab**
3. Enable quota management:

   * Check **Deny disk space to users exceeding quota limit**
4. Set default limits:

   * Limit: **5 GB**
   * Warning level: **4.5 GB**
5. Click **Quota Entries**:

   * Add per-user overrides
6. Add user:

   * `Domain\finance-manager01`
   * Limit: **20 GB**
7. Apply changes → Quota becomes active

   * Users exceeding limit receive **“Disk Full”** error

---

## 💻 PowerShell: Disk Quota with FSRM

### ⚙️ Install File Server Resource Manager

```powershell id="q6m3vn"
Install-WindowsFeature -Name FS-Resource-Manager -IncludeManagementTools
```

---

### 📏 Create Quota Template

```powershell id="t8c1xp"
New-FsrmQuotaTemplate -Name '5GB-User-Quota' `
-Size 5GB -SoftLimit $false
```

---

### 📌 Apply Quota to Folder

```powershell id="d3n7qz"
New-FsrmQuota -Path 'D:\Shares\Finance' -Template '5GB-User-Quota'
```

---

## ⭐ Best Practice Tips

* Always configure permissions on the **Security (NTFS) tab** — Share permissions only apply to network access
* Use **least privilege principle** for all access assignments
* Enable **Access-Based Enumeration (ABE)** so users only see folders they can access
* Never grant **Everyone** access — always use security groups
* Use **FSRM** for advanced quota control, reporting, and email alerts
* Audit NTFS permissions regularly:

```powershell id="a1v9xz"
Get-Acl 'D:\Shares\Finance' | Format-List
```

* Enable ABE on shares:

```powershell id="b7m2ld"
Set-SmbShare -Name Finance$ -FolderEnumerationMode AccessBased
```

---

## 🔧 Alternative Tools / Methods

* **DFS Namespaces** — Provides unified share paths like `\\corp\shares\finance`
* **DFS Replication** — Replicates file shares between servers for high availability
* **Microsoft SharePoint / OneDrive for Business** — Cloud-based file sharing alternatives
* **NetApp ONTAP / Nutanix Files** — Enterprise-scale NAS solutions

---

## 📌 Notes

This lab demonstrates enterprise file server administration including secure share creation, NTFS permission design, and storage quota enforcement using both GUI and PowerShell automation for scalable management.

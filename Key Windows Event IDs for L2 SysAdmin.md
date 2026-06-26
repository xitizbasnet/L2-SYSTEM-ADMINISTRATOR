# 📘 Key Windows Event IDs for L2 SysAdmin

---

## 🧭 Overview

Windows Event Logs are critical for monitoring security, system health, and troubleshooting incidents in enterprise environments. The following Event IDs are commonly used by L2 System Administrators for auditing and investigation.

---

## 📊 Windows Security & System Event IDs Reference

| Event ID        | Log Type | Description                                                      |
| --------------- | -------- | ---------------------------------------------------------------- |
| **4624**        | Security | Successful account logon                                         |
| **4625**        | Security | Failed account logon (used to detect brute-force attacks)        |
| **4720**        | Security | User account created in Active Directory                         |
| **4726**        | Security | User account deleted                                             |
| **4728 / 4732** | Security | Member added to security group                                   |
| **4723**        | Security | Password change attempt                                          |
| **4740**        | Security | User account locked out                                          |
| **7045**        | System   | New service installed on the system                              |
| **6005 / 6006** | System   | Event Log service started/stopped (system reboot indicator)      |
| **41**          | System   | Kernel-Power — unexpected shutdown or crash                      |
| **1102**        | Security | Audit log cleared (critical security event / possible tampering) |

---

## 🔍 Quick Interpretation Guide

### 🔐 Authentication & Security Monitoring

* **4624** → Normal successful login (baseline activity)
* **4625** → Repeated failures may indicate brute-force attack
* **4740** → Account lockout due to failed attempts

---

### 👥 Active Directory Changes

* **4720 / 4726** → User creation/deletion tracking
* **4728 / 4732** → Group membership changes (privilege escalation risk)

---

### ⚙️ System Integrity Monitoring

* **7045** → New service installed (possible persistence mechanism in attacks)
* **1102** → Security logs cleared (high-risk indicator)

---

### 💻 System Stability / Reboots

* **6005 / 6006** → System startup/shutdown events
* **41 (Kernel-Power)** → Unexpected shutdown, power failure, or crash

---

## ⚠️ Security Best Practices

* Monitor **4625 + 4740 correlation** for brute-force detection patterns
* Alert immediately on **1102 (audit log cleared)** events
* Investigate all **7045 service installations** on production systems
* Track **4728/4732 changes** for privilege escalation attempts
* Use centralized logging (SIEM) for real-time correlation and alerting

---

## 🧰 Useful Commands for Event Log Analysis

```powershell
Get-EventLog -LogName Security -Newest 100
```

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625}
```

```powershell
Get-WinEvent -LogName System | Select-Object -First 50
```

---

## 📌 Notes

These Event IDs form the foundation of Windows security auditing and are essential for incident detection, forensic investigation, and system monitoring in enterprise IT environments.

# 📘 Quick Reference — Key Commands (IT Administration Cheat Sheet)

---

## 🧭 Overview

This section provides a consolidated reference of commonly used Windows Server, Active Directory, networking, and troubleshooting commands used throughout system administration labs.

---

## 📊 Command Reference Table

| Command / Tool                              | Purpose                                     |
| ------------------------------------------- | ------------------------------------------- |
| `gpresult /r`                               | Show applied GPOs for current user/computer |
| `gpupdate /force`                           | Force immediate Group Policy refresh        |
| `dcdiag /test:DNS`                          | Validate DNS health on domain controller    |
| `repadmin /showrepl`                        | Show Active Directory replication status    |
| `netstat -an \| findstr LISTENING`          | List all listening ports                    |
| `Test-NetConnection -Port <N>`              | Test TCP connectivity to a specific port    |
| `Get-HotFix \| Sort InstalledOn`            | List installed Windows updates/patches      |
| `ipconfig /flushdns`                        | Flush local DNS resolver cache              |
| `nslookup` / `Resolve-DnsName`              | DNS record lookup and troubleshooting       |
| `Get-NetFirewallRule`                       | List Windows Firewall rules                 |
| `Get-SmbShare`                              | List all SMB network shares                 |
| `Get-DhcpServerv4Lease`                     | List active DHCP leases                     |
| `Get-ADUser` / `Get-ADGroup`                | Query Active Directory users and groups     |
| `Backup-GPO -All -Path <folder>`            | Backup all Group Policy Objects             |
| `w32tm /query /status`                      | Check NTP time synchronization status       |
| `pathping <IP>`                             | Combined traceroute + packet loss analysis  |
| `Get-EventLog -LogName Security -Newest 50` | View latest 50 security audit events        |

---

## ⚠️ Notes & Corrections

* The original line had formatting issues; corrected command:

```powershell
netstat -an | findstr LISTENING
```

* Ensure PowerShell modules (like **ActiveDirectory**) are installed for `Get-ADUser` and `Get-ADGroup`.

---

## ⭐ Best Practice Usage Tips

* Use `gpresult /r` after any GPO change to confirm application
* Combine `Test-NetConnection` + `netstat` for full network troubleshooting
* Regularly check `repadmin /showrepl` in domain environments to detect AD sync issues
* Use `ipconfig /flushdns` when DNS changes are not reflecting immediately
* Always validate firewall rules with `Get-NetFirewallRule` after deployment
* Use `pathping` instead of `tracert` when diagnosing packet loss issues

---

## 📌 Notes

This cheat sheet consolidates essential Windows Server, Active Directory, networking, and security troubleshooting commands used in enterprise IT system administration environments.

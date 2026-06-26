# 📘 10 | Host & Port Management — Windows Firewall Rules

---

## 🧭 Lab Objectives

* Create inbound/outbound firewall rules via GUI and PowerShell
* Enable common service ports: FTP (21), HTTP/S (80/443), RDP (3389), SMB (445)
* Use GPO to deploy firewall rules across multiple servers

---

## 🧩 Task 1: Create Inbound Firewall Rules (GUI) [Guided]

### 🖥️ Procedure (Windows Defender Firewall)

1. Open **Windows Defender Firewall with Advanced Security** (`wf.msc`).

2. Click **Inbound Rules** → **New Rule** (in Actions pane).

3. Rule type: **Port**

   * Protocol: **TCP**
   * Specific ports: **21 (FTP)**

4. Action: **Allow the connection**

5. Profile: **Domain, Private** (uncheck **Public**)

6. Name: **Allow FTP Inbound TCP 21**

7. Click **Finish**

8. Repeat the same process for:

   * **80 (HTTP)**
   * **443 (HTTPS)**
   * **3389 (RDP — restrict to admin subnet)**

9. Verify rule functionality:

```powershell id="r8k1dn"
Test-NetConnection -ComputerName <serverIP> -Port 21
```

---

## 💻 PowerShell: Create Firewall Rules

### 🔓 Allow FTP (Port 21)

```powershell id="f1a9xp"
New-NetFirewallRule -DisplayName 'Allow FTP TCP 21' `
-Direction Inbound -Protocol TCP -LocalPort 21 `
-Action Allow -Profile Domain,Private
```

---

### 🌐 Allow HTTP and HTTPS

```powershell id="h3m7qz"
New-NetFirewallRule -DisplayName 'Allow HTTP/HTTPS' `
-Direction Inbound -Protocol TCP -LocalPort 80,443 `
-Action Allow -Profile Domain,Private
```

---

### 🛡️ Allow RDP (Admin Subnet Only)

```powershell id="v6t2ab"
New-NetFirewallRule -DisplayName 'Allow RDP Admin Subnet' `
-Direction Inbound -Protocol TCP -LocalPort 3389 `
-RemoteAddress 10.0.0.0/24 -Action Allow -Profile Domain
```

---

### 🚫 Block a Port (Telnet Example)

```powershell id="k9p3lm"
New-NetFirewallRule -DisplayName 'Block Telnet TCP 23' `
-Direction Inbound -Protocol TCP -LocalPort 23 -Action Block
```

---

## 🧪 Task 2: Deploy Firewall Rules via GPO [Practice]

### 🖥️ Procedure (Group Policy Management)

1. Open **Group Policy Management**.
2. Create GPO: **Server-Firewall-Policy**.
3. Edit:

   * Computer Configuration → Windows Settings → Security Settings → Windows Firewall with Advanced Security
4. Right-click **Inbound Rules** → **New Rule**

   * Configure rules (FTP, HTTP/S as above)
5. Link GPO to the **Servers OU**
6. Run policy update on target server:

```powershell id="g7c1vn"
gpupdate /force
```

7. Verify applied rules:

```powershell id="m2q8rz"
Get-NetFirewallRule | Where-Object {$_.DisplayName -like '*FTP*'}
```

---

## 🧪 Task 3: Enable FTP Passive Mode Ports [Practice]

### ⚙️ Configure Passive Mode in IIS

1. FTP passive mode uses a dynamic port range — configure this in IIS FTP settings.
2. Open **IIS Manager** → FTP server-level → **FTP Firewall Support**.
3. Set:

   * Data Channel Port Range: **5000–5050**
4. Create firewall rule:

   * Allow inbound TCP **5000–5050** on FTP server
5. Add outbound rule for FTP server’s external IP in IIS FTP Firewall Support

---

## ⭐ Best Practice Tips

* Follow the **principle of least privilege**: only open required ports; block everything else by default
* Restrict **RDP (3389)** to specific admin IP ranges — never expose to `0.0.0.0/0`
* Enable logging for dropped connections:

  * `wf.msc` → Properties → Domain Profile → Logging → Enable **Log Dropped Packets**
* Regularly audit open ports:

```powershell id="n8d4vc"
netstat -an | findstr LISTENING
```

* Always test firewall rules in a **test environment before GPO deployment**

---

## 🔧 Alternative Tools / Methods

* **Cisco ASA / Palo Alto Networks Next-Generation Firewall** — Hardware/perimeter firewalls for enterprise security
* **iptables / nftables** — Linux host-based firewall systems
* **Azure Network Security Groups** — Cloud-native traffic control for Azure environments

---

## 📌 Notes

This lab focuses on Windows host-based firewall configuration, including rule creation, service port exposure control, and centralized deployment using Group Policy for enterprise-scale security management.

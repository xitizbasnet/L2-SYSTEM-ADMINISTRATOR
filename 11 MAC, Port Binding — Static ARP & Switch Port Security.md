# 📘 LAB 11 | MAC, Port Binding — Static ARP & Switch Port Security

---

## 🧭 Lab Objectives

* Configure static ARP entries on Windows to bind IP to MAC
* Understand and simulate port security (MAC binding) on managed switches
* Prevent ARP spoofing and unauthorized device connections

---

## 🧩 Task 1: Configure Static ARP Entry on Windows [Guided]

### 🖥️ Procedure (Windows CMD)

1. Find the MAC address of the target server:

   * On the server, run `ipconfig /all`
2. Note:

   * Physical Address = **00-1A-2B-3C-4D-5E**
3. On the client/router Windows machine, open **CMD as Administrator**
4. Add static ARP entry:

```cmd id="a1k9mp"
arp -s 10.0.1.60 00-1A-2B-3C-4D-5E
```

5. Verify ARP table:

```cmd id="v7p3qs"
arp -a
```

→ Entry for **10.0.1.60** should show **static**

6. Test connectivity:

```cmd id="t4n6wr"
ping 10.0.1.60
```

→ Traffic uses the bound MAC without ARP broadcast

7. Remove static entry:

```cmd id="r9x2ld"
arp -d 10.0.1.60
```

---

## 💻 PowerShell: ARP / Neighbor Table Management

### 🔎 View ARP Cache

```powershell id="n2k8vc"
arp -a
```

---

### 🔐 Add Permanent Static ARP Entry (Survives Reboot)

```powershell id="p6m1xz"
Set-NetNeighbor -InterfaceAlias 'Ethernet' `
-IPAddress '10.0.1.60' `
-LinkLayerAddress '00-1A-2B-3C-4D-5E' `
-State Permanent
```

---

### 📋 View Neighbor (ARP) Table

```powershell id="g8q4tr"
Get-NetNeighbor -InterfaceAlias 'Ethernet'
```

---

### ❌ Delete Static Entry

```powershell id="c3v9lm"
Remove-NetNeighbor -IPAddress '10.0.1.60' -Confirm:$false
```

---

## 🧪 Task 2: Simulate Port Security on Cisco Switch (CLI) [Practice]

### ⚠️ Note

* This task uses **Cisco IOS CLI**
* Use **Cisco Packet Tracer** or **GNS3** for simulation

---

### 🖥️ Basic Port Security Configuration

1. Enter configuration mode:

```bash id="s1c7ab"
configure terminal
```

2. Select interface:

```bash id="i4p8qn"
interface FastEthernet0/1
```

3. Configure access mode:

```bash id="m6t2xd"
switchport mode access
```

4. Enable port security:

```bash id="p9r3wk"
switchport port-security
```

5. Set maximum MAC addresses:

```bash id="x2k5lv"
switchport port-security maximum 1
```

6. Bind specific MAC address:

```bash id="b7n1qc"
switchport port-security mac-address 001A.2B3C.4D5E
```

7. Set violation action:

```bash id="v5m8dz"
switchport port-security violation shutdown
```

8. Enable interface:

```bash id="n9c4tr"
no shutdown
```

---

### 📊 Verification Commands

```bash id="z3p7lm"
show port-security interface fa0/1
show port-security
show port-security address
```

---

## 🧾 Full Cisco IOS Example Configuration

```bash id="c9v1xq"
interface FastEthernet0/1
switchport mode access
switchport access vlan 10
switchport port-security
switchport port-security maximum 1
switchport port-security mac-address 001A.2B3C.4D5E
switchport port-security violation shutdown
no shutdown
```

---

## ⭐ Best Practice Tips

* Static ARP entries on Windows **do not persist by default** — use PowerShell `Set-NetNeighbor -State Permanent` for persistence
* On switches, prefer **sticky MAC learning** (`port-security mac-address sticky`) when devices frequently change
* Use **restrict mode first** (log-only) before enabling **shutdown mode** in production environments
* Combine port security with **802.1X authentication (RADIUS)** for enterprise-grade security
* Always document static MAC bindings — undocumented entries complicate troubleshooting

---

## 🔧 Alternative Tools / Methods

* **802.1X with Microsoft Network Policy Server** — Standards-based authenticated network access control
* **Cisco Identity Services Engine** — Advanced NAC with identity-based policies
* **Aruba ClearPass** — Multi-vendor NAC for BYOD and enterprise environments

---

## 📌 Notes

This lab focuses on network layer security enforcement using MAC binding and switch port controls, helping prevent ARP spoofing, unauthorized access, and layer-2 network attacks in enterprise environments.

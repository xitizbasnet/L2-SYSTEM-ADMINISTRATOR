# 03 — Complex Network Troubleshooting

## 🎯 Lab Objectives

This lab focuses on structured troubleshooting of enterprise network issues using a **layered OSI model approach** and advanced diagnostic tools.

You will learn to:

* Diagnose connectivity issues systematically using OSI layers
* Use Windows and cross-platform network diagnostic tools
* Identify and resolve DNS, routing, and firewall-related failures

---

# 🧭 Task 1: Layer-by-Layer Connectivity Diagnosis (OSI Model Approach)

## 🔍 Methodology: Bottom-Up OSI Troubleshooting

> Start from the physical layer and move upward only after confirming each layer is functional.

---

## 🔌 L1 — Physical Layer

### Check Network Adapter Status

```powershell id="net1"
Get-NetAdapter
```

* Ensure status is: **Up**

### Verify via Device Manager

* Confirm NIC is detected and functioning

---

## 🔗 L2 — Data Link Layer

### Check ARP Cache

```cmd id="net2"
arp -a
```

### Switch-Level Validation (Managed Switch)

```text id="net3"
show interfaces
```

* Look for:

  * CRC errors
  * Drops
  * Interface flapping

---

## 🌐 L3 — Network Layer

### Verify IP Configuration

```cmd id="net4"
ipconfig /all
```

Check:

* IP address
* Subnet mask
* Default gateway
* DNS servers

---

### Connectivity Tests

```cmd id="net5"
ping 127.0.0.1
ping <gateway-ip>
```

---

## 🚚 L4 — Transport Layer

### Check Open Ports

```cmd id="net6"
netstat -an
```

* Verify service is **LISTENING**

---

### Application-Level Connectivity Test

```powershell id="net7"
Test-NetConnection <IP> <Port>
```

Examples:

```powershell id="net8"
Test-NetConnection -ComputerName 8.8.8.8 -Port 53
Test-NetConnection -ComputerName fileserver -Port 445
Test-NetConnection -ComputerName webserver -Port 443 -InformationLevel Detailed
```

---

### Route Tracing

```cmd id="net9"
tracert -d 10.0.1.1
```

```cmd id="net10"
pathping 10.0.1.1
```

> `pathping` provides packet loss analysis per hop

---

# 🧪 Task 2: DNS Troubleshooting

## 🧹 Step 1 — Flush DNS Cache

```cmd id="dns1"
ipconfig /flushdns
ipconfig /registerdns
```

---

## 🔎 Step 2 — Test Name Resolution

```cmd id="dns2"
nslookup server01.corp.local
```

---

## 🌐 Step 3 — Test Against Specific DNS Server

```cmd id="dns3"
nslookup server01.corp.local 10.0.0.1
```

---

## 📄 Step 4 — Check DNS Records

```cmd id="dns4"
nslookup -type=SOA corp.local
```

---

## 🔁 Step 5 — Verify Replication (Domain Controller)

```cmd id="dns5"
repadmin /showrepl
```

---

## 🔄 Step 6 — Reverse Lookup Test

```cmd id="dns6"
nslookup 10.0.0.50
```

---

## ⚡ PowerShell DNS Diagnostics (Advanced)

```powershell id="dns7"
Resolve-DnsName -Name server01.corp.local -Type A
Resolve-DnsName -Name corp.local -Type MX
Resolve-DnsName -Name 10.0.0.50 -Type PTR
```

---

# 📦 Task 3: Packet Capture & Analysis

## 📡 Step 1 — Start Network Trace

```cmd id="pkt1"
netsh trace start capture=yes tracefile=C:\trace.etl
```

---

## 🔁 Step 2 — Reproduce Issue

* Trigger the network failure scenario

---

## 🛑 Step 3 — Stop Capture

```cmd id="pkt2"
netsh trace stop
```

---

## 🔬 Step 4 — Analyze Capture

* Convert `.etl` to `.pcap` if required
* Analyze using:

  * Microsoft Network Monitor
  * Message Analyzer

---

## 🚨 Step 5 — Key Indicators

Look for:

* TCP RST packets (connection resets)
* ICMP unreachable messages
* Packet loss or filtering behavior

---

# 📌 Best Practice Tips

## 🧾 Network Troubleshooting Standards

* Always document **5-tuple data**:

  * Source IP
  * Destination IP
  * Source Port
  * Destination Port
  * Protocol

---

## 📉 Packet Loss Analysis

* Use `pathping` instead of `tracert` for intermittent issues

---

## 📏 MTU Troubleshooting

```cmd id="net11"
ping -f -l 1472 <destination>
```

* Detects fragmentation/MTU mismatch issues

---

## ⏰ Time Synchronization

* Verify NTP sync
* Clock skew > 5 minutes can break:

  * Kerberos authentication
  * Domain logins

---

## 🔁 NIC Reset Strategy

* Disable and re-enable network adapter before assuming hardware failure

---

# 🧰 Alternative Tools / Methods

* 🧵 Wireshark — deep packet inspection GUI tool
* 🐧 tcpdump — CLI packet capture for Linux systems
* 📊 SolarWinds NPM / PRTG — enterprise monitoring and alerting
* 📈 PingPlotter — visual latency and packet loss tracking

---

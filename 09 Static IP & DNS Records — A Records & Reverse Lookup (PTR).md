# 📘 09 | Static IP & DNS Records — A Records & Reverse Lookup (PTR)

---

## 🧭 Lab Objectives

* Create forward (A) DNS records for servers and services
* Create and verify Reverse Lookup Zones with PTR records
* Troubleshoot DNS record propagation and cache issues

---

## 🧩 Task 1: Create A and CNAME Records [Guided]

### 🖥️ Procedure (DNS Manager)

1. Open **DNS Manager** on your DNS server.
2. Expand **Forward Lookup Zones** → right-click **corp.local** → **New Host (A or AAAA)**.
3. Configure:

   * Name: **appserver01**
   * IP: **10.0.1.60**
   * Check: **Create associated pointer (PTR) record**
4. Click **Add Host**.
5. Verify: run `nslookup appserver01.corp.local` from client machine.
6. Add CNAME record:

   * Right-click zone → **New Alias (CNAME)**
   * Alias: **app**
   * FQDN: **appserver01.corp.local**
7. Test CNAME resolution:

   * `nslookup app.corp.local` → should resolve to **10.0.1.60**
8. Add MX Record (if mail server is required):

   * Right-click zone → **New Mail Exchanger (MX)**
   * Priority: **10**

---

## 💻 PowerShell: DNS Record Management

### ➕ Add A Record with PTR

```powershell id="x8p2ld"
Add-DnsServerResourceRecordA -ZoneName corp.local `
-Name 'appserver01' -IPv4Address '10.0.1.60' -CreatePtr
```

### 🔗 Add CNAME Record

```powershell id="7v9qk2"
Add-DnsServerResourceRecordCName -ZoneName corp.local `
-Name 'app' -HostNameAlias 'appserver01.corp.local'
```

---

## 🔍 Verify DNS Records

```powershell id="k3m8rz"
Resolve-DnsName -Name 'app.corp.local' -Type CNAME
Resolve-DnsName -Name 'appserver01.corp.local' -Type A
```

---

## 🧪 Task 2: Create Reverse Lookup Zone and PTR Records [Practice]

### 🖥️ Procedure (DNS Manager)

1. In **DNS Manager**, right-click **Reverse Lookup Zones** → **New Zone**.
2. Zone type: **Primary zone**, replicated to all DNS servers.
3. Network ID: **10.0.1** (creates zone `1.0.10.in-addr.arpa`).
4. Right-click new reverse zone → **New Pointer (PTR)**.
5. Configure:

   * Host IP number: **60** (last octet)
   * FQDN: **appserver01.corp.local**
6. Verify reverse lookup:

   * `nslookup 10.0.1.60` → should return **appserver01.corp.local**
7. Additional test:

   * `Resolve-DnsName -Name 10.0.1.60 -Type PTR`

---

## 🧪 Task 3: Troubleshoot Stale DNS Records [Challenge]

### ⚙️ Enable DNS Scavenging

1. Right-click DNS server → **Set Aging/Scavenging for All Zones**.
2. Configure:

   * Scavenge stale resource records: **Enabled**
   * No-refresh interval: **7 days**
   * Refresh interval: **7 days**

---

### 🔎 Identify & Manage Stale Records

3. Find stale records:

   * Look for A records with no timestamp (static) vs aged records
4. Force scavenging:

   * Right-click server → **Scavenge Stale Resource Records**
5. Find duplicate records:

```powershell id="p1d6xa"
Get-DnsServerResourceRecord -ZoneName corp.local -RRType A
```

6. Delete incorrect record:

```powershell id="t9v4nc"
Remove-DnsServerResourceRecord -ZoneName corp.local -Name 'oldserver' -RRType A
```

---

## 📊 DNS Troubleshooting Notes

* Use `dcdiag /test:DNS` to validate DNS health on domain controllers

---

## ⭐ Best Practice Tips

* Always create **PTR records alongside A records** — required for mail servers and security validation
* Use **DNS scavenging** to automatically remove stale records from decommissioned systems
* Set DNS TTL appropriately:

  * **5–15 minutes** for frequently changing records
  * **1–24 hours** for stable records
* Run `dcdiag /test:DNS` regularly for domain health validation
* Document all manually created DNS records in a **change log** (dynamic records are self-managed)

---

## 🔧 Alternative Tools / Methods

* **BIND9** — Industry-standard DNS server used in enterprise Linux environments
* **Unbound** — Lightweight validating DNS resolver and caching server
* **Azure Private DNS** / **Amazon Route 53 Private Hosted Zones** — Cloud-native DNS solutions for hybrid infrastructure

---

## 📌 Notes

This lab focuses on foundational DNS administration, including forward and reverse resolution, record lifecycle management, and troubleshooting techniques essential for enterprise network stability.

## 📖 Project Overview

This project demonstrates how to capture and analyse live network traffic using **Wireshark** on Linux. The goal is to understand how **application‑layer protocols** (HTTP, DNS, TLS) appear in packet captures, how to filter them, and what security (or lack of security) each protocol provides.

Three types of traffic were captured:

1. **Plain HTTP GET request** (non‑encrypted)
2. **DNS query and response** (domain name resolution)
3. **TLS handshake** (encrypted HTTPS setup)

---

## 🛠 Tools Used

- **Wireshark** (version 4.2.2) – packet capture and analysis
- **Ubuntu Linux** (running inside VMware Workstation)
- **Network interface** – `ens33` (bridged to host Wi‑Fi)
- **Terminal commands** – `nslookup`, `openssl s_client`

---

## 📋 Steps Performed

### 1. HTTP GET Request (Non‑HTTPS)
- Started a capture on interface `ens33`
- Visited `http://example.com` (explicitly **not** HTTPS)
- Applied display filter: `http.request.method == "GET"`
- Observed that all HTTP headers (Host, User‑Agent, etc.) are visible in plain text.

### 2. DNS Query and Response
- Started a fresh capture
- Ran `nslookup example.com` in the terminal
- Applied display filter: `dns`
- Noted the **Transaction ID** that links the query to its response, and the returned IP address(es).

### 3. TLS Handshake (HTTPS)
- Started a fresh capture
- Used the command:  
  `echo | openssl s_client -connect example.com:443 -servername example.com`
- Applied display filter: `tls`
- Examined the **Client Hello** (list of cipher suites, SNI = `example.com`) and **Server Hello** (chosen cipher suite).

---

## 🧠 Key Learnings

| Protocol | Encryption | Visibility in Wireshark |
|----------|------------|--------------------------|
| HTTP     | None       | Full request/response in plain text |
| DNS      | None       | Domain name and IP address clearly visible |
| TLS      | Handshake metadata only | SNI (domain name) is visible; actual application data is encrypted |

- **SNI (Server Name Indication)** leaks the domain name even when using HTTPS.
- **Display filters** (`http`, `dns`, `tls`) are essential to isolate specific traffic from background noise.
- **Command‑line tools** (like `openssl s_client`) can generate cleaner, interference‑free traffic for analysis compared to a web browser.

---


## ⚠️ Troubleshooting Notes

- **Firefox background connections** (to Mozilla telemetry/CDN) kept appearing in captures. Solved by using `openssl s_client` instead of a browser.
- **Interface selection** inside a VMware virtual machine required using `ens33`, not a `wlan` interface.
- Applying the **exact display filter syntax** (including double quotes for strings) is critical – otherwise the filter bar turns red.

---

## ✅ Conclusion

This project successfully demonstrated how to capture and interpret HTTP, DNS, and TLS traffic using Wireshark. It highlighted the visibility of unencrypted protocols (HTTP, DNS) and the limited visibility of encrypted TLS (only handshake metadata). These skills are foundational for network troubleshooting and security monitoring.

---

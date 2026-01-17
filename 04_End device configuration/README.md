# 🛠️ Cisco IOS Configuration & Device Hardening Implementation

## 📖 Executive Summary
This project outlines the foundational procedure for initializing and securing a Cisco Switch via a console connection. The objective was to transition a device from factory default to a secured state following industry-standard **Network Management** protocols.

## 📍 Environment & Connectivity
- **Management Workstation:** PC1 (Terminal Emulation)
- **Target Infrastructure:** Switch1 (Cisco IOS)
- **Interface:** Out-of-Band Management via Console Cable (RS-232/USB)

## 🚀 Technical Tasks Performed

### 1. Device Identification & Asset Management
- Defined a unique **Hostname** to ensure accurate identification within the management hierarchy.

### 2. Multi-Level Access Security
- **Console Port Hardening:** Secured the **User EXEC** mode with a password-protected login to mitigate unauthorized physical access risks.
- **Elevation Security:** Hardened the **Privileged EXEC** mode by implementing an **Enable Secret**.
  - *Strategic Note: Utilized the `secret` parameter to ensure MD5/SHA encryption for credentials stored in NVRAM.*

### 3. Regulatory & Legal Messaging
- Established a **Banner MOTD (Message of the Day)** to provide formal legal notification regarding unauthorized access attempts.

### 4. Configuration Persistence
- Committed the running configuration to the **Startup Configuration** to ensure operational continuity after power cycles.

## 💻 Command Reference Manual
| Objective | IOS Command Syntax |
| :--- | :--- |
| Global Configuration | `configure terminal` |
| Identity Management | `hostname [DeviceName]` |
| Physical Port Security | `line con 0` -> `password [key]` -> `login` |
| Encrypted Elevation | `enable secret [key]` |
| Access Warning | `banner motd # [Policy Message] #` |
| Data Persistence | `copy running-config startup-config` |

---
*Documentation developed for **Network Infrastructure Portfolio**.*

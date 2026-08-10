# Module 02: System Performance & UI Registry Tweaks

[![GitHub](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=for-the-badge&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)
[![Windows](https://img.shields.io/badge/OS-Windows%2010%2F11-0078D6?style=for-the-badge&logo=windows)](https://github.com/toannguyenitoz/windows-registry-guide)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](../LICENSE)

---

[⬅️ Back to Main Index](../README.md)

---

## 📌 Overview

This module documents high-impact registry tweaks designed to optimize Windows 10/11 system responsiveness, eliminate UI animation delays, disable non-essential startup buffering, and optimize network responsiveness.

---

## 🚀 1. Speeding Up Windows Interface & Menus

### Disable Startup App Delay
Windows introduces a mandatory delay at logon to allow background services to initialize before launching startup apps.

* **Key:** `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Serialize`
* **Value Name:** `StartupDelayInMSec`
* **Type:** `REG_DWORD`
* **Data:** `0` (Disables delay)

### Accelerate Context & Start Menu Animation Speed
Reduces the delay before context menus and sub-menus pop open.

* **Key:** `HKCU\Control Panel\Desktop`
* **Value Name:** `MenuShowDelay`
* **Type:** `REG_SZ`
* **Data:** `0` (Instant) to `100` (Fast - Recommended: `50`)

---

## ⚡ 2. Gaming & Network Responsiveness Tweaks

### Disable Network Throttling Index
Windows throttles non-multimedia network traffic by default when audio/video applications are running.

* **Key:** `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile`
* **Value Name:** `NetworkThrottlingIndex`
* **Type:** `REG_DWORD`
* **Data:** `ffffffff` (Hex: `0xFFFFFFFF` - Disables network throttling)

### Prioritize Gaming / Interactive Applications
Optimizes GPU and CPU scheduling priority for active foreground applications.

* **Key:** `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile\Tasks\Games`
* **Values:**
  * `GPU Priority` (`REG_DWORD`) = `8`
  * `Priority` (`REG_DWORD`) = `6`
  * `Scheduling Category` (`REG_SZ`) = `"High"`

---

## 🛑 3. Disabling Telemetry & Background Activity

### Minimize Diagnostic Telemetry
Reduces background data transmission to Microsoft telemetry servers.

* **Key:** `HKLM\SOFTWARE\Policies\Microsoft\Windows\DataCollection`
* **Value Name:** `AllowTelemetry`
* **Type:** `REG_DWORD`
* **Data:** `0` (Security/Off - Enterprise) or `1` (Basic)

---

## 💾 Ready-to-Use `.reg` Performance Script

Save the block below as `performance_tweaks.reg` and double-click to apply:

```registry
Windows Registry Editor Version 5.00

; Disable Windows Startup Application Delay
[HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Serialize]
"StartupDelayInMSec"=dword:00000000

; Speed up menu animations
[HKEY_CURRENT_USER\Control Panel\Desktop]
"MenuShowDelay"="50"

; Disable Network Throttling
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Multimedia\SystemProfile]
"NetworkThrottlingIndex"=dword:ffffffff
```

---

## 👨‍💻 About the Author

**Toan Nguyen**  
*IT Support | Microsoft 365 | Windows Administrator | Cloud Computing*  
📍 Adelaide, South Australia  

[![GitHub Profile](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=flat-square&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn Profile](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)

---

© 2026 Toan Nguyen. Released under the [MIT License](../LICENSE).

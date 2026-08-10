# Module 04: Troubleshooting & System Repair Registry Keys

[![GitHub](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=for-the-badge&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)
[![Windows](https://img.shields.io/badge/OS-Windows%2010%2F11-0078D6?style=for-the-badge&logo=windows)](https://github.com/toannguyenitoz/windows-registry-guide)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](../LICENSE)

---

[⬅️ Back to Main Index](../README.md)

---

## 📌 Overview

When Windows services, network interfaces, or desktop shell extensions become corrupted, specific registry key repairs can restore system functionality without requiring a full OS reinstallation.

---

## 🛠️ 1. Restoring Classic Context Menu in Windows 11

Windows 11 replaced the traditional right-click context menu with a simplified layout. Administrators can restore the full classic Windows 10 right-click menu natively via the registry.

* **Key:** `HKCU\Software\Classes\CLSID\{86ca3570-4fa4-4d79-8f29-d50d5869d514}\InprocServer32`
* **Value Name:** `(Default)`
* **Type:** `REG_SZ`
* **Data:** `""` (Empty string)

*To apply immediately:* Restart `explorer.exe` process via Task Manager or PowerShell (`Stop-Process -Name explorer -Force`).

---

## 🔧 2. Fixing Broken Windows Update Policies

When WSUS or third-party patch management tools leave residual lock policies, Windows Update may display: *"Some settings are managed by your organization"*.

* **Key:** `HKLM\SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate`
* **Remediation:** Delete subkeys `AU` and values `WUServer`, `WUStatusServer` to force Windows Update back to public Microsoft Update servers.

---

## 🌐 3. Resetting Network Stack & Winsock Catalog Keys

If network adapters fail to pull IP addresses due to registry corruption:

* **Winsock Registry Keys:**
  * `HKLM\SYSTEM\CurrentControlSet\Services\Winsock`
  * `HKLM\SYSTEM\CurrentControlSet\Services\Winsock2`
* **Remediation CLI Command:**
  ```cmd
  netsh winsock reset
  netsh int ip reset
  ```

---

## 📁 4. Repairing Corrupted `.exe` File Associations

When executable files (`.exe`) fail to open or launch with Notepad:

* **Key:** `HKCR\.exe`
* **Value Name:** `(Default)` = `"exefile"`
* **Key:** `HKCR\exefile\shell\open\command`
* **Value Name:** `(Default)` = `"%1" %*`

---

## 👨‍💻 About the Author

**Toan Nguyen**  
*IT Support | Microsoft 365 | Windows Administrator | Cloud Computing*  
📍 Adelaide, South Australia  

[![GitHub Profile](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=flat-square&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn Profile](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)

---

© 2026 Toan Nguyen. Released under the [MIT License](../LICENSE).

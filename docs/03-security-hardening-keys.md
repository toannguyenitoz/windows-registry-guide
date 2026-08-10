# Module 03: Security Hardening & Enterprise Policies

[![GitHub](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=for-the-badge&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)
[![Windows](https://img.shields.io/badge/OS-Windows%2010%2F11-0078D6?style=for-the-badge&logo=windows)](https://github.com/toannguyenitoz/windows-registry-guide)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](../LICENSE)

---

[⬅️ Back to Main Index](../README.md)

---

## 📌 Overview

Security hardening via the Windows Registry enforces critical defensive policies across workstations and servers. These settings map directly to Group Policy Object (GPO) policies defined by CIS Benchmarks and Microsoft Security Baselines.

---

## 🔐 1. User Account Control (UAC) Hardening

### Enforce Secure Desktop Prompting
Forces UAC prompts onto the isolated Secure Desktop to prevent malware spoofing.

* **Key:** `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
* **Value Name:** `PromptOnSecureDesktop`
* **Type:** `REG_DWORD`
* **Data:** `1` (Enabled)

### Block Admin Approval Mode Elevation Requests for Standard Users
Automatically denies elevation requests for non-admin accounts.

* **Key:** `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System`
* **Value Name:** `ConsentPromptBehaviorUser`
* **Type:** `REG_DWORD`
* **Data:** `0` (Automatically deny elevation requests)

---

## 🛡️ 2. Credential Protection (LSA & LSASS Protection)

### Enable LSA Protection against Mimikatz / Credential Dumping
Prevents non-protected processes from injecting code or reading memory from `lsass.exe`.

* **Key:** `HKLM\SYSTEM\CurrentControlSet\Control\Lsa`
* **Value Name:** `RunAsPPL`
* **Type:** `REG_DWORD`
* **Data:** `1` (Enable LSA protection with UEFI variable lock)

---

## 🌐 3. Remote Desktop & Network Hardening

### Enforce Network Level Authentication (NLA) for RDP
Requires remote users to authenticate before an RDP session is initiated.

* **Key:** `HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp`
* **Value Name:** `UserAuthentication`
* **Type:** `REG_DWORD`
* **Data:** `1` (Enforce NLA)

### Disable Insecure Legacy SMBv1 Protocol
SMBv1 is susceptible to WannaCry and EternalBlue exploits.

* **Key:** `HKLM\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters`
* **Value Name:** `SMB1`
* **Type:** `REG_DWORD`
* **Data:** `0` (Disable SMBv1)

---

## 💾 4. Data Loss Prevention (USB Storage Controls)

### Block Write Access to USB Removable Drives
Prevents unauthorized data exfiltration to external USB flash drives.

* **Key:** `HKLM\SYSTEM\CurrentControlSet\Control\StorageDevicePolicies`
* **Value Name:** `WriteProtect`
* **Type:** `REG_DWORD`
* **Data:** `1` (Enable Write Protection on Removable Devices)

---

## 🛡️ Hardening `.reg` Template

```registry
Windows Registry Editor Version 5.00

; Enforce Secure Desktop for UAC
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System]
"PromptOnSecureDesktop"=dword:00000001

; Enable LSA Protection against credential dumping
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Lsa]
"RunAsPPL"=dword:00000001

; Disable SMBv1
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters]
"SMB1"=dword:00000000
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

# Module 01: Windows Registry Fundamentals & Architecture

[![GitHub](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=for-the-badge&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)
[![Windows](https://img.shields.io/badge/OS-Windows%2010%2F11-0078D6?style=for-the-badge&logo=windows)](https://github.com/toannguyenitoz/windows-registry-guide)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](../LICENSE)

---

[⬅️ Back to Main Index](../README.md)

---

## 📌 Overview

The Windows Registry is a centralized, hierarchical database used by Microsoft Windows to store configuration settings for the operating system, hardware devices, installed applications, and user preferences.

---

## 🏛️ The 5 Root Hives (Root Keys)

Although `regedit.exe` displays five root keys, two of them are logical aliases of subkeys within the primary hives:

```
Computer\
├── HKEY_CLASSES_ROOT (HKCR)        --> Alias of HKLM\SOFTWARE\Classes & HKCU\Software\Classes
├── HKEY_CURRENT_USER (HKCU)        --> Alias of HKEY_USERS\<User-SID>
├── HKEY_LOCAL_MACHINE (HKLM)       --> Core Machine Hardware & OS Settings
├── HKEY_USERS (HKU)                --> Contains profiles for all active users
└── HKEY_CURRENT_CONFIG (HKCC)      --> Alias of HKLM\SYSTEM\CurrentControlSet\Hardware Profiles\Current
```

### Detailed Breakdown of Hives

| Abbreviation | Hive Name | Scope | Key Use Cases |
| :--- | :--- | :--- | :--- |
| **`HKLM`** | `HKEY_LOCAL_MACHINE` | System-wide (All users) | Boot settings, installed software, hardware configuration, security policies. |
| **`HKCU`** | `HKEY_CURRENT_USER` | Current logged-in user | Desktop background, user environment variables, user-specific app settings. |
| **`HKCR`** | `HKEY_CLASSES_ROOT` | File Associations & COM objects | Maps file extensions (`.txt`, `.pdf`) to application handlers and OLE/COM registration. |
| **`HKU`** | `HKEY_USERS` | All loaded user profiles | Contains `.DEFAULT` profile, system service profiles (S-1-5-18, S-1-5-19, S-1-5-20), and user SIDs. |
| **`HKCC`** | `HKEY_CURRENT_CONFIG` | Volatile hardware profile | Stores runtime hardware configuration settings generated at boot time. |

---

## 📊 Registry Value Data Types

Registry values store data under specific keys. Each value has a name, a data type, and the data itself.

| Data Type | Constant Name | Description | Example |
| :--- | :--- | :--- | :--- |
| **String** | `REG_SZ` | Fixed-length text string. | `"C:\\Program Files\\App"` |
| **Expandable String** | `REG_EXPAND_SZ` | Text string containing environment variables expanded at runtime. | `"%SystemRoot%\\System32"` |
| **Binary** | `REG_BINARY` | Raw binary byte stream. | `00 1F 4A D3` |
| **DWORD (32-bit)** | `REG_DWORD` | 32-bit integer (Hexadecimal or Decimal). Commonly used for booleans (`0` = Disabled, `1` = Enabled). | `0x00000001` (1) |
| **QWORD (64-bit)** | `REG_QWORD` | 64-bit integer used for large numerical values or 64-bit timestamps. | `0x0000000000000064` (100) |
| **Multi-String** | `REG_MULTI_SZ` | Array of strings separated by null characters (`\0`). | `"Line1\0Line2\0Line3"` |

---

## ⚙️ 64-bit Windows & WOW6432Node Redirection

On 64-bit editions of Windows, 32-bit applications run under the **WOW64** (Windows 32-bit on Windows 64-bit) subsystem. 

To prevent conflicts between 32-bit and 64-bit application settings, Windows uses **Registry Redirection**:
- 64-bit applications read/write directly to: `HKLM\SOFTWARE`
- 32-bit applications are transparently redirected to: `HKLM\SOFTWARE\WOW6432Node`

> [!NOTE]
> When writing PowerShell scripts or deployment packages for 32-bit applications on 64-bit OS, ensure you target the `WOW6432Node` path if applicable.

---

## 👨‍💻 About the Author

**Toan Nguyen**  
*IT Support | Microsoft 365 | Windows Administrator | Cloud Computing*  
📍 Adelaide, South Australia  

[![GitHub Profile](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=flat-square&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn Profile](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)

---

© 2026 Toan Nguyen. Released under the [MIT License](../LICENSE).

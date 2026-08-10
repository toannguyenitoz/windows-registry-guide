# Windows Registry (Regedit) Complete Guide & Reference

[![GitHub](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=for-the-badge&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)
[![Windows](https://img.shields.io/badge/OS-Windows%2010%2F11-0078D6?style=for-the-badge&logo=windows)](https://github.com/toannguyenitoz/windows-registry-guide)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](LICENSE)

---

## 📌 Overview

Welcome to the **Windows Registry (Regedit) Complete Guide**. This repository provides an enterprise-ready, structured reference for IT administrators, system engineers, and Windows power users to understand, modify, harden, and automate Windows Registry settings safely.

The Windows Registry is a hierarchical database that stores low-level settings for the Microsoft Windows operating system and for applications that opt to use the registry.

> [!WARNING]
> **Safety Disclaimer:** Incorrectly editing the Windows Registry can cause critical system instability or prevent Windows from starting. Always create a System Restore point or back up your registry keys (`.reg`) before applying modifications.

---

## 📚 Table of Contents (TOC)

| Module | Topic | Description |
| :--- | :--- | :--- |
| **[01. Fundamentals](docs/01-registry-fundamentals.md)** | Architecture & Data Types | Hives (`HKLM`, `HKCU`), registry keys, value types (`REG_SZ`, `REG_DWORD`, etc.) |
| **[02. Performance Tweaks](docs/02-performance-system-tweaks.md)** | System & UI Optimization | Disabling telemetry, menu delays, startup delays, and visual animations |
| **[03. Security Hardening](docs/03-security-hardening-keys.md)** | Enterprise Security Policies | Hardening UAC, Defender, RDP, LSA Protection, USB access, and network policies |
| **[04. Troubleshooting & Repairs](docs/04-troubleshooting-repairs.md)** | System Recovery Keys | Fixing network stacks, Windows Update glitches, file association corruptions |
| **[05. Automation & Scripting](docs/05-backup-automation-scripting.md)** | PowerShell & `.reg` Scripts | Exporting/Importing `.reg` files, PowerShell `Set-ItemProperty` and `reg.exe` CLI |

---

## 🔑 Quick Reference: High-Impact Registry Keys

| Category | Key Path | Value Name | Recommended Setting |
| :--- | :--- | :--- | :--- |
| **System** | `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\SystemRestore` | `RPSessionInterval` | `0` (Disable Restore limits) |
| **UI** | `HKCU\Control Panel\Desktop` | `MenuShowDelay` | `0` to `100` (Faster menu opening) |
| **Security** | `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System` | `EnableLUA` | `1` (Enable UAC protection) |
| **Network** | `HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters` | `EnableICMPRedirect` | `0` (Hardening against ICMP redirects) |
| **Explorer** | `HKCU\Software\Microsoft\Windows\CurrentVersion\Explorer\Advanced` | `Hidden` | `1` (Show hidden files) |

---

## 🛡️ Best Practices for Registry Administration

1. **Backup First:** Always run `reg export HKLM\Path\To\Key backup.reg` before making edits.
2. **Use Group Policy When Available:** For domain environments, deploy GPOs instead of raw registry modifications whenever possible.
3. **Automate with PowerShell:** Prefer `Set-ItemProperty` and `New-ItemProperty` over manual GUI edits in production environments.

---

## 👨‍💻 About the Author

**Toan Nguyen**  
*IT Support | Microsoft 365 | Windows Administrator | Cloud Computing*  
📍 Adelaide, South Australia  

Connect with me on social platforms:
- 🐙 **GitHub:** [@toannguyenitoz](https://github.com/toannguyenitoz)
- 💼 **LinkedIn:** [Toan Nguyen](https://www.linkedin.com/in/toan-nguyen-it-oz/)

---

© 2026 Toan Nguyen. Released under the [MIT License](LICENSE).

# Module 05: Backup, Automation & PowerShell Scripting

[![GitHub](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=for-the-badge&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)
[![Windows](https://img.shields.io/badge/OS-Windows%2010%2F11-0078D6?style=for-the-badge&logo=windows)](https://github.com/toannguyenitoz/windows-registry-guide)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](../LICENSE)

---

[⬅️ Back to Main Index](../README.md)

---

## 📌 Overview

Automating registry modifications allows systems administrators to deploy consistent configurations across hundreds of endpoints via Intune, SCCM, or PowerShell Remoting without manual GUI intervention.

---

## 💾 1. Backing Up & Exporting Registry Keys

### Command Line (`reg.exe`)
```cmd
:: Export an entire subkey tree to a .reg file
reg export "HKLM\SOFTWARE\Policies\Microsoft\Windows" "C:\Backups\WindowsPolicies.reg" /y

:: Import a .reg file silently
reg import "C:\Backups\WindowsPolicies.reg"
```

---

## ⚡ 2. PowerShell Registry Management (`HKLM:` & `HKCU:`)

PowerShell treats the Windows Registry as a drive provider (`HKLM:`, `HKCU:`).

### Checking if a Registry Path Exists
```powershell
$RegPath = "HKLM:\SOFTWARE\Policies\Microsoft\Windows\System"
if (-not (Test-Path $RegPath)) {
    New-Item -Path $RegPath -Force | Out-Null
}
```

### Reading a Value (`Get-ItemPropertyValue`)
```powershell
$Value = Get-ItemPropertyValue -Path "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion" -Name "ProductName"
Write-Host "Windows Product Name: $Value"
```

### Creating or Updating a Value (`Set-ItemProperty`)
```powershell
# Enforce LSA Protection
Set-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Lsa" -Name "RunAsPPL" -Value 1 -Type DWord
```

---

## 📜 3. Production PowerShell Automation Script

Save and run the following script as Administrator to automate multi-key deployment safely:

```powershell
<#
.SYNOPSIS
    Automated Windows Registry Security & Performance Hardening Script.
.AUTHOR
    Toan Nguyen (toannguyenitoz@gmail.com)
#>

# Requires Administrator Privileges
if (-not ([Security.Principal.WindowsPrincipal][Security.Principal.WindowsIdentity]::GetCurrent()).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)) {
    Write-Error "This script must be executed as Administrator!"
    exit 1
}

Write-Host "[+] Applying Registry Hardening Policies..." -ForegroundColor Green

# 1. Enable UAC Secure Desktop
$UacPath = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System"
Set-ItemProperty -Path $UacPath -Name "PromptOnSecureDesktop" -Value 1 -Type DWord

# 2. Disable SMBv1
$SmbPath = "HKLM:\SYSTEM\CurrentControlSet\Services\LanmanServer\Parameters"
Set-ItemProperty -Path $SmbPath -Name "SMB1" -Value 0 -Type DWord

Write-Host "[✔] Registry Hardening Completed Successfully!" -ForegroundColor Cyan
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

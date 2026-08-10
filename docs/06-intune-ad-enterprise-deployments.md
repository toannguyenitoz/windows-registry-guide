# Module 06: Enterprise App Deployment & MDM Registry Keys (Intune & Active Directory)

[![GitHub](https://img.shields.io/badge/GitHub-toannguyenitoz-181717?style=for-the-badge&logo=github)](https://github.com/toannguyenitoz)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-Toan%20Nguyen-0A66C2?style=for-the-badge&logo=linkedin)](https://www.linkedin.com/in/toan-nguyen-it-oz/)
[![Windows](https://img.shields.io/badge/OS-Windows%2010%2F11-0078D6?style=for-the-badge&logo=windows)](https://github.com/toannguyenitoz/windows-registry-guide)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge)](../LICENSE)

---

[⬅️ Back to Main Index](../README.md)

---

## 📌 Overview

In enterprise IT environments managed by **Microsoft Intune (Cloud MDM)** and **Active Directory Domain Services (On-Premises GPO)**, application deployments, enrollment states, and compliance policies leave extensive registry footprints.

Understanding these registry paths allows IT Administrators and Systems Engineers to troubleshoot failed app installations, verify GPO application, and audit software deployment states directly on client endpoints.

---

## ☁️ 1. Microsoft Intune (MDM & IME) Application Registry Keys

### A. Intune Management Extension (IME) & Win32 App Tracking
When Intune deploys Win32 Apps (`.intunewin` / `.msi` / `.exe`), the Intune Management Extension agent logs app detection rules, installation exit codes, and enforcement states in the registry.

* **Registry Path:** `HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\Win32Apps`
* **Subkeys Structure:** `HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\Win32Apps\<User-SID>\<App-GUID>`
* **Key Values:**
  * `ComplianceState`: Installation compliance (`1` = Installed/Compliant, `3` = Failed)
  * `DownloadState`: Content download progress (`0` = Not Started, `1` = Downloading, `2` = Downloaded)
  * `EnforcementState`: Current execution state
  * `ErrorCode`: Exit code returned by installer (e.g. `0` = Success, `1603` = MSI Fatal Error)

### B. MDM Device Enrollment & Policy Manager
Tracks MDM enrollment status (Azure AD Join / Hybrid AD Join) and deployed Intune Configuration Profiles.

* **Enrollment Path:** `HKLM\SOFTWARE\Microsoft\Enrollments\<Enrollment-GUID>`
  * `UPN`: Enrolled user principal name (`user@company.com`)
  * `ProviderID`: `"MS DM Process"` (Microsoft Intune Provider)
* **Policy Manager Path:** `HKLM\SOFTWARE\Microsoft\PolicyManager\current\device`
  * Contains effective MDM settings deployed via Intune Configuration Profiles (BitLocker, Wi-Fi, Defender, Device Restrictions).

---

## 🏢 2. Active Directory (AD) & Group Policy (GPO) App Deployments

### A. GPO Software Installation (MSI AppManagement)
When applications are deployed via Active Directory Group Policy Software Installation (Computer or User Configuration):

* **Computer Apps Path:** `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\AppManagement`
* **User Apps Path:** `HKCU\Software\Microsoft\Windows\CurrentVersion\Group Policy\AppManagement`
* **Key Values:**
  * `Deployment Name`: Name of the GPO software package.
  * `GPO ID`: GUID of the Active Directory Group Policy Object.
  * `Path`: Network UNC path to the `.msi` installer package (e.g. `\\domain.local\NETLOGON\Software\app.msi`).

### B. Group Policy Applied History & Extensions
* **GPO History Path:** `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\History`
* **Client Side Extensions (CSE):** `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon\GPExtensions`
  * `{c6895035-8867-11d2-887e-00c04f79e9d0}`: Software Installation CSE

---

## 📦 3. Enterprise Software Policies Configured via Intune & AD GPO

### A. Microsoft 365 Apps / Office Enterprise Settings
* **Registry Path:** `HKLM\SOFTWARE\Policies\Microsoft\Office\16.0\Common`
* **Registry Path:** `HKLM\SOFTWARE\Policies\Microsoft\Office\16.0\Outlook`
* **Key Use Cases:** Enforcing Update Channels (Current, Monthly Enterprise, Semi-Annual), blocking untrusted macros, setting default OST cache sizes.

### B. Google Chrome & Microsoft Edge Enterprise Policies
* **Microsoft Edge:** `HKLM\SOFTWARE\Policies\Microsoft\Edge`
* **Google Chrome:** `HKLM\SOFTWARE\Policies\Google\Chrome`
* **Key Use Cases:** Restricting browser extensions, enforcing enterprise homepage/newtab URLs, disabling password saving, turning on force-HTTPS.

### C. OneDrive for Business Enterprise Sync Policies
* **Registry Path:** `HKLM\SOFTWARE\Policies\Microsoft\OneDrive`
* **Key Values:**
  * `KFMOptInWithWizard`: Enforce Known Folder Move (Desktop, Documents, Pictures backup to OneDrive).
  * `SilentAccountConfig`: Automatically sign in users to OneDrive using Windows credentials.

---

## 📜 4. PowerShell Script: Inspect Intune & GPO App Deployment Status

```powershell
<#
.SYNOPSIS
    Inspects Intune Win32 App deployment states and GPO Software installation keys.
.AUTHOR
    Toan Nguyen (toannguyenitoz@gmail.com)
#>

Write-Host "==========================================================" -ForegroundColor Cyan
Write-Host "   INTUNE & AD GPO SOFTWARE DEPLOYMENT AUDIT TOOL        " -ForegroundColor Cyan
Write-Host "==========================================================" -ForegroundColor Cyan

# 1. Inspect Intune Win32 Apps
$IntunePath = "HKLM:\SOFTWARE\Microsoft\IntuneManagementExtension\Win32Apps"
if (Test-Path $IntunePath) {
    Write-Host "`n[+] Intune Win32 Apps Found:" -ForegroundColor Green
    Get-ChildItem -Path $IntunePath -Recurse | Where-Object { $_.Property -contains "ComplianceState" } | ForEach-Object {
        [PSCustomObject]@{
            AppGUID         = $_.PSChildName
            ComplianceState = Get-ItemPropertyValue -Path $_.PSPath -Name "ComplianceState"
            ErrorCode       = Get-ItemPropertyValue -Path $_.PSPath -Name "ErrorCode" -ErrorAction SilentlyContinue
        }
    } | Format-Table -AutoSize
} else {
    Write-Host "`n[-] No Intune Win32 App registry entries found on this machine." -ForegroundColor Yellow
}

# 2. Inspect Active Directory GPO AppManagement
$GpoAppPath = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\AppManagement"
if (Test-Path $GpoAppPath) {
    Write-Host "`n[+] Active Directory GPO Deployed Software Found:" -ForegroundColor Green
    Get-ChildItem -Path $GpoAppPath | ForEach-Object {
        $Name = Get-ItemPropertyValue -Path $_.PSPath -Name "Deployment Name" -ErrorAction SilentlyContinue
        $Path = Get-ItemPropertyValue -Path $_.PSPath -Name "Path" -ErrorAction SilentlyContinue
        Write-Host "  - App: $Name | MSI Source: $Path" -ForegroundColor White
    }
} else {
    Write-Host "`n[-] No GPO AppManagement entries found." -ForegroundColor Yellow
}
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

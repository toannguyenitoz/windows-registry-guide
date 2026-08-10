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

This module details the **dual-location App ID architecture**, the **Group Policy Resultant State (`GPO-List` / GPR State)**, Intune SideCar policies, and **Intune Detection Rules** interaction with the Windows Registry.

---

## 🔑 1. Dual-Location App ID Architecture in Windows Registry

In both Intune and Active Directory, application deployments maintain **two separate registry locations**: one for **management policy/enforcement tracking** and another for **installed software inventory/MSI installer registration**.

### ☁️ A. Intune Dual App ID Registry Locations

| Registry Location Type | Registry Path | Purpose & Data Stored |
| :--- | :--- | :--- |
| **Location 1: IME Agent Runtime & Policy Tracking** | `HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\Win32Apps\<User-SID>\<Intune-App-GUID>` | Tracks real-time deployment execution, `ComplianceState`, `DownloadState`, `EnforcementState`, and installer exit code (`ErrorCode`). |
| **Location 2: MDM Desktop App Inventory & Uninstall Keys** | `HKLM\SOFTWARE\Microsoft\EnterpriseDesktopAppManagement\<Enrollment-GUID>\MSI\<App-GUID>`<br>*and*<br>`HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{MSI-Product-Code}` | Stores installed software inventory, MSI Product Codes, publisher metadata, display names, and uninstall strings. |

---

### 🏢 B. Active Directory GPO Dual App ID Registry Locations

| Registry Location Type | Registry Path | Purpose & Data Stored |
| :--- | :--- | :--- |
| **Location 1: GPO AppManagement Deployment Registration** | `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\AppManagement\{GPO-App-GUID}` | Stores GPO policy metadata, deployment name, GPO GUID, and network UNC source path (`\\domain.local\NETLOGON\app.msi`). |
| **Location 2: Windows Installer Engine & Compressed MSI Hives** | `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Installer\UserData\S-1-5-18\Products\<Compressed-MSI-GUID>`<br>*and*<br>`HKLM\SOFTWARE\Classes\Installer\Products\<Compressed-MSI-GUID>` | Low-level Windows Installer database storing installed MSI features, components, transforms, and patch cache. |

---

## 🏛️ 2. Group Policy Resultant State (`GPO-List` / GPR State) & Intune SideCar Policies

In addition to app deployment hives, Windows maintains **Resultant State of Policy (RSoP)** registry keys that log applied GPO IDs and Intune SideCar script policies.

### 🏢 A. Active Directory `GPO-List` (GPR / GPO Resultant State)
When Group Policies apply to a machine or user, Windows caches the GPO IDs, display names, and SYSVOL file paths under:

* **Machine GPO List Path:** `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\State\Machine\GPO-List`
* **User GPO List Path:** `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\State\<User-SID>\GPO-List`

**Key Properties Stored per GPO Subkey:**
- `GPO-ID`: The unique GUID of the Group Policy Object (e.g., `{35378EAC-683F-11D2-A89A-00C04FBBCFA2}`).
- `DisplayName`: Human-readable GPO name defined in Active Directory (e.g., *"Deploy Chrome Enterprise Policy"*).
- `FileSysPath`: The Domain Controller SYSVOL path containing policy files (`\\domain.local\SysVol\domain.local\Policies\{GUID}`).
- `Extensions`: List of Client-Side Extension (CSE) GUIDs invoked by the GPO.

### ☁️ B. Intune SideCar Policies Repository
Intune's management agent (codenamed **SideCar**) stores remediation scripts, PowerShell scripts, and custom policy execution states under:

* **SideCar App Status:** `HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\SideCarPolicies\StatusService\App`
* **SideCar Script Execution:** `HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\SideCarPolicies\Scripts\Execution`
* **SideCar Health Scripts:** `HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\SideCarPolicies\SideCarPoliciesState`

---

## 🔍 3. How to Find Intune App IDs & Match Endpoint Registry Keys

To troubleshoot a specific application deployed via Intune, you must match the **Intune Portal App ID** with the **Endpoint Registry App GUID**.

### Step 1: Find the App ID in Microsoft Intune Admin Center
1. Sign in to the **[Microsoft Intune Admin Center](https://intune.microsoft.com)**.
2. Navigate to **Apps** $\rightarrow$ **All apps**.
3. Select your target Win32 or Line-of-Business app (e.g., *Google Chrome Enterprise*).
4. Inspect the browser address URL:
   ```text
   https://intune.microsoft.com/#view/Microsoft_Intune_Apps/SettingsMenu/~/0/appId/9f8e7d6c-5b4a-3f2e-1d0c-9b8a7f6e5d4c
   ```
5. The string after `/appId/` (`9f8e7d6c-5b4a-3f2e-1d0c-9b8a7f6e5d4c`) is the **Intune App GUID**.

### Step 2: Locate the App ID on the Client Machine
Open `regedit.exe` on the endpoint and navigate to:
```registry
HKLM\SOFTWARE\Microsoft\IntuneManagementExtension\Win32Apps\<User-SID>\9f8e7d6c-5b4a-3f2e-1d0c-9b8a7f6e5d4c
```
Under this key, you can view the exact installation status, exit code, and detection evaluation logs.

---

## 🎯 4. Intune Win32 App Detection Rules & Registry Mapping

Intune uses **Detection Rules** to evaluate whether an app is already installed *before* running the installer, and to verify success *after* running the installer.

```
+-----------------------------------------------------------------------+
|                       Intune Detection Cycle                          |
|                                                                       |
|  [Intune IME Agent] ---> Evaluates Detection Rule ---> [Registry/MSI] |
|                                                                       |
|   - If Detected (True)  --> Skip Installation (Compliant)              |
|   - If Not Detected     --> Execute Installer Script                   |
|   - Re-evaluate Rule    --> Verify Installation (Exit Code 0)          |
+-----------------------------------------------------------------------+
```

### Detection Rule Types & Registry Keys

| Detection Rule Type | Evaluation Logic | Target Registry / System Path |
| :--- | :--- | :--- |
| **1. MSI Detection Rule** | Checks if the MSI Product Code GUID exists in the registry. | `HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\{Product-Code}` *or* `HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Uninstall\{Product-Code}` |
| **2. Registry Detection Rule** | Evaluates Key existence, Value existence, or String/DWORD version comparison. | Key: `HKLM\SOFTWARE\Vendor\AppName`<br>Value Name: `Version`<br>Operator: `>=` `2.4.0` |
| **3. File / Folder Detection Rule** | Checks file path, file version, or date modified on disk. | e.g. `C:\Program Files\Vendor\App.exe` |
| **4. Custom PowerShell Script** | Executes `.ps1` script. Output to STDOUT + Exit Code `0` = Installed. | Script queries custom registry keys using `Get-ItemProperty`. |

### Troubleshooting Detection Failures
If Intune reports `Error 0x87D13B5D (User check failed / Detection rule not met)`:
1. Open the IME Log: `C:\ProgramData\Microsoft\IntuneManagementExtension\Logs\IntuneManagementExtension.log`.
2. Search for keyword `[Win32App] Detection check`.
3. Verify if the Registry key path or MSI Product Code defined in Intune matches the actual endpoint registry path exactly (including 32-bit `WOW6432Node` vs 64-bit paths).

---

## 📜 5. Comprehensive PowerShell Script: Audit Intune & GPO App IDs

```powershell
<#
.SYNOPSIS
    Audits dual-location Intune App GUIDs, GPO-List (GPR State), Active Directory App IDs, and SideCar Policies.
.AUTHOR
    Toan Nguyen (toannguyenitoz@gmail.com)
#>

Write-Host "=================================================================" -ForegroundColor Cyan
Write-Host "  INTUNE & AD GPO DUAL-LOCATION & GPO-LIST AUDIT TOOL            " -ForegroundColor Cyan
Write-Host "=================================================================" -ForegroundColor Cyan

# 1. Location 1: Intune IME Win32 App Enforcement Keys
$IntuneImePath = "HKLM:\SOFTWARE\Microsoft\IntuneManagementExtension\Win32Apps"
if (Test-Path $IntuneImePath) {
    Write-Host "`n[+] Intune Location 1 (IME Policy Enforcement & Exit Codes):" -ForegroundColor Green
    Get-ChildItem -Path $IntuneImePath -Recurse | Where-Object { $_.Property -contains "ComplianceState" } | ForEach-Object {
        [PSCustomObject]@{
            AppGUID         = $_.PSChildName
            ComplianceState = Get-ItemPropertyValue -Path $_.PSPath -Name "ComplianceState"
            DownloadState   = Get-ItemPropertyValue -Path $_.PSPath -Name "DownloadState" -ErrorAction SilentlyContinue
            ErrorCode       = Get-ItemPropertyValue -Path $_.PSPath -Name "ErrorCode" -ErrorAction SilentlyContinue
        }
    } | Format-Table -AutoSize
}

# 2. GPO-List (GPR State: Applied Active Directory GPOs)
$GpoListPath = "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Group Policy\State\Machine\GPO-List"
if (Test-Path $GpoListPath) {
    Write-Host "`n[+] Active Directory GPO-List (GPR Applied GPOs State):" -ForegroundColor Green
    Get-ChildItem -Path $GpoListPath | ForEach-Object {
        $GpoId = Get-ItemPropertyValue -Path $_.PSPath -Name "GPO-ID" -ErrorAction SilentlyContinue
        $Name  = Get-ItemPropertyValue -Path $_.PSPath -Name "DisplayName" -ErrorAction SilentlyContinue
        Write-Host "  - GPO Name: $Name" -ForegroundColor White
        Write-Host "    GPO ID:   $GpoId" -ForegroundColor Gray
    }
}

# 3. Location 2: Intune MDM Enterprise Desktop App Management Inventory
$IntuneMdmPath = "HKLM:\SOFTWARE\Microsoft\EnterpriseDesktopAppManagement"
if (Test-Path $IntuneMdmPath) {
    Write-Host "`n[+] Intune Location 2 (MDM Desktop App Management Inventory):" -ForegroundColor Green
    Get-ChildItem -Path $IntuneMdmPath -Recurse | Where-Object { $_.PSChildName -like "{*" } | ForEach-Object {
        Write-Host "  - MDM Tracked App GUID: $($_.PSChildName)" -ForegroundColor White
    }
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

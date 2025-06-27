# Overview

## High-Level Windows 11 Upgrade Plan

### 1. Data Review & Device Readiness

- All device inventory and readiness data has been reviewed from the following files, located in '\GIT IT Support - Documents\General\Projects\2025 Project Documents\2025-10 Windows10 EOL'
  - 2025-05-20-allManagedWindowsDevices.csv
  - 2025-05-20-win10Devices.csv
  - 2025-05-21-allD42InTuneDevices.csv
  - 2025-05-21-allD42InTuneWin10Audit.csv
  - 2025-05-28-win10Sorted.csv
  - 2025-06-09-allwinDevices.csv
  - 2025-06-09-EE-EA-deviceReadiness.csv
- Devices are categorized as **Compatible** or **Not Compatible** for Windows 11 based on CPU, RAM, and other hardware requirements.

### 2. Upgrade Process

- **Compatible Devices:**
  - Use `Set-MgDeviceExtensionAttribute` to set a custom attribute (e.g., `patch level = 'upgrade'`).
  - This attribute will place devices into the required Azure AD/Intune groups for Windows 11 Feature Update deployment.
  - Devices in these groups will receive the Windows 11 Feature Update via Intune/Windows Update for Business.
- **Incompatible Devices:**
  - Devices flagged as "Not Compatible" (due to insufficient CPU, RAM, etc.) will be scheduled for replacement.
  - Replacement planning should include user communication, backup, and device lifecycle management.
- **Alerting**
  - We will reach out to the users 2 weeks prior 
  - We will reach out to the user 1 day prior and day of.

### 3. Microsoft Documentation Links

- [Windows 11 Feature Update Process](https://learn.microsoft.com/en-us/windows/whats-new/whats-new-windows-11-version-23h2)
- [Windows 11 Release Health](https://learn.microsoft.com/en-us/windows/release-health/)
- [Windows 11 Servicing Timeline](https://learn.microsoft.com/en-us/lifecycle/faq/windows#windows-11)
- [Manage Windows Updates for Business](https://learn.microsoft.com/en-us/windows/deployment/update/waas-manage-updates-wufb)
- [Set device extension attributes using Microsoft Graph](https://learn.microsoft.com/en-us/powershell/module/microsoft.graph.devices.cloudpc/set-mgdeviceextensionattribute)

### 4. Upgrade Steps

1. **Inventory & Readiness Validation**
  - Confirm device compatibility using the latest audit/exported data.
2. **Group Assignment**
  - For compatible devices, on a GIT Management Endpoint run a script to set the extension attribute after installing the EvapcoModule
  - *You will need to be PIM'd,* ***AND*** *Connect-MgGraph needs to have been run.*
     ```pwsh
     Set-MgDeviceExtensionAttribute -DeviceId <DeviceId> -ExtensionAttribute1 'upgrade'
     ```
  - [Ensure dynamic groups in Azure AD/Intune are configured to include devices](https://intune.microsoft.com/#view/Microsoft_AAD_IAM/GroupDetailsMenuBlade/~/DynamicGroupMembershipRule/groupId/4ead5497-a492-4812-b90b-634abb5013ee/menuId/)
  
   - The group is: [Global: CC - Windows 10 to Windows 11 Upgrade Devices](https://intune.microsoft.com/#view/Microsoft_AAD_IAM/GroupDetailsMenuBlade/~/Overview/groupId/4ead5497-a492-4812-b90b-634abb5013ee/menuId/)
3. **Feature Update Deployment**
  - Monitor deployment status and address any failures.
4. **Device Replacement**
  - For incompatible devices, initiate procurement and replacement process.
  - Communicate with users and schedule device swaps.

### 5. Notes

- Ensure all device data is up to date before running upgrade scripts.
- Communicate upgrade timelines and expectations to end users.
- Track progress and report on upgrade and replacement status.
- We are solely concerned with Non-OT Devices
  - OT Devices:
    - Air Gapped Systems
    - Systems that drive manufacturing equipment
    - Etc.
- **All Commands are to be run using the EvapcoModule**
- The File Path is as follows: ***Evapco, Inc\GIT IT Support - Documents\General\Powershell Scripts\EvapcoRepo\Modules\EvapcoModule***
  - The *required*    version is **1.0.2.4**
  - The *recommended* version is **1.0.3.7** 

## 6. Upgrade Experience

### Install the following module(s) if you do not have them already by running these commands:
```
 If (!((Get-PSRepository -Name PSGAllery | Select-Object -Property InstallationPolicy) -eq "Trusted")){Set-PSResourceRepository -Name PSGallery -Trusted:$true}
    if(!(Get-AppXPackage -name Microsoft.DesktopAppInstaller)){Add-AppxPackage -RegisterByFamilyName -MainPackage Microsoft.DesktopAppInstaller_8wekyb3d8bbwe}
    if (!(Get-PackageProvider -Name PowerShellGet)){Install-PackageProvider WinGet -Force}
    If (!(Get-PSResource -Name PSWinGet -Scope AllUsers -erroraction silentlyContinue)){Install-PSResource -Name PSWinGet -Scope AllUsers}
    $modules = Invoke-RestMethod -Method Get -URI "https://raw.githubusercontent.com/DirtyDabe23/DDrosdick_Public_Repo/refs/heads/main/PSModules.JSON"
    $allInstalledModules = Get-PSResource -Scope Allusers
    $missingModules = $modules | Where-Object {($_.Name -notin $allInstalledModules.Name)}
    $moduleErrorLog = @()
    $moduleErrorCount = 0
    ForEach ($module in $missingModules){
        try{
            Install-PSREsource -Name $module.name -Version ($module.version.Major, $module.version.Minor -join ".") -Scope AllUsers -ErrorAction SilentlyContinue
        }
        catch{
            $moduleErrorCount++
            $moduleErrorLog+= [PSCustomObject]@{
                moduleName = $Module.name
                error       = $error[0]
            }
        }
    }
    if ($moduleErrorCount -gt 0){
        Write-Output "Please review the `$moduleErrorLog after this completes"
    }
```


### The following Links are for Overviews and Visbility
- [Dynamic Group for the Update - 'Global: CC - Windows 10 to Windows 11 Upgrade Devices'](https://intune.microsoft.com/#view/Microsoft_AAD_IAM/GroupDetailsMenuBlade/~/Overview/groupId/4ead5497-a492-4812-b90b-634abb5013ee/menuId/)
- [Feature Update Configuration Profile](https://intune.microsoft.com/#view/Microsoft_Intune_DeviceSettings/WindowsFeatureUpdateProfileMenu/~/2/profileId/016465b5-f7f0-4d03-bc17-a3955e53d2cd/profileName/6%20GLOBAL%3A%20CC%20-%20Win10%20to%20Win11/hasWufbEnabledLicense/true)
- [Feature Update Failure Overview](https://intune.microsoft.com/#view/Microsoft_Intune_DeviceSettings/WindowsUpdateAlertSummaryReport.ReactView)
- [EvapcoModule](https://intune.microsoft.com/#view/Microsoft_Intune_Apps/SettingsMenu/~/0/appId/1f81f11d-8349-4cfb-8e53-a41b24da27cb)


#### 1. Using InTune |  **Requires PIM to 'InTune Device Administrator' or role with Device Management Permissions.**
- Open Terminal / 'PWSH' 
```
Connect-MgGraph
```
<br>

- Get the device name that you will be using 
```
$deviceName = 'Evapco-TechBenchPC'
```
<br>

- Enter the following command
```
Set-MGDeviceExtensionAttribute -DeviceName $deviceName -PatchLevel Upgrade
```
<br>

- **To ensure the device communicates with InTune and updates its Update Configuration to reflect in InTune, it is best to reboot the endpoint 15 minutes after making this change.**
- [Review the Group in InTune]((https://intune.microsoft.com/#view/Microsoft_AAD_IAM/GroupDetailsMenuBlade/~/Overview/groupId/4ead5497-a492-4812-b90b-634abb5013ee/menuId/))
- [Quick Link for Feature Update Failures Report in InTune](https://intune.microsoft.com/#view/Microsoft_Intune_DeviceSettings/WindowsUpdateAlertSummaryReport.ReactView)
- Review the endpoint between 1800-2300 the evening of the change
- Review the endpoint after   0700-0900 the following day
- Run the following command to get the status of the endpoint
``` 
Get-EndpointStatus -Recommendations
```
  - That will print out easy readable instructions and issues with the endpoint to let you know what the issues may be.
<br><br>

#### 2. Using Start-Windows11Upgrade | **Requires Local Admin**
- Run the following command and determine if the endpoint is eligible:
```
Get-Windows11HardwareReadiness
```
<br>

- Run the following command for a more readable list of requirements:
``` 
Get-EndpointStatus -Recommendations
```
<br>

- Run the following command to run the Windows 11 Upgrade Tool with all notifications to the end user:
```
Start-Windows11Upgrade
```
<br>

- Run the following command to run the Windows 11 Upgrade Tool with only the 'reboot' notification to the end user:
```
Start-Windows11Upgrade -Quiet
```
  - **The above will not notify in the event of incompatibility or failures. Nothing will occur.**
  - Review the Endpoint the following day.



# **If the Device fails both of the above methods, it will require replacement.**
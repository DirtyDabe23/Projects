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
  - For compatible devices, run a script to set the extension attribute:
     ```powershell
     Set-MgDeviceExtensionAttribute -DeviceId <DeviceId> -ExtensionAttribute1 'upgrade'
     ```
  - Ensure dynamic groups in Azure AD/Intune are configured to include devices with this attribute.
  
   - The group is: [Global: CC - Windows 10 to Windows 11 Upgrade Devices](https://intune.microsoft.com/#view/Microsoft_AAD_IAM/GroupDetailsMenuBlade/~/Overview/groupId/4ead5497-a492-4812-b90b-634abb5013ee/menuId/)
3. **Feature Update Deployment**
  -
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
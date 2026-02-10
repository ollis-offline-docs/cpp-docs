---
title: "Troubleshoot Visual C++ Redistributable installation issues"
description: "Provide steps to diagnose and resolve issues with installing the Visual C++ Redistributable as a standalone installation and as part of the Visual Studio Installer."
author: vicroms
ms.author: viromer
ms.date: 02/10/2025
ms.topic: troubleshooting-general
helpviewer_keywords: [ "redist", "vcredist", "Visual [C++] redistributable", "VC Redist" ]
---
# Troubleshoot Visual C++ Redistributable installation issues

This guide is for users experiencing issues while installing the Visual C++ Runtime components using the Visual C++ Redistributable (vcredist) installer or the Visual Studio (VS) Installer.

If you're experiencing such issues, we recommend that you first attempt installing the [latest version of the Visual C++ Redistributable](latest-supported-vc-redist.md).

## Collect failure logs

The first step to diagnose an issue with the Visual C++ Redistributable installer is to collect its failure logs.

1. Download the [Microsoft Visual Studio and .NET Log Collection Tool](<https://aka.ms/vscollect>).
2. Run `Collect.exe`.
3. Extract the contents of `%TEMP%/vscollect.zip`.

Once you extract `vscollect.zip`, the vcredist logs are located inside the `Temp` folder. The relevant log files are prefixed with the pattern `dd_vcredist_<arch>_yyyyMMddHHmmss`.

The vcredist logs are useful to diagnose common installation issues.

### Other log locations

The vcredist installer is often executed as a prerequisite of other products, in such cases the installation log might be found in a different path.

For example, [Configuration Manager](/intune/configmgr/core/understand/introduction) upgrades vcredist as part of its own upgrade process by running `vcredist_x64.exe` with the `/l` option that overrides the default log location.

In such cases, the path to the logs can be found by reading that product's own logs:

**Example: Configuration Manager logs**

`<ConfigMgr_Installation_Directory>\Logs\cmupdate.log`

```log
10-31-2025 17:40:06.421    CONFIGURATION_MANAGER_UPDATE    67368 (0x10728)    [Visual C++ 2015-2022 Redistributable (x64)] with older version 14.28.29914 is installed. it needs to upgraded.
10-31-2025 17:40:06.421    CONFIGURATION_MANAGER_UPDATE    67368 (0x10728)    INFO: Start install Visual C redistributable ("C:\Program Files\Microsoft Configuration Manager\CMUStaging\AA928926-5C76-4DE0-B51F-0FE4D365DFE2\SMSSETUP\BIN\X64\vcredist_x64.exe"  /q /norestart /l "C:\Program Files\Microsoft Configuration Manager\Logs\VCRedist64Install.log").
10-31-2025 17:40:14.553    CONFIGURATION_MANAGER_UPDATE    67368 (0x10728)    ERROR: Failed to install Visual C redistributable. Return code: 1603.
10-31-2025 17:40:14.553    CONFIGURATION_MANAGER_UPDATE    67368 (0x10728)    ERROR: 64-bit VC Redist installation ("C:\Program Files\Microsoft Configuration Manager\CMUStaging\AA928926-5C76-4DE0-B51F-0FE4D365DFE2\SMSSETUP\BIN\X64\vcredist_x64.exe") failed. Please check log file [C:\Program Files\Microsoft Configuration Manager\Logs\VCRedist64Install.log].
10-31-2025 17:40:14.554    CONFIGURATION_MANAGER_UPDATE    67368 (0x10728)    Failed to install vc redist. Please manually install it from C:\Program Files\Microsoft Configuration Manager\CMUStaging\AA928926-5C76-4DE0-B51F-0FE4D365DFE2\SMSSETUP\BIN\X64
```

## General troubleshooting steps

This section describes general troubleshooting methods you can try to resolve issues with the vcredist installer.

### <a name="method-1"></a> Method 1: Disable antivirus software temporarily

Security products often block installation of vcredist components.

1. Turn off Windows Defender real-time protection.
2. Disable any corporate endpoint protection temporarily (Symantec, McAfee, etc.).
3. Retry the installation.
4. Reenable any disabled protection software.

### <a name="method-2"></a> Method 2: Run the Visual C++ Redistributable installer as Administrator

File-access failures are heavily correlated with insufficient permissions.

1. Right-click the vcredist installer and select **Run as administrator**.
2. Retry the installation.

### <a name="method-3"></a> Method 3: Check for Windows System Updates

On rare occasions, outdated system components can cause installation issues.

1. Go to Windows Update and install all pending updates.
2. Reboot your PC.
3. Retry the installation.

> [!IMPORTANT]
> The following troubleshooting methods apply when vcredist is installed as a component of the Visual Studio Installer.

### <a name="method-4"></a> Method 4: Manually Run the Visual C++ Redistributable installer

1. Go to `%ProgramData%\Microsoft\VisualStudio\Packages`
2. Open the folders:
    - `Microsoft.VisualCpp.Redist.14,version=<version>,chip=x64`
    - `Microsoft.VisualCpp.Redist.14,version=<version>,chip=x86`
3. Run:
    - `vc_redist.x64.exe`
    - `vc_redist.x86.exe`
4. Retry the Visual Studio Installer.

If the manual installation fails, follow [methods 1 and 2](#method-1).

### <a name="method-5"></a> Method 5: Clear the Visual Studio Installer cache

1. Open `%ProgramData%\Microsoft\VisualStudio\Packages`
2. Delete all files inside the folder to force the Visual Studio Installer to regenerate them.
3. Retry the Visual Studio Installer.

### <a name="method-6"></a> Method 6: Repair the Visual Studio Installer

1. Go to **Settings** > **Apps** > **Installed Apps**
2. Select **Visual Studio Installer** > **Modify** > **Repair**
3. Retry the Visual Studio Installer.

### <a name="method-7"></a> Method 7: Delete the Visual Studio Installer folder

> [!WARNING]
> This method requires that you have the Visual Studio Installer Setup downloaded (VisualStudioSetup.exe).
> You can download the Visual Studio Installer Setup from <https://my.visualstudio.com>.

This method helps in case the installer metadata is corrupted, deleting the installer folder forces the Visual Studio Installer to regenerate it.

1. Download `VisualStudioSetup.exe` from <https://my.visualstudio.com>.
2. Delete the folder `C:\Program Files (x86)\Microsoft Visual Studio\Installer`, you might be prompted to run this operation as Administrator.
3. Run `VisualStudioSetup.exe`.

## Common issues

### Return code 1603

Return code 1603 indicates a generic installation failure produced by the Windows Installer during the installation of the Visual C++ Runtime components.

Because many factors can produce a 1603 code, the code by itself doesn't provide enough information to diagnose the cause of the issue.
Often, the log files produced by the vcredistt installer contain relevant information that might lead to a solution or workaround.

The [Common issues](#common-issues) section describes examples of how to diagnose common installation errors and steps that might resolve them.
If your issue isn't found here, then follow the instructions to [report an issue in the Visual C++ Redistributable installer](#report-a-visual-c-redistributable-installation-problem).

### Access denied

An issue occurs during installation of vcredist failing with return code 5.
The error code typically indicates a permissions issue; specifically, an access denied issue.

**Steps to resolve**

1. Disable antivirus, group policies, and firewalls temporarily.
2. Run the vcredist installer.
3. Reenable any disabled protection software.

If the installation fails, try to run the vcredist installer as Administrator.

### File is locked

An issue occurs during the installation of vcredist failing with return code 32.
Likely causes include blocked permissions, interference from antivirus, group policies, or corrupted files.

**Steps to resolve**

1. Close any running software in your PC.
2. Try the methods in [General troubleshooting steps](#general-troubleshooting-steps).

If the installation fails, try to restart your PC to release any locked files.

### Corrupt or invalid installer package

The issue involves an error during installation of vcredist using the Visual Studio Installer with error code 1620.
Error code 1620 indicates that a Windows Installer package couldn't be opened, likely due to corruption or invalid files.

One or more of these error codes are present in the logs:

- Error code 1620: indicates that the Windows Installer package couldn't be opened.
- Error code 0x80091007: several payloads failed verification due to hash mismatches.
- Error code 0x80070654: occurs when an MSI package fails to execute.

**Steps to resolve**

Methods 5, 6, and 7 in the [General troubleshoot steps](#file-is-locked) might help solve this issue by cleaning the package cache.

### Older version can't be removed

A corrupted Windows Installer cache produces error code 1714, indicating a failure to remove a previous version of vcredist.

The presence of these error messages indicates a corrupted cache.

> [!NOTE]
> Timestamps are removed from these examples to condense the output.

In `dd_vcredist_<arch>_<timestamp>.log`

```log
Error 0x80070003: Failed to get size of pseudo bundle: C:\ProgramData\Package Cache\{43d1ce82-6f55-4860-a938-20e5deb28b98}\VC_redist.x64.exe
Error 0x80070003: Failed to initialize package from related bundle id: {43d1ce82-6f55-4860-a938-20e5deb28b98}
```

In  `dd_vcredist_<arch>_<timestamp>_vcRuntimeMinimum_<arch>.log`:

```log
SOURCEMGMT: Trying source C:\ProgramData\Package Cache\{455DF12C-7D43-4EFF-AE2F-43C8AF2817A3}v14.28.29914\packages\vcRuntimeMinimum_amd64\.
Note: 1: 2203 2: C:\ProgramData\Package Cache\{455DF12C-7D43-4EFF-AE2F-43C8AF2817A3}v14.28.29914\packages\vcRuntimeMinimum_amd64\vc_runtimeMinimum_x64.msi 3: -2147287037 
SOURCEMGMT: Source is invalid due to missing/inaccessible package.
```

```log
Note: 1: 1714 2: Microsoft Visual C++ 2022 X64 Minimum Runtime - 14.40.33816 3: 1612 
CustomAction  returned actual error code 1612 (note this may not be 100% accurate if translation happened inside sandbox)
Product: Microsoft Visual C++ 2022 X64 Minimum Runtime - 14.40.33816 -- Error 1714. The older version of Microsoft Visual C++ 2022 X64 Minimum Runtime - 14.40.33816 cannot be removed.  Contact your technical support group.  System Error 1612.

Error 1714. The older version of Microsoft Visual C++ 2022 X64 Minimum Runtime - 14.40.33816 cannot be removed.  Contact your technical support group.  System Error 1612.
```

In `VSSetupEvents.txt`

```log
Error 1714. The older version of Microsoft Visual C++ 2022 X64 Minimum Runtime - 14.40.33816 cannot be removed.  Contact your technical support group.  System Error 1612.] [(NULL)] [(NULL)] [(NULL)] [(NULL)] [(NULL)] []
```

**Steps to resolve**

From the log files, take note of the vcredist version causing the issue.

Method 1: Use the Windows Installer.

1. Try using the Windows Installer to manually remove the old vcredist version.
   If prompted, let the Windows Installer Troubleshooter attempt to fix the issue.
2. Retry the installation.

Method 2: Manually remove the old version.

1. Download the vcredist installer for the old version by following the steps in
   [Download old versions of the Visual C++ Redistributable installer](#old-vcredist-versions)
2. Run the installer to uninstall the old vcredist.
3. Retry the installation.

## <a name="old-vcredist-versions"></a> Download old versions of the Visual C++ Redistributable installer

> [!WARNING]
> Never install a Visual C++ Redistributable installer that wasn't downloaded from a Microsoft site.

The vcredist installer can be downloaded from [my.visualstudio.com](<https://my.visualstudio.com/Downloads>), search for Visual C++ Redistributable

The latest supported vcredist version for each version of Visual Studio can be found in [this article](/cpp/windows/latest-supported-vc-redist).

To access older or legacy versions follow these steps:

**Version 14.50 or later**

The download links use the `https://aka.ms/vs/18/release/<version>/VC_redist.<arch>.exe` pattern.
For example: <https://aka.ms/vs/18/release/14.50.35719/VC_redist.x64.exe>.

**Version 14.30 to 14.44**

The download links use the `https://aka.ms/vs/17/release/<version>/VC_redist<arch>.exe` pattern.
For example: <https://aka.ms/vs/17/release/14.32.31332/VC_redist.arm64.exe>.

**Version 14.20 to 14.29**

The download links use the `https://aka.ms/vs/16/release/<version>/VC_redist<arch>.exe` pattern.
For example: <https://aka.ms/vs/16/release/14.28.29914/VC_Redist.x86.exe>

**Version 14.10 or later**

The download links use the `https://aka.ms/vs/15/release/<version>/VC_redist<arch>.exe` pattern.
For example: <https://aka.ms/vs/15/release/14.12.25810/VC_redist.x64.exe>

If the aka.ms links don't work, you might be able to find the version you're looking for through a Bing search.
However, make sure that the download comes from a Microsoft site and that the installer is signed by Microsoft.

## Report a Visual C++ Redistributable installation problem

The list of [common issues](#common-issues) was collected from feedback reported to Microsoft through [Developer Community](<https://developercommunity.visualstudio.com/>).

If you encounter an issue not found in that section, or if the steps in this troubleshooting guide don't resolve your issue. Use the [Report a Problem](<https://developercommunity.visualstudio.com/cpp/report>) form to create a new feedback item.

Your report must include the following information about your environment:

- Version of vcredist you're trying to install.
- If upgrading, version of previously installed vcredist installations.
- If installing through the Visual Studio Installer, version of the VS Installer.
- Logs collected by following the steps in the [Collect failure logs](#collect-failure-logs) section.

Feedback without this information, especially without logs, is nonactionable and might be closed if additional information is not submitted promptly.

---
title: "Automated Autopilot Tenant Move Part 2: Using PowerShell and Graph"
date: 2021-10-14T11:19:49+01:00
draft: true
tags: ["PowerShell", "Autopilot", "Microsoft Graph"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "A simpler, automated process to wipe, then de-register an Autopilot device from one tenant and register to another"
    relative: false
---

Following on from my previous post, this one provides a far simpler process to automatically de-register your Windows Autopilot devices from one tenant, and provision them in another tenant.

I came across a great post on [MsEndpointMgr](https://msendpointmgr.com/2019/06/01/intune-tenant-to-tenant-migration-with-autopilot/) which details the steps to extract your Autopilot profile in the new tenant, and copy to the machines in the old tenant ready for them to be wiped. But the process was missing an automated way to delete the Intune Managed Device, delete the current Autopilot registration and then wipe the device. This post will cover those gaps so the end-to-end process is fully automated.

Throughout the post the old tenant will be referred to as "Tenant A" and the new tenant "Tenant B".

## Overview

Here is a high-level overview of the process:

1. From the Company Portal, the process begins when the PowerShell script (packages as a Win32 app) is invoked.
2. The script will copy Tenant B's Autopilot configuration file (AutopilotConfigurationFile.json) to `C:\Windows\Provisioning\Autopilot` on the local machine. The json file is in the same app package as the script.
3. Using MS Graph and an App Registration in Tenant A, the script will gather the local machines "Managed Device" and delete it from Intune, then delete the Autopilot Registered Device, and finally sync the Autopilot Registration Service in Tenant A.
4. A function within the PowerShell script will wipe the device via WMI/CIM. Note: The `C:\Windows\Provisioning\Autopilot` and it's content remains intact during a device reset.
5. After the device completes it's reset the user will be presented with the Autopilot registration for Tenant B.

Assuming Tenant B is fully provisioned and ready to go, the following is required:

## App Registration in Tenant A

First we need to create the App registration in Tenant A, which has all the relevant permissions to delete an Intune Manage Device and Autopilot device registration.

* Open Azure Active Directory
* Click App Registration
* Click New registration
* Give it a name of __AutopilotTenantMove__
* Under __Support account types__ select __Accounts in this organizational directory only__

![AppReg1](images/AppReg1.png)

* Click __Register__
* In the newly created App Registration, select __API permissions__
* Click Add a permission

![AppReg2](images/AppReg2.png)

* Under Microsoft APIs, select Microsoft Graph

![AppReg3](images/AppReg3.png)

* Click Application permissions

![AppReg4](images/AppReg4.png)

* Scroll down to __DeviceManagementManagedDevices__, expand it and tick the box for __DeviceManagementManagedDevices.ReadWrite.All__.

> This is required to delete Intune Managed device

* Scroll down to __DeviceManagementServiceConfig__, expand it and tick the box for __DeviceManagementServiceConfig.ReadWrite.All__.

> This is required to delete the Autopilot device registration

* Then select __Add permissions__

![AppReg5](images/AppReg5.png)

* Back on the API permissions page, select __Grant consent__ for the tenant.

![AppReg6](images/AppReg6.png)

* Under __Manage__ of the App Registration, click __Certificates & secrets__.
* Click __New client secret__

![ClientSecret1](images/ClientSecret1.png)

* Give it a description of __Autopilot Tenant Move__ and Expires in __6 months__
* Click __Add__

![ClientSecret2](images/ClientSecret2.png)

* IMPORTANT: He you need to save the __Value__ of the Client secret somewhere save to be used in the PowerShell script later.

![ClientSecret3](images/ClientSecret3.png)

* Finally, now may also be a good time to click the __Overview__ page of the App registration and copy the __Application (client) ID__ which will be needed along with the client secret in the PowerShell script.

## Autopilot Profile from Tenant B

In this part we will extract the Autopilot profile from Tenant B in the form of a json file, which will be used later to add to the `C:\Windows\Provisioning\Autopilot` directory of the devices before they are deleted and reset. Perform the following.

* Run PowerShell as admin
* Type `Install-Module WindowsAutopilotIntune -Force`
* Authenticate to MS Graph by typing `Connect-MSGraph`. Enter your credentials for Tenant B
* If you have multiple Autopilot profiles, type `Get-AutopilotProfile` and gather the `Id` of the one you want. You will need to type `Get-AutopilotProfile -Id <AutopilotID>` in the next part
* Then extract and convert the Autopilot profile to json using the following command:

```PowerShell
# You will need to specify the path to extract the file to. I selected C:\Temp
Get-AutopilotProfile | ConvertTo-AutopilotconfigurationJSON | Out-File -FilePath C:\Temp\AutopilotConfigurationFile.json -Encoding ASCII
```

You will need the AutopilotConfigurationFile for the next part when you create the Intune Win32 App.

## PowerShell Script and Win32 App in Tenant A

In this section we will modify the PowerShell script with the Application (client) ID and secret of the app registration, and package the script and AutopilotConfigurationFile.json as an Intune Win32 App.

* In the following [GitHub Repo](https://github.com/markkerry/automated-autopilot-tenant-move-simplified), download `Install.cmd`, `Invoke-AutopilotTenantMove.ps1`, and `Uninstall.cmd` into a directory called `C:\Win32Apps\AutopilotTenantMove`.

* Open `Invoke-AutopilotTenantMove.ps1` and edit lines 171, 172 and 173 with your app registration's Client ID, Secret and your tenant name (e.g. contoso.com). Then save the file. Note: these are variables below:

```powershell
$clientID = ''
$clientSecret = ''
$tenantID = ''
```

* Copy the `AutopilotConfigurationFile.json` you created earlier to the same directory. The contents of the directory should look as follows:

```cmd
C:\Win32Apps\AutopilotTenantMove
        AutopilotConfigurationFile.json
        Install.cmd
        Invoke-AutopilotTenantMove.ps1
        Uninstall.cmd
```

* Now create a new directory called `C:\Win32Apps\AutopilotTenantMoveOutput`

* Open the [Microsoft Win32 Content Prep Tool](https://github.com/Microsoft/Microsoft-Win32-Content-Prep-Tool) repo and download the `IntuneWinAppUtil.exe` binary to `C:\Win32Apps`.

* Now we can package the scripts into an `.intune.wim` file. Open the Command Prompt and run the following:

```cmd
cd C:\Win32Apps

IntuneWinAppUtil.exe -c C:\Win32Apps\AutopilotTenantMove -s Invoke-AutopilotTenantMove.ps1 -o C:\Win32Apps\AutopilotTenantMoveOutput
```

* If you browse to `C:\Win32Apps\AutopilotTenantMoveOutput` you'll see the new `Invoke-AutopilotTenantMove.intunewin` package which needs uploading to Intune.

![IntuneWinAppUtil](images/IntuneWinAppUtil.gif)

## Upload the Win32 App to Intune in Tenant A


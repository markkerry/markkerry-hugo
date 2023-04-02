---
title: "Deploy an Azure Automation Runbook With PowerShell"
date: 2023-04-02T12:39:38Z
draft: false
tags: ["Azure", "Automation", "PowerShell"]
cover:
    image: "media/cover.png"
    alt: "<alt text>"
    caption: "Deploy a PowerShell 5.1 & 7.1 runtime, Azure Automation Runbook, using PowerShell"
    relative: false
---

The following will walk through the steps to deploy a PowerShell 5.1 and 7.1 runtime, Azure Automation Runbook, using PowerShell. This may be necessary if you are using a deployment script in a pipeline. There are two different methods to this depending on if you are deploying a runtime type of PowerShell 5.1 or PowerShell 7.1. If you want to use the `ForEach-Object -Parrallel` feature for example, then you will have to use PowerShell 7.1 runtime type.

> _NOTE: At the time of writing, Microsoft does not support deploying a runbook with PowerShell 7.1 as the runtime type using PowerShell, AZ CLI, ARM or Bicep. To get around this the content of the runbook has to be posted to the automation account using the `Invoke-AzRestMethod` cmdlet._

I won't go over creating the automation account and will assume it is already created. To create one using PowerShell see [Microsoft Docs](https://learn.microsoft.com/en-us/powershell/module/az.automation/new-azautomationaccount).

## PowerShell 5.1 Runtime

The PowerShell 5.1 runbook can easily be created and imported using a single command. The following assumes the runbook script is in the same directory as the deployment script and the name of the file is assigned to the `$scriptName` variable. Assign the `$runbookName` variable the name of the runbook you want to create or deploy to.

```powershell
$automationAccount = ""
$resourceGroup = ""
$runbookName = ""
$scriptName = ""

Import-AzAutomationRunbook `
    -AutomationAccountName $automationAccount `
    -Name $runbookName `
    -Path $PSScriptRoot\$scriptName `
    -Published `
    -ResourceGroupName $resourceGroup `
    -Type PowerShell
```

Although the above is well documented already, I wanted to keep it in this post as a comparison to doing for PowerShell 7.1 runtime which is not documented by Microsoft.

## PowerShell 7.1 Runtime

As mentioned above the process is different and the `Invoke-AzRestMethod` cmdlet has to be used to publish a PowerShell 7.1 runtime runbook. As the script has to be posted, start by assigning the script content to a variable, create the runbook, then finally importing it. The below gives an example script assigned to the `$scriptContent` variable.

```powershell
$scriptContent = @'
# Ensure you do not inherit an AzContext in your runbook

try {
    Disable-AzContextAutosave -Scope Process | Out-Null
    Write-Output "Logging in to Azure..."
    Connect-AzAccount -Identity | Out-Null
}
catch {
    Write-Error -Message $_.Exception
    throw $_.Exception
}

# Rest of script goes here...
'@

$automationAccount = ""
$resourceGroup = ""
$runbookName = ""
$location = ""

Invoke-AzRestMethod -Method "PUT" `
    -ResourceGroupName $resourceGroup `
    -ResourceProviderName "Microsoft.Automation" `
    -ResourceType "automationAccounts" `
    -Name "${$automationAccount}/runbooks/${$runbookName}" `
    -ApiVersion "2017-05-15-preview" `
    -Payload "{`"properties`":{`"runbookType`":`"PowerShell7`", `"logProgress`":false, `"logVerbose`":false, `"draft`":{}}, `"location`":`"${location}`"}"

Invoke-AzRestMethod -Method "PUT" `
    -ResourceGroupName $resourceGroup `
    -ResourceProviderName "Microsoft.Automation" `
    -ResourceType automationAccounts `
    -Name "${$automationAccount}/runbooks/${$runbookName}/draft/content" `
    -ApiVersion 2015-10-31 `
    -Payload "$scriptContent"
```

Once the PowerShell 7 Runbook has been deployed to the automation account, the standard PowerShell cmdlets can be used again, such as [creating schedules](https://learn.microsoft.com/en-us/powershell/module/az.automation/new-azautomationschedule).

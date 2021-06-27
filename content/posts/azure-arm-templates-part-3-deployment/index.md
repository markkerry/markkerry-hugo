---
title: "Azure ARM Templates - Part 3: Deployment"
date: 2021-06-16T15:55:22+01:00
draft: true
tags: ["Azure", "ARM Templates", "IaC"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "Part 3 in developing, testing and deploying Azure ARM templates"
    relative: false
---

Test

## Deploy

Deploy

```powershell
# Connect to Azure
Connect-AzAccount

# Create the Resource Group
New-AzResourceGroup -Name "rg-eu-vm" -Location "westeurope"
```

![newRG](images/newRG.png)

``` powershell
# Deploy the resources using the template and parameters file
New-AzResourceGroupDeployment -Name "NewVM" -ResourceGroupName "rg-mk-bicep" -TemplateParameterFile .\NewVM.parameters.json -TemplateFile .\NewVM.json
```

![output](images/output.png)

You can list the resource using the command

```powershell
Get-AzResource -ResourceGroupName "rg-eu-vm" | ft
```

## Clean-up Resources

```powershell
Remove-AzResourceGroup -Name "rg-eu-vm"
```

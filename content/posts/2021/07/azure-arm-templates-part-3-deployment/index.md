---
title: "Azure ARM Templates - Part 3: Deployment"
date: 2021-07-04T12:55:22+01:00
draft: false
tags: ["Azure", "ARM Templates", "IaC", "PowerShell"]
cover:
    image: "images/cover.png"
    alt: "<alt text>"
    caption: "Part 3 in developing, testing and deploying Azure ARM templates"
    relative: false
---

In the third and final post of this series I'll quickly cover the process to deploy the ARM template with PowerShell

## Deploy

Open PowerShell and change directory to the location of your ARM template and parameter files. Then complete the following commands to connect to Azure and create a Resource Group to deploy the resources to:

```powershell
# Import the Azure Az PowerShell Module
Import-Module -Name Az

# Connect to Azure and authenticate
Connect-AzAccount

# Create the Resource Group
New-AzResourceGroup -Name "rg-eu-vm" -Location "westeurope"
```

![newRG](images/newRG.png)

Once the Resource Group has been created it's time to deploy the resources using the `New-AzResourceGroupDeployment` cmdlet. Here I have specified the name of the deployment as "NewVM", the newly created resource group, and the json files.

``` powershell
# Deploy the resources using the template and parameters file
New-AzResourceGroupDeployment -Name "NewVM" -ResourceGroupName "rg-eu-vm" -TemplateParameterFile .\NewVM.parameters.json -TemplateFile .\NewVM.json
```

You can see the successful deployment of the resources in the image below. Notice the Outputs which I specified in the ARM template.

![output](images/output.png)

The image at the top of this post is how the resources looked within the Resource Group after deployment. You can also list the resource using the following cmdlet:

```powershell
Get-AzResource -ResourceGroupName "rg-eu-vm" | ft
```

## Clean-up Resources

To clean-up the resources after you are done with them, and to avoid any unnecessary spend, delete the Resource Group (and it's contents) as follows:

```powershell
Remove-AzResourceGroup -Name "rg-eu-vm"
```

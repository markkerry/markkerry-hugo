---
title: "Deploy an AKS Cluster with Pulumi and C#"
date: 2022-12-01T16:16:14Z
draft: true
tags: ["Azure", "Kubernetes", "IaC", "Pulumi", "C#"]
cover:
    image: "media/cover.png"
    alt: "<alt text>"
    caption: "Deploy an Azure Kubernetes Service Cluster Using Pulumi"
    relative: false
---

Use Pulumi with a remote state to deploy resources into Azure.

## Install Pulumi, .Net SDK, and AZ CLI

Using PowerShell 7

## Create the Azure resources

```terminal
az login
```

Create the RG

```terminal
az group create --name PulumiState --location uksouth
```

Create the storage account for the state

```terminal
az storage account create --name stgmkpulumistate --resource-group PulumiState --location uksouth --sku Standard_LRS
```

Get the first access key

```terminal
$key = $(az storage account keys list --resource-group PulumiState --account-name stgmkpulumistate --query "[0].value" -o tsv)
```

> __NOTE__: _The Azure account must have the Storage Blob Data Contributor role or an equivalent role with permissions to read, write, and delete blobs_

Create the container

```terminal
az storage container create --name state --account-name stgmkpulumistate --account-key $key
```

Create the Key Vault

```terminal
az keyvault create --location uksouth --name kvmkpulumi --resource-group PulumiState
```

Create a Key

```terminal
az keyvault key create --name PulumiStateAKS --kty RSA --vault-name kvmkpulumi
```

In the Azure portal, gather the vault key ID

Set the variables

```powershell
$stgAccountName = "stgmkpulumistate"
$containerName = "state"
$keyVaultName = "kvmkpulumi"
$keyVaultKey = "PulumiStateAKS/<unique_id>"
```

Now configure some temporary environment variables

```powershell
$env:AZURE_STORAGE_ACCOUNT = $stgAccountName
$env:AZURE_STORAGE_KEY = $(az storage account keys list --account-name $stgAccountName --query [0].value --output=tsv)
$env:AZURE_KEYVAULT_AUT_CLI=$true
```

## Deploy a Storage Account Using Pulumi

```terminal
mkdir pulumi-aks && cd pulumi-aks
```

Log into Pulumi's new backend

```terminal
pulumi login --cloud-url azblob://$containerName?storage_account=$stgAccountName
```

```terminal
pulumi new kubernetes-azure-csharp
```

Give the project a name, description, name the stack.

Create a new stack using Key Vault Key

```terminal
pulumi stack init aks-secure --secrets-provider="azurekeyvault://$keyVaultName.vault.azure.net/keys/$keyVaultKey"
```

Set the location 

```terminal
pulumi config set azure-native:location uksouth
```

Preview the deployment

```terminal
pulumi preview
```

deploy the resources

```terminal
pulumi up
```

Check the state file

```terminal
az storage blob directory show \
  --container-name state \
  --directory-path ".pulumi/stacks/dev.json" \
  --account-name stgmkpulumistate
```


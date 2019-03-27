---
title: Distribuera en Azure Resource Manager-mall i en Azure Automation-runbook
description: Så här distribuerar du en Azure Resource Manager-mall som lagras i Azure Storage från en runbook
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
keywords: powershell,  runbook, json, azure automation
ms.openlocfilehash: 2008ba697665baa0e8cf73564ec31d6267425404
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58446974"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Distribuera en Azure Resource Manager-mall i en Azure Automation PowerShell- runbook

Du kan skriva en [Azure Automation PowerShell-runbook](automation-first-runbook-textual-powershell.md) som distribuerar en Azure-resurs med hjälp av en [Azure Resource Manager-mall](../azure-resource-manager/resource-manager-create-first-template.md).

På så sätt kan du automatisera distributionen av Azure-resurser. Du kan underhålla dina Resource Manager-mallar på en central och säker plats som Azure Storage.

I den här artikeln ska vi skapa en PowerShell-runbook som använder en Resource Manager-mall som lagras i [Azure Storage](../storage/common/storage-introduction.md) att distribuera ett nytt Azure Storage-konto.

## <a name="prerequisites"></a>Förutsättningar

Den här kursen behöver du följande objekt:

* En Azure-prenumeration. Om du inte har ett konto kan du [aktivera MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller [registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* [Azure Storage-konto](../storage/common/storage-create-storage-account.md) där du vill lagra Resource Manager-mall
* Azure Powershell installerad på en lokal dator. Se [installera och konfigurera Azure Powershell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) information om hur du hämtar Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

Det här exemplet använder vi en Resource Manager-mall som distribuerar ett nytt Azure Storage-konto.

Kopiera följande text i en textredigerare:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Spara filen lokalt som `TemplateTest.json`.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Spara Resource Manager-mall i Azure Storage

Vi använder PowerShell för att skapa en filresurs i Azure Storage och överföra den `TemplateTest.json` filen.
Instruktioner om hur du skapar en fil dela och ladda upp en fil i Azure-portalen finns i [Kom igång med Azure File storage i Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starta PowerShell på den lokala datorn och kör följande kommandon för att skapa en filresurs och ladda upp Resource Manager-mallen till filresursen.

```powershell
# Login to Azure
Connect-AzureRmAccount

# Get the access key for your storage account
$key = Get-AzureRmStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Skapa PowerShell-runbook-skript

Nu skapar vi ett PowerShell-skript som hämtar den `TemplateTest.json` från Azure Storage och distribuerar mallen för att skapa ett nytt Azure Storage-konto.

Klistra in följande text i en textredigerare:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)



# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Spara filen lokalt som `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importera och publicera en runbook i Azure Automation-konto

Nu vi använda PowerShell för att importera runbooken till ditt Azure Automation-konto och sedan publicera en runbook.
Information om hur du importerar och publicerar en runbook i Azure-portalen finns i [hantera runbooks i Azure Automation](manage-runbooks.md).

Importera `DeployTemplate.ps1` i ditt Automation-konto som en PowerShell-runbook, kör du följande PowerShell-kommandon:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzureRmAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Starta runbook

Nu börjar vi runbook genom att anropa den [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) cmdlet.

Information om hur du startar en runbook i Azure-portalen finns i [starta en runbook i Azure Automation](automation-starting-a-runbook.md).

Kör följande kommandon i PowerShell-konsolen:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzureRmAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzureRmAutomationRunbook @startParams
```

Runbook körs, och du kan kontrollera statusen genom att köra `$job.Status`.

Runbook hämtar Resource Manager-mallen och används för att distribuera ett nytt Azure Storage-konto.
Du kan se att det nya lagringskontot har skapats genom att köra följande kommando:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Sammanfattning

Klart! Nu kan du använda Azure Automation och Azure Storage och Resource Manager-mallar för att distribuera alla dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Resource Manager-mallar i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Kom igång med Azure Storage, se [introduktion till Azure Storage](../storage/common/storage-introduction.md).
* Du hittar andra användbara Azure Automation-runbooks [Runbook- och gallerier för Azure Automation](automation-runbook-gallery.md).
* Andra användbara Resource Manager-mallar finns i [Azure-Snabbstartsmallar](https://azure.microsoft.com/resources/templates/)



---
title: Distribuera en Azure Resource Manager-mall i en Azure Automation-Runbook
description: Så här distribuerar du en Azure Resource Manager mall som lagras i Azure Storage från en Runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell,  runbook, json, azure automation
ms.openlocfilehash: d4adbea42cda54380ad32dce40cfa0d8391ee490
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75366642"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Distribuera en Azure Resource Manager-mall i en Azure Automation PowerShell- runbook

Du kan skriva en [Azure Automation PowerShell-Runbook](automation-first-runbook-textual-powershell.md) som distribuerar en Azure-resurs med hjälp av en [Azure Resource Management-mall](../azure-resource-manager/resource-manager-create-first-template.md).

På så sätt kan du automatisera distributionen av Azure-resurser. Du kan underhålla Resource Manager-mallarna på en central, säker plats, till exempel Azure Storage.

I den här artikeln skapar vi en PowerShell-Runbook som använder en Resource Manager-mall som lagras i [Azure Storage](../storage/common/storage-introduction.md) för att distribuera ett nytt Azure Storage-konto.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande objekt:

* En Azure-prenumeration. Om du inte har något än kan du [aktivera din prenumeration på MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller [Registrera dig för ett kostnads fritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* [Azure Storage konto](../storage/common/storage-create-storage-account.md) där du vill lagra Resource Manager-mallen
* Azure PowerShell installerat på en lokal dator. Se [Installera och konfigurera Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) för information om hur du hämtar Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

I det här exemplet använder vi en Resource Manager-mall som distribuerar ett nytt Azure Storage-konto.

Kopiera följande text i en text redigerare:

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

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Spara Resource Manager-mallen i Azure Storage

Nu använder vi PowerShell för att skapa en Azure Storage fil resurs och ladda upp `TemplateTest.json`s filen.
Instruktioner för hur du skapar en fil resurs och laddar upp en fil i Azure Portal finns i [komma igång med Azure File Storage i Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starta PowerShell på den lokala datorn och kör följande kommandon för att skapa en fil resurs och ladda upp Resource Manager-mallen till fil resursen.

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

## <a name="create-the-powershell-runbook-script"></a>Skapa PowerShell-skriptet för Runbook

Nu skapar vi ett PowerShell-skript som hämtar `TemplateTest.json`-filen från Azure Storage och distribuerar mallen för att skapa ett nytt Azure Storage-konto.

I en text redigerare klistrar du in följande text:

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

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importera och publicera runbooken i ditt Azure Automation-konto

Nu använder vi PowerShell för att importera runbooken till ditt Azure Automation-konto och sedan publicera runbooken.
Information om hur du importerar och publicerar en Runbook i Azure Portal finns i [Hantera Runbooks i Azure Automation](manage-runbooks.md).

Kör följande PowerShell-kommandon om du vill importera `DeployTemplate.ps1` till ditt Automation-konto som en PowerShell-Runbook:

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

Nu startar du runbooken genom att anropa cmdleten [Start-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook) .

Information om hur du startar en Runbook i Azure Portal finns i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).

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

Runbooken körs och du kan kontrol lera dess status genom att köra `$job.Status`.

Runbook hämtar Resource Manager-mallen och använder den för att distribuera ett nytt Azure Storage-konto.
Du kan se att det nya lagrings kontot har skapats genom att köra följande kommando:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Sammanfattning

Klart! Nu kan du använda Azure Automation och Azure Storage och Resource Manager-mallar för att distribuera alla dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

* Mer information om Resource Manager-mallar finns i [Azure Resource Manager översikt](../azure-resource-manager/management/overview.md)
* Information om hur du kommer igång med Azure Storage finns i [Introduktion till Azure Storage](../storage/common/storage-introduction.md).
* Du hittar andra användbara Azure Automation runbooks i [Runbook-och modul gallerier för Azure Automation](automation-runbook-gallery.md).
* Du hittar andra användbara Resource Manager-mallar i [Azure snabb starts mallar](https://azure.microsoft.com/resources/templates/)



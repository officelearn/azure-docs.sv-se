---
title: Distribuera en Azure Resource Manager-mall i Azure Automation-runbook | Microsoft Docs
description: "Så här distribuerar du en Azure Resource Manager-mall som lagras i Azure Storage från en runbook"
services: automation
documentationcenter: dev-center-name
author: georgewallace
manager: carmonm
keywords: PowerShell, runbook, json, azure automation
ms.service: automation
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: TBD
ms.date: 07/09/2017
ms.author: gwallace
ms.openlocfilehash: dc283973efd936d50418d303bbc359dd4312a121
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Distribuera en Azure Resource Manager-mall i en Azure Automation PowerShell-runbook

Du kan skriva en [PowerShell för Azure Automation-runbook](automation-first-runbook-textual-powershell.md) som distribuerar en Azure-resurs med hjälp av en [Azure Resource Manager-mallen](../azure-resource-manager/resource-manager-create-first-template.md).

På så sätt kan du automatisera distributionen av Azure-resurser. Du kan underhålla Resource Manager-mallar på en central, säker plats, till exempel Azure Storage.

I det här avsnittet skapar vi en PowerShell-runbook som använder en Resource Manager-mall som lagras i [Azure Storage](../storage/common/storage-introduction.md) att distribuera ett nytt Azure Storage-konto.

## <a name="prerequisites"></a>Krav

För att kunna genomföra den här kursen behöver du följande:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller <a href="/pricing/free-account/" target="_blank">[registrera dig för ett kostnadsfritt konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* [Azure Storage-konto](../storage/common/storage-create-storage-account.md) där du kan lagra Resource Manager-mall
* Azure Powershell installeras på en lokal dator. Se [installera och konfigurera Azure Powershell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.1.0) information om hur du hämtar Azure PowerShell.

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
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
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

Vi använder PowerShell för att skapa en filresurs i Azure Storage och ladda upp den `TemplateTest.json` filen.
Instruktioner om hur du skapar en fil dela och överföra en fil i Azure portal finns [Kom igång med Azure File storage i Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starta PowerShell på den lokala datorn och kör följande kommandon för att skapa en filresurs och ladda upp Resource Manager-mallen till att filresursen.

```powershell
# Login to Azure
Login-AzureRmAccount

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

## <a name="create-the-powershell-runbook-script"></a>Skapa skript för PowerShell-runbook

Nu skapar vi ett PowerShell-skript som hämtar den `TemplateTest.json` från Azure Storage och distribuerar mallen om du vill skapa ett nytt Azure Storage-konto.

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
Add-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzureStorageContext -StorageAccountKey $StorageAccountKey

Get-AzureStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Spara filen lokalt som `DeployTemplate.ps1`.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importera och publicera en runbook i Azure Automation-konto

Nu vi använda PowerShell för att importera runbook till ditt Azure Automation-konto och publicera en runbook.
Information om hur du importerar och publicera en runbook i Azure portal finns [skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md).

Så här importerar du `DeployTemplate.ps1` i ditt Automation-konto som en PowerShell-runbook, kör du följande PowerShell-kommandon:

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
Import-AzureRmAutomationRunbook @

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzureRmAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Starta runbook

Nu vi starta runbook genom att anropa den [Start AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/start-azurermautomationrunbook?view=azurermps-4.1.0) cmdlet.

Information om hur du startar en runbook i Azure portal finns [starta en runbook i Azure Automation](automation-starting-a-runbook.md).

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

En runbook körs, och du kan kontrollera statusen genom att köra `$job.Status`.

Runbook hämtar Resource Manager-mallen och används för att distribuera ett nytt Azure Storage-konto.
Du kan se att det nya lagringskontot har skapats genom att köra följande kommando:
```powershell
Get-AzureRmStorageAccount
```

## <a name="summary"></a>Sammanfattning

Klart! Nu kan du använda mallar för Azure Automation och Azure Storage och Resource Manager för att distribuera alla dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

* Läs mer om Resource Manager-mallar i [översikt över Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* Om du vill komma igång med Azure Storage finns [introduktion till Azure Storage](../storage/common/storage-introduction.md).
* Du hittar andra användbara Azure Automation-runbooks [Azure Automation Runbook- och stänga](automation-runbook-gallery.md).
* Andra användbara Resource Manager-mallar finns [Azure Quickstart-mallar](https://azure.microsoft.com/resources/templates/)


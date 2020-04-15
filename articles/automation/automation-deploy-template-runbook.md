---
title: Distribuera en Azure Resource Manager-mall i en Azure Automation-runbook
description: Distribuera en Azure Resource Manager-mall som lagras i Azure Storage från en runbook
services: automation
ms.subservice: process-automation
ms.date: 03/16/2018
ms.topic: conceptual
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 575f954b346edb7d682e3fd0b432a257486bbfbb
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383286"
---
# <a name="deploy-an-azure-resource-manager-template-in-an-azure-automation-powershell-runbook"></a>Distribuera en Azure Resource Manager-mall i en Azure Automation PowerShell- runbook

Du kan skriva en [Azure Automation PowerShell-runbook](automation-first-runbook-textual-powershell.md) som distribuerar en Azure-resurs med hjälp av en [Azure Resource Management-mall](../azure-resource-manager/resource-manager-create-first-template.md).

På så sätt kan du automatisera distributionen av Azure-resurser. Du kan underhålla dina Resource Manager-mallar på en central, säker plats som Azure Storage.

I den här artikeln skapar vi en PowerShell-runbook som använder en Resource Manager-mall som lagras i [Azure Storage](../storage/common/storage-introduction.md) för att distribuera ett nytt Azure Storage-konto.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du följande objekt:

* En Azure-prenumeration. Om du inte har en ännu kan du [aktivera dina msdn-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller [registrera dig för ett gratis konto](https://azure.microsoft.com/free/).
* Ett [Automation-konto för Azure](automation-sec-configure-azure-runas-account.md) som runbooken ska ligga under och som ska användas för autentisering mot Azure-resurser.  Det här kontot måste ha behörighet att starta och stoppa den virtuella datorn.
* [Azure Storage-konto](../storage/common/storage-create-storage-account.md) där resource manager-mallen ska lagras
* Azure PowerShell installerat på en lokal dator. Mer information om hur du skaffar Azure PowerShell finns [i Installera Azure PowerShell-modulen.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)

## <a name="create-the-resource-manager-template"></a>Skapa Resource Manager-mallen

I det här exemplet använder vi en Resource Manager-mall som distribuerar ett nytt Azure Storage-konto.

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

Spara filen lokalt som **TemplateTest.json**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Spara Resource Manager-mallen i Azure Storage

Nu använder vi PowerShell för att skapa en Azure Storage-filresurs och ladda upp **filen TemplateTest.json.**
Instruktioner om hur du skapar en filresurs och laddar upp en fil i Azure-portalen finns i [Komma igång med Azure File Storage på Windows](../storage/files/storage-dotnet-how-to-use-files.md).

Starta PowerShell på den lokala datorn och kör följande kommandon för att skapa en filresurs och överföra Resource Manager-mallen till den filresursresursresursen.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzureStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzureStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzureStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Skapa PowerShell-runbook-skriptet

Nu skapar vi ett PowerShell-skript som hämtar **filen TemplateTest.json** från Azure Storage och distribuerar mallen för att skapa ett nytt Azure Storage-konto.

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
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
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
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
``` 

Spara filen lokalt som **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importera och publicera runbooken i ditt Azure Automation-konto

Nu använder vi PowerShell för att importera runbook till ditt Azure Automation-konto och sedan publicera runbook.
Information om hur du importerar och publicerar en runbook i Azure-portalen finns [i Hantera runbooks i Azure Automation](manage-runbooks.md).

Om du vill importera **DeployTemplate.ps1** till ditt Automation-konto som en PowerShell-runbook kör du följande PowerShell-kommandon:

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
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Starta runbook

Nu startar vi runbooken genom att anropa [Start-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0
) cmdlet. Information om hur du startar en runbook i Azure-portalen finns [i Starta en runbook i Azure Automation](automation-starting-a-runbook.md).

Kör följande kommandon i PowerShell-konsolen:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json' 
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Runbook körs och du kan kontrollera `$job.Status`dess status genom att köra .

Runbook hämtar Resource Manager-mallen och använder den för att distribuera ett nytt Azure Storage-konto.
Du kan se att det nya lagringskontot skapades genom att köra följande kommando:

```powershell
Get-AzStorageAccount
```

## <a name="summary"></a>Sammanfattning

Klart! Nu kan du använda Azure Automation och Azure Storage med Resource Manager-mallar för att distribuera alla dina Azure-resurser.

## <a name="next-steps"></a>Nästa steg

* Mer information om Resource Manager-mallar finns i [översikt över Azure Resource Manager](../azure-resource-manager/management/overview.md).
* Information om hur du kommer igång med Azure Storage finns [i Introduktion till Azure Storage](../storage/common/storage-introduction.md).
* Information om hur du hittar andra användbara Azure Automation-runbooksböcker finns i [Runbook- och modulgallerier för Azure Automation](automation-runbook-gallery.md).
* Information om hur du hittar andra användbara Resource Manager-mallar finns i [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/).
* En PowerShell-cmdlet-referens finns i [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).

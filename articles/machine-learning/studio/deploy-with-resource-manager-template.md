---
title: Distribuera en Machine Learning-arbetsytan med Azure Resource Manager | Microsoft Docs
description: "Så här distribuerar du en arbetsyta för Azure Machine Learning med Azure Resource Manager-mall"
services: machine-learning
documentationcenter: 
author: ahgyger
manager: haining
editor: garye
ms.assetid: 4955ac4d-ff99-4908-aa27-69b6bfcc8e85
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/15/2017
ms.author: ahgyger
ms.openlocfilehash: e3cbcb8118aa05e554b2493506280d0e24706059
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Distribuera Machine Learning-arbetsyta med hjälp av Azure Resource Manager
## <a name="introduction"></a>Introduktion
Med en Azure Resource Manager-mall för distribution av sparar du tid genom att ge ett skalbart sätt att distribuera sammankopplade komponenter med en verifiering och försök mekanism. Om du vill konfigurera Azure Machine Learning arbetsytor, till exempel behöver du först konfigurera en Azure storage-konto och sedan distribuera din arbetsyta. Anta att göra detta manuellt för hundratals arbetsytor. Ett enklare alternativ är att använda en Azure Resource Manager-mall för att distribuera en Azure Machine Learning-arbetsytan och alla dess beroenden. Den här artikeln tar dig igenom processen steg för steg. En bra översikt över Azure Resource Manager finns [översikt över Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Steg för steg: skapa en Machine Learning-arbetsytan
Vi ska skapa en Azure-resursgrupp och sedan distribuera ett nytt Azure storage-konto och en ny Azure Machine Learning-arbetsytan med hjälp av en Resource Manager-mall. När installationen är klar, ut vi viktig information om arbetsytor som har skapats (primärnyckel, workspaceID och URL: en till arbetsytan).

### <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall
En Machine Learning-arbetsytan kräver ett Azure storage-konto för att lagra dataset som är länkade till den.
Följande mall använder namnet på resursgruppen att generera lagringskontonamn och namnet på arbetsytan.  Dessutom används lagringskontonamn som en egenskap när du skapar arbetsytan.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Spara den här mallen som mlworkspace.json fil under c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Distribuera resursgrupp, baserat på mallen
* Öppna PowerShell
* Installera moduler för Azure Resource Manager och Azure-tjänsthantering  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   De här stegen hämta och installera modulerna som krävs för att slutföra stegen. Detta behöver bara göras en gång i miljön där du kör PowerShell-kommandon.   

* Autentisera till Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Det här steget måste upprepas för varje session. När autentiseringen är ska din prenumerationsinformation visas.

![Azure-konto][1]

Nu när vi har åtkomst till Azure kan vi skapa resursgruppen.

* Skapa en resursgrupp

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Kontrollera att resursgruppen har etablerats på rätt sätt. **ProvisioningState** ska vara ”lyckades”.
Resursgruppens namn används av mallen för att generera lagringskontonamn. Lagringskontonamnet måste vara mellan 3 och 24 tecken långt och innehålla siffror och gemena bokstäver.

![Resursgrupp][2]

* Med gruppdistributionen resurs kan distribuera en ny Machine Learning-arbetsytan.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

När distributionen är klar, är det enkelt komma åt egenskaper i arbetsytan som du har distribuerat. Du kan till exempel åtkomst till den primära nyckeln-Token.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Ett annat sätt att hämta token på befintlig arbetsyta är att använda kommandot Invoke-AzureRmResourceAction. Exempelvis kan du visa de primära och sekundära token av alla arbetsytor.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Efter att arbetsytan har etablerats kan du också automatisera många Azure Machine Learning Studio uppgifter med hjälp av den [PowerShell-modulen för Azure Machine Learning](http://aka.ms/amlps).

## <a name="next-steps"></a>Nästa steg
* Lär dig mer om [redigera Azure Resource Manager-mallar](../../azure-resource-manager/resource-group-authoring-templates.md). 
* Ta en titt på den [Azure Quickstart mallar databasen](https://github.com/Azure/azure-quickstart-templates). 
* Det här videoklippet om [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 

<!--Image references-->
[1]: ./media/deploy-with-resource-manager-template/azuresubscription.png
[2]: ./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->

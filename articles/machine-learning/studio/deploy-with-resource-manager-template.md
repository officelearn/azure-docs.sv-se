---
title: Distribuera Studio -arbetsyta (klassiskt) med Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Distribuera en arbetsyta för Azure Machine Learning Studio (klassisk) med Azure Resource Manager-mall
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: 34333d4fe6e9b34a0c8b56cca8123f4ed93a917a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218124"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Distribuera Azure Machine Learning Studio (klassisk) arbetsyta med Azure Resource Manager

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Om du använder en distributionsmall för Azure Resource Manager sparar du tid genom att ge dig ett skalbart sätt att distribuera sammankopplade komponenter med en verifierings- och återförsöksmekanism. Om du till exempel vill konfigurera Azure Machine Learning Studio (klassiska) arbetsytor måste du först konfigurera ett Azure-lagringskonto och sedan distribuera arbetsytan. Tänk dig att göra detta manuellt för hundratals arbetsytor. Ett enklare alternativ är att använda en Azure Resource Manager-mall för att distribuera en Studio -arbetsyta (klassisk) och alla dess beroenden. Den här artikeln tar dig igenom den här processen steg för steg. En bra översikt över Azure Resource Manager finns i [översikt över Azure Resource Manager](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Steg för steg: skapa en maskininlärningsarbetsyta
Vi skapar en Azure-resursgrupp och distribuerar sedan ett nytt Azure-lagringskonto och en ny Azure Machine Learning Studio (klassisk) arbetsyta med hjälp av en Resource Manager-mall. När distributionen är klar skriver vi ut viktig information om de arbetsytor som skapades (primärnyckeln, arbetsytanID och URL:en till arbetsytan).

### <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

En Machine Learning Workspace kräver ett Azure-lagringskonto för att lagra datauppsättningen som är länkad till den.
I följande mall används namnet på resursgruppen för att generera lagringskontonamnet och arbetsytans namn.  Den använder också lagringskontonamnet som en egenskap när arbetsytan skapas.

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
Spara den här mallen som filen mlworkspace.json under c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Distribuera resursgruppen, baserat på mallen

* Öppna PowerShell
* Installera moduler för Azure Resource Manager och Azure Service Management

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press "A")
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press "A")
Install-Module Azure -Scope CurrentUser
```

   Dessa steg hämta och installera de moduler som krävs för att slutföra de återstående stegen. Detta behöver bara göras en gång i den miljö där du kör PowerShell-kommandona.

* Autentisera till Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Det här steget måste upprepas för varje session. När prenumerationsinformationen har autentiserats ska den visas.

![Azure-konto](./media/deploy-with-resource-manager-template/azuresubscription.png)

Nu när vi har åtkomst till Azure kan vi skapa resursgruppen.

* Skapa en resursgrupp

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Kontrollera att resursgruppen är korrekt etablerad. **Etableringstaten** ska vara "lyckades".
Resursgruppsnamnet används av mallen för att generera lagringskontonamnet. Lagringskontonamnet får vara mellan 3 och 24 tecken långa och endast med siffror och gemener.

![Resursgrupp](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Distribuera en ny machine learning-arbetsyta med hjälp av resursgruppsdistributionen.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

När distributionen är klar är det enkelt att komma åt egenskaper för arbetsytan som du har distribuerat. Du kan till exempel komma åt primärnyckeltoken.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Ett annat sätt att hämta token för befintlig arbetsyta är att använda kommandot Invoke-AzResourceAction. Du kan till exempel lista de primära och sekundära tokens för alla arbetsytor.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
När arbetsytan har etablerats kan du också automatisera många klassiska Azure Machine Learning Studio-uppgifter med [PowerShell-modulen för Azure Machine Learning Studio (klassiskt).](https://aka.ms/amlps)

## <a name="next-steps"></a>Nästa steg

* Läs mer om [hur du skapar Azure Resource Manager-mallar](../../azure-resource-manager/templates/template-syntax.md).
* Ta en titt på [Azure Quickstart Templates Repository](https://github.com/Azure/azure-quickstart-templates).
* Titta på det här videoklippet om [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Se [referenshjälpen för Resurshanterarens mall](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->

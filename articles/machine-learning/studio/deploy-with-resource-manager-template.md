---
title: Distribuera Studio-arbetsytan (klassisk) med Azure Resource Manager
titleSuffix: ML Studio (classic) - Azure
description: Så här distribuerar du en arbets yta för Azure Machine Learning Studio (klassisk) med Azure Resource Manager-mall
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 02/05/2018
ms.openlocfilehash: f05ef9472f11a5025e9856cfb207cc0859f24c3e
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169353"
---
# <a name="deploy-azure-machine-learning-studio-classic-workspace-using-azure-resource-manager"></a>Distribuera Azure Machine Learning Studio-arbetsytan (klassisk) med Azure Resource Manager

Med en Azure Resource Manager-mall för distribution av sparar du tid genom att ge ett skalbart sätt att distribuera sammankopplade komponenter med en verifiering och återförsöksmekanism. Om du vill konfigurera Azure Machine Learning Studio (klassiska) arbets ytor, till exempel, måste du först konfigurera ett Azure Storage-konto och sedan distribuera din arbets yta. Tänk dig att göra detta manuellt för hundratals arbetsytor. Ett enklare alternativ är att använda en Azure Resource Manager mall för att distribuera en Studio (klassisk) arbets yta och alla dess beroenden. Den här artikeln tar dig igenom processen steg för steg. En bra översikt över Azure Resource Manager finns i [Azure Resource Manager översikt](../../azure-resource-manager/management/overview.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Steg för steg: skapa en Machine Learning-arbetsyta
Vi kommer att skapa en Azure-resurs grupp och sedan distribuera ett nytt Azure Storage-konto och en ny Azure Machine Learning Studio (klassisk) arbets yta med en Resource Manager-mall. När distributionen är klar kan ut vi viktig information om arbetsytor som har skapats (primärnyckel, workspaceID och URL: en till arbetsytan).

### <a name="create-an-azure-resource-manager-template"></a>Skapa en Azure Resource Manager-mall

En Machine Learning-arbetsyta kräver ett Azure storage-konto för att lagra den datauppsättning som är länkad till den.
Följande mall använder namnet på resursgruppen som ska generera lagringskontonamn och namnet på arbetsytan.  Den använder också namnet på lagringskontot som en egenskap när du skapar arbetsytan.

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
Spara den här mallen som mlworkspace.json filen under c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Distribuera resursgruppen, baserat på mallen

* Öppna PowerShell
* Installera moduler för Azure Resource Manager och Azure Service Management

```powershell
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module Az -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   De här stegen hämta och installera modulerna som krävs för att utföra stegen. Detta behöver bara göras en gång i miljön där du kör PowerShell-kommandon.

* Autentisera till Azure

```powershell
# Authenticate (enter your credentials in the pop-up window)
Connect-AzAccount
```
Det här steget måste du upprepa för varje session. När autentiseringen är klar visas din prenumerationsinformation.

![Azure-konto](./media/deploy-with-resource-manager-template/azuresubscription.png)

Nu när vi har åtkomst till Azure kan vi skapa resursgruppen.

* Skapa en resursgrupp

```powershell
$rg = New-AzResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Kontrollera att resursgruppen har etablerats korrekt. **ProvisioningState** bör vara "lyckades".
Resursgruppens namn används av mallen för att generera lagringskontonamnet. Lagringskontonamnet måste vara mellan 3 och 24 tecken och siffror och gemener.

![Resursgrupp](./media/deploy-with-resource-manager-template/resourcegroupprovisioning.png)

* Med distributionen av resursgrupper kan distribuera en ny Machine Learning-arbetsyta.

```powershell
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

När distributionen är klar, är det enkelt att egenskaper i arbetsytan som du har distribuerat. Du kan till exempel komma åt den primära nyckeln-Token.

```powershell
# Access Azure Machine Learning Studio Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Ett annat sätt att hämta token för en befintlig arbets yta är att använda kommandot Invoke-AzResourceAction. Du kan exempelvis visa de primära och sekundära token för alla arbetsytor.

```powershell
# List the primary and secondary tokens of all workspaces
Get-AzResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |ForEach-Object { Invoke-AzResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}
```
När arbets ytan har tillhandahållits kan du också automatisera många Azure Machine Learning Studio (klassiska) aktiviteter med [PowerShell-modulen för Azure Machine Learning Studio (klassisk)](https://aka.ms/amlps).

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du [redigerar Azure Resource Manager mallar](../../azure-resource-manager/templates/template-syntax.md).
* Ta en titt på [lagrings platsen för Azure snabb starts mallar](https://github.com/Azure/azure-quickstart-templates).
* Titta på den här videon om [Azure Resource Manager](https://channel9.msdn.com/Events/Ignite/2015/C9-39).
* Se [referens hjälp för Resource Manager-mall](https://docs.microsoft.com/azure/templates/microsoft.machinelearning/allversions)

<!--Link references-->

---
title: Distribuera resurser med PowerShell och mall | Microsoft Docs
description: Använd Azure Resource Manager och Azure PowerShell för att distribuera en resurser till Azure. Resurserna definieras i en Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 55903f35-6c16-4c6d-bf52-dbf365605c3f
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2017
ms.author: tomfitz
ms.openlocfilehash: 3a5daea55f2303f695627f06569f04e1c18b5e09
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-powershell"></a>Distribuera resurser med Resource Manager-mallar och Azure PowerShell

Den här artikeln förklarar hur du använder Azure PowerShell med Resource Manager-mallar för att distribuera resurserna till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure lösningar finns [översikt över Azure Resource Manager](resource-group-overview.md).

Resource Manager-mallen som du distribuerar kan antingen vara en lokal fil på din dator eller en extern fil som finns i en databas som GitHub. Den mall som du distribuerar i den här artikeln är tillgänglig i den [exempelmall](#sample-template) avsnitt, eller som [lagring mall i GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

Om det behövs installerar du Azure PowerShell-modulen med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzureRmAccount` för att skapa en anslutning till Azure.

<a id="deploy-local-template" />

## <a name="deploy-a-template-from-your-local-machine"></a>Distribuera en mall från den lokala datorn

När du distribuerar resurser till Azure måste du:

1. Logga in på ditt Azure-konto
2. Skapa en resursgrupp som fungerar som behållare för distribuerade resurser. Namnet på resursgruppen får bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Den kan inte sluta med en punkt.
3. Distribuera till resursgrupp mallen som definierar resurser för att skapa

En mall kan innehålla parametrar som gör att du kan anpassa distributionen. Exempelvis kan du ange värden som är anpassade för en viss miljö (t.ex dev, test- och). Mallen exempel definierar en parameter för lagringskontot SKU.

I följande exempel skapar en resursgrupp och distribuerar en mall från den lokala datorn:

```powershell
Connect-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
 
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När den är klar visas ett meddelande som innehåller resultatet:

```powershell
ProvisioningState       : Succeeded
```

## <a name="deploy-a-template-from-an-external-source"></a>Distribuera en mall från en extern källa

Du kanske föredrar att lagra dem på en extern plats istället för att lagra Resource Manager-mallar på den lokala datorn. Du kan lagra mallar i en källkontroll (till exempel GitHub). Eller, du kan lagra dem i ett Azure storage-konto för delad åtkomst i din organisation.

Distribuera en extern mall att använda den **TemplateUri** parameter. Använd URI: N i exemplet för att distribuera exempelmall från GitHub.

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS
```

I exemplet ovan kräver en offentligt tillgänglig URI för mallen, som fungerar i de flesta scenarier eftersom mallen inte innehålla känsliga data. Om du behöver ange känsliga data (till exempel en adminlösenord) skicka det värdet som en säker parameter. Om du inte vill att mallen ska vara offentligt tillgänglig, kan du dock skydda den genom att lagra det i en behållare för privat lagring. Information om hur du distribuerar en mall som kräver en signatur (SAS) token för delad åtkomst finns i [distribuera privata mallar med SAS-token](resource-manager-powershell-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Använd följande kommandon i molnet-gränssnittet:

```powershell
New-AzureRmResourceGroup -Name ExampleResourceGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri <copied URL> `
  -storageAccountType Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Distribuera till mer än en resursgrupp eller prenumeration

Normalt distribuerar du alla resurser i mallen som en enskild resursgrupp. Det finns emellertid scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper eller prenumerationer. Du kan distribuera till bara fem resursgrupper i en enda distribution. Mer information finns i [distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](resource-manager-cross-resource-group-deployment.md).

<a id="parameter-file" />

## <a name="parameter-files"></a>Parametern-filer

I stället för att skicka parametrar som infogade värden i skriptet kan det vara lättare att använda en JSON-fil som innehåller parametrarnas värden. Parameterfilen måste vara i följande format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Observera att avsnittet Parametrar innehåller ett parameternamn som matchar den parameter som definierats i mallen (storageAccountType). Parameterfilen innehåller ett värde för parametern. Det här värdet skickas automatiskt till mallen under distributionen. Du kan skapa flera parametern filer för olika distributionsscenarier och sedan ange lämpliga parameterfil. 

Kopiera föregående exempel och spara den som en fil med namnet `storage.parameters.json`.

Om du vill lägga till en lokal parameterfil, Använd den **TemplateParameterFile** parameter:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Om du vill lägga till en extern parameterfil, Använd den **TemplateParameterUri** parameter:

```powershell
New-AzureRmResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.parameters.json
```

Du kan använda infogade parametrar och en lokal parameterfil i samma åtgärd för distribution. Du kan till exempel ange vissa värden i den lokala parameterfilen och lägga till andra värden infogade under distributionen. Om du anger värden för en parameter i både lokala parameterfilen och infogade företräde infogade värdet.

Men när du använder en extern parameterfil, du kan inte skicka andra värden antingen infogade eller från en lokal fil. När du anger en parameterfil i den **TemplateParameterUri** parametern, alla infogade parametrar kommer att ignoreras. Ange alla värdena i den externa filen. Om din mall innehåller något känsligt värde som du inte kan ingå i parameterfilen, lägga till värdet i ett nyckelvalv eller dynamiskt tillhandahåller alla parametern värden infogad.

Om mallen innehåller en parameter med samma namn som en av parametrarna i PowerShell-kommandot, PowerShell visar parametern från din mall med username@Domain **från mall**. Till exempel en parameter med namnet **ResourceGroupName** i din mall står i konflikt med den **ResourceGroupName** parametern i den [ny AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment) cmdlet. Du uppmanas att ange ett värde för **ResourceGroupNameFromTemplate**. I allmänhet bör du undvika den här förvirring genom att namnge inte parametrar med samma namn som parametrar som används för distributionsåtgärder.

## <a name="test-a-template-deployment"></a>Testa en för malldistribution

Testa din mall och parametern-värden utan att faktiskt distribuera resurser med [Test AzureRmResourceGroupDeployment](/powershell/module/azurerm.resources/test-azurermresourcegroupdeployment). 

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType Standard_GRS
```

Om inga fel identifieras kommandot har slutförts utan ett svar. Om ett fel upptäcks returnerar kommandot ett felmeddelande. Försök att skicka ett felaktigt värde för lagringskontot SKU, returnerar till exempel följande fel:

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName testgroup `
  -TemplateFile c:\MyTemplates\storage.json -storageAccountType badSku

Code    : InvalidTemplate
Message : Deployment template validation failed: 'The provided value 'badSku' for the template parameter 'storageAccountType'
          at line '15' and column '24' is not valid. The parameter value is not part of the allowed value(s):
          'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.
Details :
```

Om mallen har ett syntaxfel, returnerar kommandot ett felmeddelande om att det inte kunde tolka mallen. Meddelandet Anger radnumret och positionen för parsningsfel.

```powershell
Test-AzureRmResourceGroupDeployment : After parsing a value an unexpected character was encountered: 
  ". Path 'variables', line 31, position 3.
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Om du vill använda fullständig läge i `Mode` parameter:

```powershell
New-AzureRmResourceGroupDeployment -Mode Complete -Name ExampleDeployment `
  -ResourceGroupName ExampleResourceGroup -TemplateFile c:\MyTemplates\storage.json 
```

## <a name="sample-template"></a>Exempelmall

Följande mall används i exemplen i den här artikeln. Kopiera och spara den som en fil med namnet storage.json. Information om hur du skapar den här mallen finns [skapa din första Azure Resource Manager-mallen](resource-manager-create-first-template.md).  

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

## <a name="next-steps"></a>Nästa steg
* Exemplen i den här artikeln distribuera resurser i en resursgrupp i din standard-prenumeration. Om du vill använda en annan prenumeration finns [hantera Azure-prenumerationer](/powershell/azure/manage-subscriptions-azureps).
* En fullständig exempelskript som distribuerar en mall finns i [distributionsskriptet för Resource Manager-mallen](resource-manager-samples-powershell-deploy.md).
* Information om hur du definierar parametrar i mallen finns [förstå struktur och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns [distribuera privata mallar med SAS-token](resource-manager-powershell-sas-token.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](resource-manager-subscription-governance.md).


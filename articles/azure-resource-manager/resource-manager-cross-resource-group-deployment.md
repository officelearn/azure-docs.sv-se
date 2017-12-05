---
title: "Distribuera Azure-resurser till flera grupper för prenumeration och resurs | Microsoft Docs"
description: "Visar hur du mer än en Azure-prenumeration och resurs grupp som mål under distributionen."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/01/2017
ms.author: tomfitz
ms.openlocfilehash: 763f46b9b5be7edf06ee0604bfc51a2482405b60
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2017
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp

Normalt distribuerar du alla resurser i mallen som en enskild resursgrupp. Det finns emellertid scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper eller prenumerationer. Exempelvis kanske du vill distribuera den säkerhetskopiera virtuella för Azure Site Recovery till en separat resursgrupp och plats. Resource Manager kan du använda kapslade mallar mål olika prenumerationer och resursgrupper än prenumeration och resursgrupp som används för den överordnade mallen.

Resursgruppen är en behållare för livscykeln för programmet och dess mängd resurser. Du skapar resursgruppen utanför mallen och ange resursgrupp som mål under distributionen. En introduktion till resursgrupper finns [översikt över Azure Resource Manager](resource-group-overview.md).

## <a name="specify-a-subscription-and-resource-group"></a>Ange en prenumeration och resurs

Om du vill ange en annan resurs, måste du använda en mall för kapslade eller länkade under distributionen. Den `Microsoft.Resources/deployments` tillhandahåller resurstypen parametrar för `subscriptionId` och `resourceGroup`. Dessa egenskaper kan du ange en annan prenumeration och resurs grupp för den kapslade distributionen. Alla resursgrupper måste finnas innan du kör distributionen. Om du inte anger antingen gruppen prenumerations-ID eller en resurs, prenumeration och resursgrupp från den överordnade mallen används.

I följande exempel distribuerar två lagringskonton - en i resursgruppen som anges under distributionen, och en i en resursgrupp anges i den `secondResourceGroup` parameter:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "secondResourceGroup": {
            "type": "string"
        },
        "secondSubscriptionID": {
            "type": "string",
            "defaultValue": ""
        },
        "secondStorageLocation": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "firstStorageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]",
        "secondStorageName": "[concat(parameters('storagePrefix'), uniqueString(parameters('secondSubscriptionID'), parameters('secondResourceGroup')))]"
    },
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "nestedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "[parameters('secondResourceGroup')]",
            "subscriptionId": "[parameters('secondSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Storage/storageAccounts",
                            "name": "[variables('secondStorageName')]",
                            "apiVersion": "2017-06-01",
                            "location": "[parameters('secondStorageLocation')]",
                            "sku":{
                                "name": "Standard_LRS"
                            },
                            "kind": "Storage",
                            "properties": {
                            }
                        }
                    ]
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[variables('firstStorageName')]",
            "apiVersion": "2017-06-01",
            "location": "[resourceGroup().location]",
            "sku":{
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        }
    ]
}
```

Om du ställer in `resourceGroup` till namnet på en resursgrupp som inte finns misslyckas distributionen.

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill distribuera mallen exempel Använd en version av Azure PowerShell eller Azure CLI från maj 2017 eller senare. De här exemplen använder den [mellan prenumeration mallen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) i GitHub.

### <a name="two-resource-groups-in-the-same-subscription"></a>Två resursgrupper i samma prenumeration

För PowerShell för att distribuera två storage-konton till två resursgrupper i samma prenumeration, Använd:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzureRmResourceGroup -Name $firstRG -Location southcentralus
New-AzureRmResourceGroup -Name $secondRG -Location eastus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

För Azure CLI för att distribuera två storage-konton till två resursgrupper i samma prenumeration, Använd:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

När distributionen är klar kan du se två resursgrupper. Varje resursgrupp innehåller ett lagringskonto.

### <a name="two-resource-groups-in-different-subscriptions"></a>Två resursgrupper för olika prenumerationer

För PowerShell för att distribuera två storage-konton till två prenumerationer, Använd:

```powershell
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzureRmSubscription -Subscription $secondSub
New-AzureRmResourceGroup -Name $secondRG -Location eastus

Select-AzureRmSubscription -Subscription $firstSub
New-AzureRmResourceGroup -Name $firstRG -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

För Azure CLI för att distribuera två storage-konton till två prenumerationer, Använd:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

## <a name="use-the-resourcegroup-function"></a>Använd funktionen resourceGroup()

Mellan distributionen av resursgrupper, för den [resourceGroup() funktionen](resource-group-template-functions-resource.md#resourcegroup) matchar på olika sätt beroende på hur du anger den kapslade mallen. 

Om du bäddar in en mall i en annan mall löser resourceGroup() i den kapslade mallen till den överordnade resursgruppen. Ett inbäddat mallen använder följande format:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() refers to parent resource group
    }
}
```

Om du länkar till en separat mall löser resourceGroup() i mallen länkade till den kapslade resursgruppen. En länkad mall används följande format:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() in linked template refers to linked resource group
    }
}
```

Att testa olika sätt `resourceGroup()` matchar, distribuera ett [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) som returnerar gruppobjektet resurs för överordnat mallen, infogade mall och länkade mall. Den överordnade och infogade mall både motsvara samma resursgrupp. Den länkade mallen matchar länkade resursgruppen.

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Om du använder Azure CLI använder du:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du definierar parametrar i mallen finns [förstå struktur och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns [distribuera privata mallar med SAS-token](resource-manager-powershell-sas-token.md).

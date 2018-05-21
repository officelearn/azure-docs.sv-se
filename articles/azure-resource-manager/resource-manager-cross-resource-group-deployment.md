---
title: Distribuera Azure-resurser till flera grupper för prenumeration och resurs | Microsoft Docs
description: Visar hur du mer än en Azure-prenumeration och resurs grupp som mål under distributionen.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/13/2018
ms.author: tomfitz
ms.openlocfilehash: c7e9807e7195be47bf7874837ff9428c90abbcee
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp

Normalt distribuerar du alla resurser i mallen för en enskild [resursgruppen](resource-group-overview.md). Det finns emellertid scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper eller prenumerationer. Exempelvis kanske du vill distribuera den säkerhetskopiera virtuella för Azure Site Recovery till en separat resursgrupp och plats. Resource Manager kan du använda kapslade mallar mål olika prenumerationer och resursgrupper än prenumeration och resursgrupp som används för den överordnade mallen.

> [!NOTE]
> Du kan distribuera till bara fem resursgrupper i en enda distribution. Den här begränsningen innebär vanligtvis, du kan distribuera till en resursgrupp som angetts för den överordnade mallen och upp till fyra resursgrupper i kapslade eller länkade distributioner. Men om överordnade mallen innehåller endast kapslade eller länkade mallar och har inte själva distribuera resurser, kan du inkludera upp till fem resursgrupper i kapslade eller länkade distributioner.

## <a name="specify-a-subscription-and-resource-group"></a>Ange en prenumeration och resurs

Använd en kapslad eller länkade mall om du vill ange en annan resurs. Den `Microsoft.Resources/deployments` tillhandahåller resurstypen parametrar för `subscriptionId` och `resourceGroup`. Dessa egenskaper kan du ange en annan prenumeration och resurs grupp för den kapslade distributionen. Alla resursgrupper måste finnas innan du kör distributionen. Om du inte anger antingen gruppen prenumerations-ID eller en resurs, prenumeration och resursgrupp från den överordnade mallen används.

Det konto som används för att distribuera mallen måste ha behörighet att distribuera till angivet prenumerations-ID. Om den angivna prenumerationen finns i en annan Azure Active Directory-klient, måste du [lägga till gästanvändare från en annan katalog](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Om du vill ange en annan resursgrupp och en prenumeration, använder du:

```json
"resources": [
    {
        "apiVersion": "2017-05-10",
        "name": "nestedTemplate",
        "type": "Microsoft.Resources/deployments",
        "resourceGroup": "[parameters('secondResourceGroup')]",
        "subscriptionId": "[parameters('secondSubscriptionID')]",
        ...
    }
]
```

Om din resursgrupper finns i samma prenumeration måste du ta bort den **subscriptionId** värde.

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

Om du vill distribuera mallen exempel använda Azure PowerShell 4.0.0 eller senare, eller Azure CLI 2.0.0 eller senare.

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

## <a name="example-templates"></a>Exempel mallar

Följande mallar visar flera distributionen av resursgrupper. Skript för att distribuera mallarna visas efter tabellen.

|Mall  |Beskrivning  |
|---------|---------|
|[Mellan prenumeration mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Distribuerar ett lagringskonto i en resursgrupp och ett lagringskonto till en andra resursgrupp. Innehåller ett värde för prenumerations-ID när andra resursgruppen finns i en annan prenumeration. |
|[Mellan resurs grupp Egenskaper mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Visar hur `resourceGroup()` fungerar matchar. Den distribuerar inte några resurser. |

### <a name="powershell"></a>PowerShell

För PowerShell för att distribuera två storage-konton till två resursgrupper i den **samma prenumeration**, Använd:

```azurepowershell-interactive
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

För PowerShell för att distribuera två storage-konton till **två prenumerationer**, Använd:

```azurepowershell-interactive
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

För PowerShell för att testa hur **resurs gruppobjekt** löser för överordnade mallen, infogade mall och länkade mall används:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

I föregående exempel, både **parentRG** och **inlineRG** motsvara **parentGroup**. **linkedRG** matchar **linkedGroup**. Utdata från föregående exempel är:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 inlineRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
                                               "name": "parentGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
 linkedRG         Object                     {
                                               "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
                                               "name": "linkedGroup",
                                               "location": "southcentralus",
                                               "properties": {
                                                 "provisioningState": "Succeeded"
                                               }
                                             }
```

### <a name="azure-cli"></a>Azure CLI

För att distribuera två storage-konton till två resursgrupper i Azure CLI i **samma prenumeration**, Använd:

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

För Azure CLI, att distribuera två storage-konton till **två prenumerationer**, Använd:

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

För Azure CLI, att testa hur **resurs gruppobjekt** löser för överordnade mallen, infogade mall och länkade mall används:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

I föregående exempel, både **parentRG** och **inlineRG** motsvara **parentGroup**. **linkedRG** matchar **linkedGroup**. Utdata från föregående exempel är:

```azurecli
...
"outputs": {
  "inlineRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "linkedRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/linkedGroup",
      "location": "southcentralus",
      "name": "linkedGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  },
  "parentRG": {
    "type": "Object",
    "value": {
      "id": "/subscriptions/<subscription-id>/resourceGroups/parentGroup",
      "location": "southcentralus",
      "name": "parentGroup",
      "properties": {
        "provisioningState": "Succeeded"
      }
    }
  }
},
...
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du definierar parametrar i mallen finns [förstå struktur och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns [distribuera privata mallar med SAS-token](resource-manager-powershell-sas-token.md).

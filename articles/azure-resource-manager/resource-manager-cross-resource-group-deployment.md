---
title: Distribuera Azure-resurser till flera grupper för prenumeration och resursgrupp | Microsoft Docs
description: Lär dig att fokusera på mer än en Azure-prenumeration och resursgrupp grupp under distributionen.
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
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: fec075a744b5f47a4be7f1b960cceedfea7b9a2c
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648316"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp

Normalt distribuerar du alla resurser i mallen till en enda [resursgrupp](resource-group-overview.md). Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans, men placera dem i olika resursgrupper eller prenumerationer. Du kanske vill distribuera den säkerhetskopiera virtuella datorn för Azure Site Recovery till en separat resursgrupp och plats. Resource Manager kan du använda kapslade mallar target olika prenumerationer och resursgrupper än den prenumerationen och resursgruppen som används för den överordnade mallen.

> [!NOTE]
> Du kan distribuera till endast fem resursgrupper i samma distribution. Den här begränsningen innebär normalt att du kan distribuera till en resursgrupp har angetts för den överordnade mallen och upp till fyra resursgrupper i kapslad eller länkad distributioner. Men om överordnade mallen innehåller endast kapslad eller länkad mallar och har inte själva distribuera några resurser, kan du inkludera upp till fem resursgrupper i kapslad eller länkad distributioner.

## <a name="specify-a-subscription-and-resource-group"></a>Ange en prenumeration och resursgrupp grupp

Använd en kapslad eller länkad mall om du vill ange en annan resurs. Den `Microsoft.Resources/deployments` resurstyp innehåller parametrar för `subscriptionId` och `resourceGroup`. De här egenskaperna kan du ange en annan prenumeration och resursgrupp grupp för den kapslade distributionen. Alla resursgrupper måste finnas innan du kör distributionen. Om du inte anger antingen gruppen prenumerations-ID eller resurs, prenumeration och resursgrupp från den överordnade mallen används.

Det konto som används för att distribuera mallen måste ha behörighet att distribuera till angivna prenumerations-ID. Om den angivna prenumerationen finns i en annan Azure Active Directory-klient, måste du [lägga till gästanvändare från en annan katalog](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

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

Om resursgrupper finns i samma prenumeration, kan du ta bort den **subscriptionId** värde.

I följande exempel distribuerar två lagringskonton – en i resursgruppen som anges under distributionen, och en i en resursgrupp har angetts i den `secondResourceGroup` parameter:

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

Om du ställer in `resourceGroup` till namnet på en resursgrupp som inte finns, distributionen misslyckas.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Använd funktionerna resourceGroup() och subscription()

Mellan distribution av resursgrupper, för den [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) och [subscription()](resource-group-template-functions-resource.md#subscription) functions matcha på olika sätt beroende på hur du anger den kapslade mallen. 

Om du bäddar in en mall i en annan mall matcha funktionerna i den kapslade mallen till den överordnade resursgrupp och prenumeration. En mall för inbäddade använder följande format:

```json
"apiVersion": "2017-05-10",
"name": "embeddedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "template": {
        ...
        resourceGroup() and subscription() refer to parent resource group/subscription
    }
}
```

Om du länkar till en separat mall kan matcha funktionerna i länkad mall till kapslad resursgruppen och prenumerationen. En länkad mall används följande format:

```json
"apiVersion": "2017-05-10",
"name": "linkedTemplate",
"type": "Microsoft.Resources/deployments",
"resourceGroup": "crossResourceGroupDeployment",
"properties": {
    "mode": "Incremental",
    "templateLink": {
        ...
        resourceGroup() and subscription() in linked template refer to linked resource group/subscription
    }
}
```

## <a name="example-templates"></a>Exempel på mallar

Följande mallar visar flera resursgrupper. Skript för att distribuera mallar visas efter tabellen.

|Mall  |Beskrivning  |
|---------|---------|
|[Mellan prenumeration mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Distribuerar ett lagringskonto till en resursgrupp och ett lagringskonto till en andra resursgrupp. Inkludera ett värde för prenumerations-ID när andra resursgruppen är i en annan prenumeration. |
|[Flera egenskaper för resursgruppsmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Visar hur `resourceGroup()` fungerar matchas. Den distribuerar inte några resurser. |

### <a name="powershell"></a>PowerShell

För PowerShell, för att distribuera två lagringskonton till två resursgrupper i den **samma prenumeration**, Använd:

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

För PowerShell för att distribuera två lagringskonton till **två prenumerationer**, Använd:

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

För PowerShell, för att testa hur **resource gruppobjektet** löser för en överordnad mall, infogade mall och länkad mall användning:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name parentGroup -Location southcentralus
New-AzureRmResourceGroup -Name inlineGroup -Location southcentralus
New-AzureRmResourceGroup -Name linkedGroup -Location southcentralus

New-AzureRmResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

I föregående exempel, både **parentRG** och **inlineRG** matcha **parentGroup**. **linkedRG** motsvarar **linkedGroup**. Utdata från föregående exempel är:

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

För Azure CLI för att distribuera två lagringskonton till två resursgrupper i den **samma prenumeration**, Använd:

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

För Azure CLI för att distribuera två lagringskonton till **två prenumerationer**, Använd:

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

För Azure CLI för att testa hur **resource gruppobjektet** löser för en överordnad mall, infogade mall och länkad mall användning:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

I föregående exempel, både **parentRG** och **inlineRG** matcha **parentGroup**. **linkedRG** motsvarar **linkedGroup**. Utdata från föregående exempel är:

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

* Information om hur du definierar parametrar i mallen finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md).

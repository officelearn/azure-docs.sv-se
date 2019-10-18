---
title: Distribuera Azure-resurser kors prenumeration & resurs grupp
description: Visar hur du riktar in mer än en Azure-prenumeration och resurs grupp under distributionen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/02/2018
ms.author: tomfitz
ms.openlocfilehash: c90096043f54eb8db5834fbe83ed1d6ae710d371
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72528323"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuera Azure-resurser till mer än en prenumeration eller resurs grupp

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Normalt distribuerar du alla resurser i mallen till en enda [resurs grupp](resource-group-overview.md). Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans, men placera dem i olika resurs grupper eller prenumerationer. Du kanske till exempel vill distribuera den virtuella datorn för säkerhets kopiering för Azure Site Recovery till en separat resurs grupp och plats. Med Resource Manager kan du använda kapslade mallar för att rikta olika prenumerationer och resurs grupper än den prenumeration och resurs grupp som används för den överordnade mallen.

> [!NOTE]
> Du kan bara distribuera till fem resurs grupper i en enda distribution. Den här begränsningen innebär vanligt vis att du kan distribuera till en resurs grupp som angetts för den överordnade mallen och upp till fyra resurs grupper i kapslade eller länkade distributioner. Men om den överordnade mallen bara innehåller kapslade eller länkade mallar och inte själv distribuerar några resurser, kan du inkludera upp till fem resurs grupper i kapslade eller länkade distributioner.

## <a name="specify-a-subscription-and-resource-group"></a>Ange en prenumeration och resurs grupp

Använd en kapslad eller länkad mall för att rikta in en annan resurs. Resurs typen `Microsoft.Resources/deployments` innehåller parametrar för `subscriptionId` och `resourceGroup`. Med dessa egenskaper kan du ange en annan prenumeration och resurs grupp för den kapslade distributionen. Alla resurs grupper måste finnas innan du kör distributionen. Om du inte anger något av prenumerations-ID: t eller resurs gruppen används prenumerationen och resurs gruppen från den överordnade mallen.

Det konto som du använder för att distribuera mallen måste ha behörighet att distribuera till det angivna prenumerations-ID: t. Om den angivna prenumerationen finns i en annan Azure Active Directory klient organisation måste du [lägga till gäst användare från en annan katalog](../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Om du vill ange en annan resurs grupp och prenumeration använder du:

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

Om dina resurs grupper finns i samma prenumeration kan du ta bort **subscriptionId** -värdet.

I följande exempel distribueras två lagrings konton – en i resurs gruppen som anges under distributionen och en i en resurs grupp som anges i parametern `secondResourceGroup`:

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

Om du anger `resourceGroup` till namnet på en resurs grupp som inte finns, Miss lyckas distributionen.

## <a name="use-the-resourcegroup-and-subscription-functions"></a>Använd funktionerna resourceGroup () och Subscription ()

För distributioner mellan olika resurs grupper fungerar funktionerna [resourceGroup ()](resource-group-template-functions-resource.md#resourcegroup) och [Subscription ()](resource-group-template-functions-resource.md#subscription) på olika sätt beroende på hur du anger den kapslade mallen. 

Om du bäddar in en mall i en annan mall, matchas funktionerna i den kapslade mallen till den överordnade resurs gruppen och prenumerationen. En inbäddad mall använder följande format:

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

Om du länkar till en separat mall matchas funktionerna i den länkade mallen till den kapslade resurs gruppen och prenumerationen. En länkad mall använder följande format:

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

I följande mallar demonstreras flera resurs grupps distributioner. Skript för att distribuera mallarna visas efter tabellen.

|Mall  |Beskrivning  |
|---------|---------|
|[Mall för över prenumerationer](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crosssubscription.json) |Distribuerar ett lagrings konto till en resurs grupp och ett lagrings konto till en andra resurs grupp. Inkludera ett värde för prenumerations-ID: t när den andra resurs gruppen finns i en annan prenumeration. |
|[Mall för egenskaper för kors resurs grupp](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) |Visar hur den `resourceGroup()` funktionen matchar. Inga resurser distribueras. |

### <a name="powershell"></a>PowerShell

För att PowerShell ska kunna distribuera två lagrings konton till två resurs grupper i **samma prenumeration**använder du:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Om du vill distribuera två lagrings konton till **två prenumerationer**för PowerShell använder du:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

För PowerShell, för att testa hur **resurs grupps objekt** matchas för den överordnade mallen, den infogade mallen och den länkade mallen, använder du:

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

I föregående exempel matchar både **parentRG** och **inlineRG** till **parentGroup**. **linkedRG** matchar **linkedGroup**. Utdata från föregående exempel är:

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

För att kunna distribuera två lagrings konton till två resurs grupper i **samma prenumeration**använder du Azure CLI för att använda:

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

För att distribuera två lagrings konton till **två prenumerationer**i Azure CLI använder du:

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

För Azure CLI, för att testa hur **resurs grupps objekt** matchas för den överordnade mallen, den infogade mallen och den länkade mallen, använder du:

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

I föregående exempel matchar både **parentRG** och **inlineRG** till **parentGroup**. **linkedRG** matchar **linkedGroup**. Utdata från föregående exempel är:

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

* Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](resource-manager-powershell-sas-token.md).

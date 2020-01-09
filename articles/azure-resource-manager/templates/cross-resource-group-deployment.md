---
title: Distribuera resurser mellan prenumerationer & resurs grupp
description: Visar hur du riktar in mer än en Azure-prenumeration och resurs grupp under distributionen.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 87cf28a1cb47621539d53d80f59670103ce36aee
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75477959"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuera Azure-resurser till mer än en prenumeration eller resurs grupp

Normalt distribuerar du alla resurser i mallen till en enda [resurs grupp](../management/overview.md). Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans, men placera dem i olika resurs grupper eller prenumerationer. Du kanske till exempel vill distribuera den virtuella datorn för säkerhets kopiering för Azure Site Recovery till en separat resurs grupp och plats. Med Resource Manager kan du använda kapslade mallar för att hantera mer än en prenumeration och resurs grupp.

> [!NOTE]
> Du kan bara distribuera till fem resurs grupper i en enda distribution. Den här begränsningen innebär vanligt vis att du kan distribuera till en resurs grupp som angetts för den överordnade mallen och upp till fyra resurs grupper i kapslade eller länkade distributioner. Men om den överordnade mallen bara innehåller kapslade eller länkade mallar och inte själv distribuerar några resurser, kan du inkludera upp till fem resurs grupper i kapslade eller länkade distributioner.

## <a name="specify-subscription-and-resource-group"></a>Ange prenumeration och resurs grupp

Om du vill rikta en annan resurs grupp eller prenumeration använder du en [kapslad eller länkad mall](linked-templates.md). Resurs typen `Microsoft.Resources/deployments` innehåller parametrar för `subscriptionId` och `resourceGroup`, vilket gör att du kan ange prenumeration och resurs grupp för den kapslade distributionen. Om du inte anger prenumerations-ID eller resurs grupp används prenumerationen och resurs gruppen från den överordnade mallen. Alla resurs grupper måste finnas innan du kör distributionen.

Det konto som du använder för att distribuera mallen måste ha behörighet att distribuera till det angivna prenumerations-ID: t. Om den angivna prenumerationen finns i en annan Azure Active Directory klient organisation måste du [lägga till gäst användare från en annan katalog](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

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

I följande exempel distribueras två lagrings konton. Det första lagrings kontot distribueras till den resurs grupp som anges under distributionen. Det andra lagrings kontot distribueras till den resurs grupp som anges i `secondResourceGroup` och `secondSubscriptionID` parametrar:

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
        },
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
        }
    ]
}
```

Om du anger `resourceGroup` till namnet på en resurs grupp som inte finns, Miss lyckas distributionen.

Testa föregående mall och se resultatet med PowerShell eller Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration**använder du:

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

Om du vill distribuera två lagrings konton till **två prenumerationer**använder du:

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration**använder du:

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

Om du vill distribuera två lagrings konton till **två prenumerationer**använder du:

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

---

## <a name="use-functions"></a>Använda funktioner

Funktionerna [resourceGroup ()](template-functions-resource.md#resourcegroup) och [Subscription ()](template-functions-resource.md#subscription) löses på olika sätt beroende på hur du anger mallen. När du länkar till en extern mall, matchas alltid funktionerna med omfånget för mallen. När du kapslar in en mall i en överordnad mall använder du egenskapen `expressionEvaluationOptions` för att ange om funktionerna ska matcha till resurs gruppen och prenumerationen för den överordnade mallen eller den kapslade mallen. Ange egenskapen till `inner` för att matcha omfånget för den kapslade mallen. Ställ in egenskapen på `outer` för att matcha omfånget för den överordnade mallen.

Följande tabell visar om funktionerna matchas med den överordnade eller inbäddade resurs gruppen och prenumerationen.

| Malltyp | Omfång | Upplösning |
| ------------- | ----- | ---------- |
| Nest        | yttre (standard) | Överordnad resurs grupp |
| Nest        | innersta | Under resurs grupp |
| länkade        | Gäller inte   | Under resurs grupp |

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) visar:

* kapslad mall med standard omfång (yttre)
* kapslad mall med inre omfång
* länkad mall

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "defaultScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "name": "innerScopeTemplate",
            "apiVersion": "2017-05-10",
            "resourceGroup": "inlineGroup",
            "properties": {
                "expressionEvaluationOptions": {
                    "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                    ],
                    "outputs": {
                        "resourceGroupOutput": {
                            "type": "string",
                            "value": "[resourceGroup().name]"
                        }
                    }
                },
                "parameters": {}
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "resourceGroup": "linkedGroup",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/resourceGroupName.json"
                },
                "parameters": {}
            }
        }
    ],
    "outputs": {
        "parentRG": {
            "type": "string",
            "value": "[concat('Parent resource group is ', resourceGroup().name)]"
        },
        "defaultScopeRG": {
            "type": "string",
            "value": "[concat('Default scope resource group is ', reference('defaultScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "innerScopeRG": {
            "type": "string",
            "value": "[concat('Inner scope resource group is ', reference('innerScopeTemplate').outputs.resourceGroupOutput.value)]"
        },
        "linkedRG": {
            "type": "string",
            "value": "[concat('Linked resource group is ', reference('linkedTemplate').outputs.resourceGroupOutput.value)]"
        }
    }
}
```

Testa föregående mall och se resultatet med PowerShell eller Azure CLI.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Utdata från föregående exempel är:

```powershell
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az group deployment create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json 
```

Utdata från föregående exempel är:

```azurecli
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Nästa steg

* Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
* Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](secure-template-with-sas-token.md).

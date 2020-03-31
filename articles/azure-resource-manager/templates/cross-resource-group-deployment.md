---
title: Distribuera resurser över prenumeration & resursgrupp
description: Visar hur du inriktar dig på mer än en Azure-prenumeration och resursgrupp under distributionen.
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 70868f5a3598c26ffff81f0ad3536a6c5c0a7e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460355"
---
# <a name="deploy-azure-resources-to-more-than-one-subscription-or-resource-group"></a>Distribuera Azure-resurser till mer än en prenumeration eller resursgrupp

Vanligtvis distribuerar du alla resurser i mallen till en enda [resursgrupp](../management/overview.md). Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper eller prenumerationer. Du kanske till exempel vill distribuera den virtuella säkerhetskopieringsmaskinen för Azure Site Recovery till en separat resursgrupp och plats. Med Resurshanteraren kan du använda kapslade mallar för att rikta in dig på mer än en prenumerations- och resursgrupp.

> [!NOTE]
> Du kan distribuera till endast fem resursgrupper i en enda distribution. Den här begränsningen innebär vanligtvis att du kan distribuera till en resursgrupp som angetts för den överordnade mallen och upp till fyra resursgrupper i kapslade eller länkade distributioner. Om den överordnade mallen bara innehåller kapslade eller länkade mallar och inte själv distribuerar några resurser, kan du inkludera upp till fem resursgrupper i kapslade eller länkade distributioner.

## <a name="specify-subscription-and-resource-group"></a>Ange prenumeration och resursgrupp

Om du vill rikta in dig på en annan resursgrupp eller prenumeration använder du en [kapslad eller länkad mall](linked-templates.md). Resurstypen `Microsoft.Resources/deployments` innehåller `subscriptionId` parametrar `resourceGroup`för och , som gör att du kan ange prenumerations- och resursgruppen för den kapslade distributionen. Om du inte anger prenumerations-ID eller resursgrupp används prenumerations- och resursgruppen från den överordnade mallen. Alla resursgrupper måste finnas innan distributionen körs.

Kontot som du använder för att distribuera mallen måste ha behörighet att distribuera till det angivna prenumerations-ID:et. Om den angivna prenumerationen finns i en annan Azure Active Directory-klient måste du [lägga till gästanvändare från en annan katalog](../../active-directory/active-directory-b2b-what-is-azure-ad-b2b.md).

Om du vill ange en annan resursgrupp och prenumeration använder du:

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

Om resursgrupperna finns i samma prenumeration kan du ta bort **värdet för subscriptionId.**

I följande exempel distribueras två lagringskonton. Det första lagringskontot distribueras till resursgruppen som anges under distributionen. Det andra lagringskontot distribueras till `secondResourceGroup` resursgruppen som anges i parametrarna och: `secondSubscriptionID`

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
      "apiVersion": "2017-06-01",
      "name": "[variables('firstStorageName')]",
      "location": "[resourceGroup().location]",
      "sku":{
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "nestedTemplate",
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
            "apiVersion": "2017-06-01",
            "name": "[variables('secondStorageName')]",
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

Om du `resourceGroup` anger namnet på en resursgrupp som inte finns misslyckas distributionen.

Om du vill testa föregående mall och se resultaten använder du PowerShell eller Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Om du vill distribuera två lagringskonton till två resursgrupper i **samma prenumeration**använder du:

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

Om du vill distribuera två **lagringskonton**till två prenumerationer använder du:

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

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill distribuera två lagringskonton till två resursgrupper i **samma prenumeration**använder du:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Om du vill distribuera två **lagringskonton**till två prenumerationer använder du:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="use-functions"></a>Använda funktioner

Funktionerna [resourceGroup()](template-functions-resource.md#resourcegroup) och [subscription()](template-functions-resource.md#subscription) matchas på olika sätt beroende på hur du anger mallen. När du länkar till en extern mall löser funktionerna alltid till omfånget för mallen. När du kapsla en mall `expressionEvaluationOptions` i en överordnad mall använder du egenskapen för att ange om funktionerna ska matchas till resursgruppen och prenumerationen för den överordnade mallen eller den kapslade mallen. Ange egenskapen `inner` så att den ska matcha till omfånget för den kapslade mallen. Ange egenskapen `outer` som den ska matcha till omfattningen av den överordnade mallen.

I följande tabell visas om funktionerna matchas till den överordnade eller inbäddade resursgruppen och prenumerationen.

| Malltyp | Omfång | Lösning |
| ------------- | ----- | ---------- |
| Kapslade        | yttre (standard) | Överordnad resursgrupp |
| Kapslade        | Inre | Resursgrupp för underresurs |
| Länkade        | Ej tillämpligt   | Resursgrupp för underresurs |

Följande [exempelmall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) visar:

* kapslad mall med standardomfång (yttre)
* kapslad mall med inre scope
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
      "apiVersion": "2017-05-10",
      "name": "defaultScopeTemplate",
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
      "apiVersion": "2017-05-10",
      "name": "innerScopeTemplate",
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
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
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

Om du vill testa föregående mall och se resultaten använder du PowerShell eller Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Utdata från föregående exempel är:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Utdata från föregående exempel är:

```output
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

* Information om hur du definierar parametrar i mallen finns i [Förstå strukturen och syntaxen för Azure Resource Manager-mallar](template-syntax.md).
* Tips om hur du löser vanliga distributionsfel finns i [Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [Distribuera privat mall med SAS-token](secure-template-with-sas-token.md).

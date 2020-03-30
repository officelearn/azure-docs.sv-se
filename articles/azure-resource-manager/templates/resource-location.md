---
title: Plats för mallresurs
description: Beskriver hur du anger resursplats i en Azure Resource Manager-mall.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a8324dac1232eecd5624e5f1dc0e6656295c0a10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80156437"
---
# <a name="set-resource-location-in-arm-template"></a>Ange resursplats i ARM-mall

När du distribuerar en ARM-mall (Azure Resource Manager) måste du ange en plats för varje resurs. Platsen behöver inte vara samma plats som resursgruppsplatsen.

## <a name="get-available-locations"></a>Få tillgängliga platser

Olika resurstyper stöds på olika platser. Om du vill hämta de platser som stöds för en resurstyp använder du Azure PowerShell eller Azure CLI.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Använd platsparameter

Om du vill ha flexibilitet när du distribuerar mallen använder du en parameter för att ange plats för resurser. Ange parameterns standardvärde `resourceGroup().location`till .

I följande exempel visas ett lagringskonto som distribueras till en plats som anges som en parameter:

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
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "Location for all resources."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('storage', uniquestring(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {}
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

* Den fullständiga listan över mallfunktioner finns i [Mallfunktionerna](template-functions.md)i Azure Resource Manager .
* Mer information om mallfiler finns [i Förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).

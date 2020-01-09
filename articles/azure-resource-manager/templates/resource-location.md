---
title: Mallens resurs plats
description: Beskriver hur du anger resurs plats i en Azure Resource Manager mall.
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: f8b4897bfa4e618059998650ae411ebb0fb59921
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75484316"
---
# <a name="set-resource-location-in-resource-manager-template"></a>Ange resurs plats i Resource Manager-mall

När du distribuerar en mall måste du ange en plats för varje resurs. Platsen behöver inte vara samma plats som resurs gruppens plats.

## <a name="get-available-locations"></a>Hämta tillgängliga platser

Olika resurs typer stöds på olika platser. Använd Azure PowerShell eller Azure CLI för att hämta de platser som stöds för en resurs typ.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
((Get-AzResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes `
  | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az provider show \
  --namespace Microsoft.Batch \
  --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" \
  --out table
```

---

## <a name="use-location-parameter"></a>Använd plats parameter

För att ge flexibilitet när du distribuerar din mall använder du en parameter för att ange plats för resurser. Ange standardvärdet för parametern till `resourceGroup().location`.

I följande exempel visas ett lagrings konto som har distribuerats till en plats som anges som en parameter:

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
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
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

* En fullständig lista över mall funktioner finns i [Azure Resource Manager Template Functions](template-functions.md).
* Mer information om mallfiler finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).

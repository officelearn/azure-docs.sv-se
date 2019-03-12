---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 03/11/2019
ms.author: tomfitz
ms.openlocfilehash: 104bd13a0cf97a8605670adde479c2a2eeb29c15
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57786336"
---
Om du vill tagga en resurs under distributionen lägger du till den `tags` elementet till den resurs som du distribuerar. Ange namn och värde för taggen.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Lägga till ett literalvärde till taggnamnet
I följande exempel visas ett lagringskonto med två taggar (`Dept` och `Environment`) som har angetts till literalvärden:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```

Ange en tagg till ett datetime-värde och den [utcNow funktionen](../articles/azure-resource-manager/resource-group-template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Lägga till ett objekt till taggelementet
Du kan definiera en objektparameter som lagrar flera taggar, och använda det objektet för taggelementet. Varje egenskap i objektet blir en separat tagg för resursen. I följande exempel finns en parameter med namnet `tagValues` som används för taggelementet.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": "[parameters('tagValues')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    }
  ]
}
```

### <a name="apply-a-json-string-to-the-tag-name"></a>Lägga till en JSON-sträng till taggnamnet

Du kan lagra flera värden i en enskild tagg genom att använda en JSON-sträng som representerar värdena. Hela JSON-strängen lagras som en tagg som inte får överskrida 256 tecken. I följande exempel finns en enskild tagg med namnet `CostCenter` som innehåller flera värden från en JSON-sträng:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
    {
      "apiVersion": "2016-01-01",
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": { }
    }
    ]
}
```
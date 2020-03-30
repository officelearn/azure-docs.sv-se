---
title: Definiera flera förekomster av ett utdatavärde
description: Använd kopieringsåtgärd i en Azure Resource Manager-mall för att iterera flera gånger när du returnerar ett värde från en distribution.
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 3889260d02f438274c80e99e99136515499443e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153394"
---
# <a name="output-iteration-in-arm-templates"></a>Utdataiteration i ARM-mallar

Den här artikeln visar hur du skapar mer än ett värde för en utdata i din AZURE Resource Manager -mall (ARM). Genom att **copy** lägga till kopieringselementet i avsnittet utdata i mallen kan du returnera ett antal objekt dynamiskt under distributionen.

Du kan också använda kopia med [resurser,](copy-resources.md) [egenskaper i en resurs](copy-properties.md)och [variabler](copy-variables.md).

## <a name="outputs-iteration"></a>Utdata iteration

Kopieringselementet har följande allmänna format:

```json
"copy": [
  {
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Egenskapen **count** anger hur många iterationer du vill använda för utdatavärdet.

**Indataegenskapen** anger de egenskaper som du vill upprepa. Du kan skapa en matris med element som skapats från värdet i **indataegenskapen.** Det kan vara en enda egenskap (som en sträng) eller ett objekt med flera egenskaper.

I följande exempel skapas ett variabelt antal lagringskonton och en slutpunkt returneras för varje lagringskonto:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageEndpoints": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]"
            }
        }
    }
}
```

Den föregående mallen returnerar en matris med följande värden:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

I nästa exempel returneras tre egenskaper från de nya lagringskontona.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageCount": {
            "type": "int",
            "defaultValue": 2
        }
    },
    "variables": {
        "baseName": "[concat('storage', uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(), variables('baseName'))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": "[parameters('storageCount')]"
            }
        }
    ],
    "outputs": {
        "storageInfo": {
            "type": "array",
            "copy": {
                "count": "[parameters('storageCount')]",
                "input": {
                    "id": "[reference(concat(copyIndex(), variables('baseName')), '2019-04-01', 'Full').resourceId]",
                    "blobEndpoint": "[reference(concat(copyIndex(), variables('baseName'))).primaryEndpoints.blob]",
                    "status": "[reference(concat(copyIndex(), variables('baseName'))).statusOfPrimary]"
                }
            }
        }
    }
}
```

I föregående exempel returneras en matris med följande värden:

```json
[
    {
        "id": "Microsoft.Storage/storageAccounts/0storagecfrbqnnmpeudi",
        "blobEndpoint": "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    },
    {
        "id": "Microsoft.Storage/storageAccounts/1storagecfrbqnnmpeudi",
        "blobEndpoint": "https://1storagecfrbqnnmpeudi.blob.core.windows.net/",
        "status": "available"
    }
]
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en självstudiekurs finns i [Självstudiekurs: skapa flera resursinstanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
* För annan användning av kopieringselementet, se:
  * [Resursiteration i ARM-mallar](copy-resources.md)
  * [Egenskapsiter iteration i ARM-mallar](copy-properties.md)
  * [Variabel iteration i ARM-mallar](copy-variables.md)
* Om du vill lära dig mer om avsnitten i en mall läser [du Skapa ARM-mallar](template-syntax.md).
* Mer information om hur du distribuerar mallen finns i [Distribuera ett program med ARM-mall](deploy-powershell.md).


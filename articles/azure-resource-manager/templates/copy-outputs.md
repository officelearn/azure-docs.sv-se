---
title: Definiera flera instanser av ett utdata-värde
description: Använd kopierings åtgärden i en Azure Resource Manager-mall för att iterera flera gånger när du returnerar ett värde från en distribution.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 0315af2f083285c4704b08fec608341b6f0b2231
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617839"
---
# <a name="output-iteration-in-arm-templates"></a>Utdata iteration i ARM-mallar

Den här artikeln visar hur du skapar fler än ett värde för utdata i din Azure Resource Manager-mall (ARM). Genom att lägga till elementet **Kopiera** i avsnittet utdata i mallen kan du dynamiskt returnera ett antal objekt under distributionen.

Du kan också använda kopiera med [resurser](copy-resources.md), [Egenskaper i en resurs](copy-properties.md)och [variabler](copy-variables.md).

## <a name="outputs-iteration"></a>Upprepningar av utdata

Kopierings elementet har följande allmänna format:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Egenskapen **Count** anger antalet iterationer som du vill använda för utmatning svärdet.

Egenskapen **indatamängd** anger de egenskaper som du vill upprepa. Du skapar en matris med element som skapats från värdet i egenskapen **indatamängd** . Det kan vara en enskild egenskap (till exempel en sträng) eller ett objekt med flera egenskaper.

I följande exempel skapas ett variabelt antal lagrings konton och en slut punkt returneras för varje lagrings konto:

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

Föregående mall returnerar en matris med följande värden:

```json
[
    "https://0storagecfrbqnnmpeudi.blob.core.windows.net/",
    "https://1storagecfrbqnnmpeudi.blob.core.windows.net/"
]
```

I nästa exempel returneras tre egenskaper från de nya lagrings kontona.

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

Föregående exempel returnerar en matris med följande värden:

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

* Information om hur du går igenom självstudierna finns i [Självstudier: skapa flera resurs instanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
* För andra användnings områden av elementet Copy, se:
  * [Resurs upprepning i ARM-mallar](copy-resources.md)
  * [Egenskaps upprepning i ARM-mallar](copy-properties.md)
  * [Variabel iteration i ARM-mallar](copy-variables.md)
* Om du vill lära dig mer om avsnitten i en mall, se [Redigera arm-mallar](template-syntax.md).
* Information om hur du distribuerar din mall finns i [distribuera ett program med arm-mall](deploy-powershell.md).


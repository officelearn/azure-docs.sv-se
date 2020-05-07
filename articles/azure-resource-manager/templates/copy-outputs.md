---
title: Definiera flera instanser av ett utdata-värde
description: Använd kopierings åtgärden i en Azure Resource Manager-mall för att iterera flera gånger när du returnerar ett värde från en distribution.
ms.topic: conceptual
ms.date: 04/17/2020
ms.openlocfilehash: 50c4b4b8f301ad88d3dfde98ace1aed4431693db
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583423"
---
# <a name="output-iteration-in-arm-templates"></a>Utdata iteration i ARM-mallar

Den här artikeln visar hur du skapar fler än ett värde för utdata i din Azure Resource Manager-mall (ARM). Genom att lägga till elementet **Kopiera** i avsnittet utdata i mallen kan du dynamiskt returnera ett antal objekt under distributionen.

Du kan också använda kopiera med [resurser](copy-resources.md), [Egenskaper i en resurs](copy-properties.md)och [variabler](copy-variables.md).

## <a name="syntax"></a>Syntax

Kopierings elementet har följande allmänna format:

```json
"copy": {
  "count": <number-of-iterations>,
  "input": <values-for-the-output>
}
```

Egenskapen **Count** anger antalet iterationer som du vill använda för utmatning svärdet.

Egenskapen **indatamängd** anger de egenskaper som du vill upprepa. Du skapar en matris med element som skapats från värdet i egenskapen **indatamängd** . Det kan vara en enskild egenskap (till exempel en sträng) eller ett objekt med flera egenskaper.

## <a name="copy-limits"></a>Kopierings gränser

Antalet får inte överskrida 800.

Antalet får inte vara ett negativt tal. Det kan vara noll om du distribuerar mallen med en senare version av Azure CLI, PowerShell eller REST API. Mer specifikt måste du använda:

* Azure PowerShell **2,6** eller senare
* Azure CLI- **2.0.74** eller senare
* REST API version **2019-05-10** eller senare
* [Länkade distributioner](linked-templates.md) måste använda API version **2019-05-10** eller senare för distributions resurs typen

Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för Count.

## <a name="outputs-iteration"></a>Upprepningar av utdata

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


---
title: Definiera flera förekomster av en variabel
description: Använd kopieringsåtgärd i en Azure Resource Manager-mall för att iterera flera gånger när du skapar en variabel.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: ed0c2d87c48a18b0a065f6c76e1e69142a9df048
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153309"
---
# <a name="variable-iteration-in-arm-templates"></a>Variabel iteration i ARM-mallar

Den här artikeln visar hur du skapar mer än ett värde för en variabel i din AZURE Resource Manager -mall (ARM). Genom att **copy** lägga till kopieringselementet i variabelavsnittet i mallen kan du dynamiskt ange antalet objekt för en variabel under distributionen. Du undviker också att behöva upprepa mallsyntaxen.

Du kan också använda kopia med [resurser,](copy-resources.md) [egenskaper i en resurs](copy-properties.md)och [utdata](copy-outputs.md).

## <a name="variable-iteration"></a>Variabel iteration

Kopieringselementet har följande allmänna format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Namnegenskapen är ett värde som identifierar loopen. **name** Egenskapen **count** anger hur många iterationer du vill använda för variabeln.

**Indataegenskapen** anger de egenskaper som du vill upprepa. Du kan skapa en matris med element som skapats från värdet i **indataegenskapen.** Det kan vara en enda egenskap (som en sträng) eller ett objekt med flera egenskaper.

I följande exempel visas hur du skapar en matris med strängvärden:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
     },
    "variables": {
        "copy": [
            {
                "name": "stringArray",
                "count": "[parameters('itemCount')]",
                "input": "[concat('item', copyIndex('stringArray', 1))]"
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('stringArray')]"
        }
    }
}
```

Den föregående mallen returnerar en matris med följande värden:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

Nästa exempel visar hur du skapar en matris med objekt med tre egenskaper - namn, diskSizeGB och diskIndex.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "copy": [
            {
                "name": "objectArray",
                "count": "[parameters('itemCount')]",
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('objectArray', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('objectArray')]"
                }
            }
        ]
    },
    "resources": [],
    "outputs": {
        "arrayResult": {
            "type": "array",
            "value": "[variables('objectArray')]"
        }
    }
}
```

I föregående exempel returneras en matris med följande värden:

```json
[
    {
        "name": "myDataDisk1",
        "diskSizeGB": "1",
        "diskIndex": 0
    },
    {
        "name": "myDataDisk2",
        "diskSizeGB": "1",
        "diskIndex": 1
    },
    {
        "name": "myDataDisk3",
        "diskSizeGB": "1",
        "diskIndex": 2
    },
    {
        "name": "myDataDisk4",
        "diskSizeGB": "1",
        "diskIndex": 3
    },
    {
        "name": "myDataDisk5",
        "diskSizeGB": "1",
        "diskIndex": 4
    }
]
```

> [!NOTE]
> Variabel iteration stöder ett förskjutningsargument. Förskjutningen måste komma efter namnet på iterationen, till exempel copyIndex('diskNames', 1). Om du inte anger ett förskjutningsvärde är det standard 0 för första instansen.
>

Du kan också använda kopieringselementet i en variabel. I följande exempel skapas ett objekt som har en matris som ett av dess värden.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "itemCount": {
            "type": "int",
            "defaultValue": 5
        }
    },
    "variables": {
        "topLevelObject": {
            "sampleProperty": "sampleValue",
            "copy": [
                {
                    "name": "disks",
                    "count": "[parameters('itemCount')]",
                    "input": {
                        "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                        "diskSizeGB": "1",
                        "diskIndex": "[copyIndex('disks')]"
                    }
                }
            ]
        }
    },
    "resources": [],
    "outputs": {
        "objectResult": {
            "type": "object",
            "value": "[variables('topLevelObject')]"
        }
    }
}
```

Föregående exempel returnerar ett objekt med följande värden:

```json
{
    "sampleProperty": "sampleValue",
    "disks": [
        {
            "name": "myDataDisk1",
            "diskSizeGB": "1",
            "diskIndex": 0
        },
        {
            "name": "myDataDisk2",
            "diskSizeGB": "1",
            "diskIndex": 1
        },
        {
            "name": "myDataDisk3",
            "diskSizeGB": "1",
            "diskIndex": 2
        },
        {
            "name": "myDataDisk4",
            "diskSizeGB": "1",
            "diskIndex": 3
        },
        {
            "name": "myDataDisk5",
            "diskSizeGB": "1",
            "diskIndex": 4
        }
    ]
}
```

I nästa exempel visas olika sätt att använda kopian med variabler.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "disk-array-on-object": {
      "copy": [
        {
          "name": "disks",
          "count": 5,
          "input": {
            "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
            "diskSizeGB": "1",
            "diskIndex": "[copyIndex('disks')]"
          }
        },
        {
          "name": "diskNames",
          "count": 5,
          "input": "[concat('myDataDisk', copyIndex('diskNames', 1))]"
        }
      ]
    },
    "copy": [
      {
        "name": "top-level-object-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('top-level-object-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('top-level-object-array')]"
        }
      },
      {
        "name": "top-level-string-array",
        "count": 5,
        "input": "[concat('myDataDisk', copyIndex('top-level-string-array', 1))]"
      },
      {
        "name": "top-level-integer-array",
        "count": 5,
        "input": "[copyIndex('top-level-integer-array')]"
      }
    ]
  },
  "resources": [],
  "outputs": {
    "exampleObject": {
      "value": "[variables('disk-array-on-object')]",
      "type": "object"
    },
    "exampleArrayOnObject": {
      "value": "[variables('disk-array-on-object').disks]",
      "type" : "array"
    },
    "exampleObjectArray": {
      "value": "[variables('top-level-object-array')]",
      "type" : "array"
    },
    "exampleStringArray": {
      "value": "[variables('top-level-string-array')]",
      "type" : "array"
    },
    "exampleIntegerArray": {
      "value": "[variables('top-level-integer-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="copy-limits"></a>Kopiera gränser

Antalet får inte överstiga 800.

Antalet kan inte vara ett negativt tal. Om du distribuerar en mall med Azure PowerShell 2.6 eller senare, Azure CLI 2.0.74 eller senare, eller REST API version **2019-05-10** eller senare, kan du ange antal till noll. Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för antal.

## <a name="example-templates"></a>Exempel på mallar

Följande exempel visar vanliga scenarier för att skapa mer än ett värde för en variabel.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Visar olika sätt att iterera på variabler. |
|[Flera säkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuerar flera säkerhetsregler till en nätverkssäkerhetsgrupp. Säkerhetsregler na konstrueras från en parameter. För parametern finns [i flera NSG-parameterfil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en självstudiekurs finns i [Självstudiekurs: skapa flera resursinstanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
* För annan användning av kopieringselementet, se:
  * [Resursiteration i ARM-mallar](copy-resources.md)
  * [Egenskapsiter iteration i ARM-mallar](copy-properties.md)
  * [Utdataiteration i ARM-mallar](copy-outputs.md)
* Om du vill lära dig mer om avsnitten i en mall läser [du Skapa ARM-mallar](template-syntax.md).
* Mer information om hur du distribuerar mallen finns i [Distribuera ett program med ARM-mall](deploy-powershell.md).


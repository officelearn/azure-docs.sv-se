---
title: Definiera flera instanser av en variabel
description: Använd kopierings åtgärden i en Azure Resource Manager mall för att iterera flera gånger när du skapar en variabel.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 9e252a0b9721ffec99535c5d30e609e12e9e67eb
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210819"
---
# <a name="variable-iteration-in-azure-resource-manager-templates"></a>Variabel iteration i Azure Resource Manager mallar

Den här artikeln visar hur du skapar fler än ett värde för en variabel i din Azure Resource Manager-mall. Genom att lägga till elementet **Kopiera** i avsnittet variabler i mallen kan du dynamiskt ange antalet objekt för en variabel under distributionen. Du behöver inte heller upprepa syntaxen för mallar.

Du kan också använda kopiera med [resurser](copy-resources.md) och [Egenskaper i en resurs](copy-properties.md).

## <a name="variable-iteration"></a>Variabel iteration

Kopierings elementet har följande allmänna format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-variable>
  }
]
```

Egenskapen **Name** är ett värde som identifierar slingan. Egenskapen **Count** anger antalet iterationer som du vill använda för variabeln.

Egenskapen **indatamängd** anger de egenskaper som du vill upprepa. Du skapar en matris med element som skapats från värdet i egenskapen **indatamängd** . Det kan vara en enskild egenskap (till exempel en sträng) eller ett objekt med flera egenskaper.

I följande exempel visas hur du skapar en matris med sträng värden:

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

Föregående mall returnerar en matris med följande värden:

```json
[
    "item1",
    "item2",
    "item3",
    "item4",
    "item5"
]
```

I nästa exempel visas hur du skapar en matris med objekt med tre egenskaper: Name, diskSizeGB och diskIndex.

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

Föregående exempel returnerar en matris med följande värden:

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
> Variabeln iteration stöder ett offset-argument. Förskjutningen måste komma efter namnet på iterationen, till exempel copyIndex (' diskNames ', 1). Om du inte anger ett förskjutnings värde används 0 som standard för den första instansen.
>

Du kan också använda kopierings elementet i en variabel. I följande exempel skapas ett objekt som har en matris som en av dess värden.

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

Föregående exempel returnerar ett-objekt med följande värden:

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

I nästa exempel visas olika sätt som du kan använda kopiera med variabler.

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

## <a name="copy-limits"></a>Kopierings gränser

Antalet får inte överskrida 800.

Antalet får inte vara ett negativt tal. Om du distribuerar en mall med Azure PowerShell 2,6 eller senare, Azure CLI 2.0.74 eller senare, eller REST API version **2019-05-10** eller senare, kan du ange antal till noll. Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för Count.

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas vanliga scenarier för att skapa mer än ett värde för en variabel.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Visar de olika sätten att iterera på variabler. |
|[Flera säkerhets regler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuerar flera säkerhets regler till en nätverks säkerhets grupp. Den skapar säkerhets reglerna från en parameter. För-parametern, se [flera NSG-parameter fil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: skapa flera resurs instanser med Resource Manager-mallar](template-tutorial-create-multiple-instances.md).
* För annan användning av kopierings elementet, se [resurs upprepning i Azure Resource Manager mallar](copy-resources.md) och [egenskaper iteration i Azure Resource Manager mallar](copy-properties.md).
* Om du vill lära dig mer om avsnitten i en mall, se [redigera Azure Resource Manager mallar](template-syntax.md).
* Information om hur du distribuerar din mall finns i [distribuera ett program med Azure Resource Manager-mall](deploy-powershell.md).


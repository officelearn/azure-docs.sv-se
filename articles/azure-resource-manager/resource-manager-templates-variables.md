---
title: Azure Resource Manager-mallvariablerna | Microsoft Docs
description: Beskriver hur du definierar variabler i en Azure Resource Manager-mallar med hjälp av deklarativa JSON-syntax.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: tomfitz
ms.openlocfilehash: f6c629182fdcce83c566869860480d9c70488797
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53712754"
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Variables-avsnittet av Azure Resource Manager-mallar
I avsnittet variables kan skapa du värden som kan användas i hela din mall. Du behöver inte definiera variabler, men de förenkla ofta din mall genom att minska komplexa uttryck.

## <a name="define-and-use-a-variable"></a>Definiera och använda en variabel

I följande exempel visas en variabeldefinitionen. Ett strängvärde för namnet på ett lagringskonto skapas. Flera Mallfunktioner används för att få ett parametervärde och sammanfoga till en unik sträng.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Du använder variabeln när du definierar resursen.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Tillgängliga definitioner

I föregående exempel visade ett sätt att definiera en variabel. Du kan använda någon av följande definitioner:

```json
"variables": {
    "<variable-name>": "<variable-value>",
    "<variable-name>": { 
        <variable-complex-type-value> 
    },
    "<variable-object-name>": {
        "copy": [
            {
                "name": "<name-of-array-property>",
                "count": <number-of-iterations>,
                "input": {
                    <properties-to-repeat>
                }
            }
        ]
    },
    "copy": [
        {
            "name": "<variable-array-name>",
            "count": <number-of-iterations>,
            "input": {
                <properties-to-repeat>
            }
        }
    ]
}
```

## <a name="configuration-variables"></a>Av konfigurationsvariabler

Du kan använda komplexa JSON-typer för att definiera relaterade värden för en miljö. 

```json
"variables": {
    "environmentSettings": {
        "test": {
            "instanceSize": "Small",
            "instanceCount": 1
        },
        "prod": {
            "instanceSize": "Large",
            "instanceCount": 4
        }
    }
},
```

I parametrar skapar du ett värde som anger vilken konfiguration som värden som ska användas.

```json
"parameters": {
    "environmentName": {
        "type": "string",
        "allowedValues": [
          "test",
          "prod"
        ]
    }
},
```

Du kan hämta de aktuella inställningarna med:

```json
"[variables('environmentSettings')[parameters('environmentName')].instanceSize]"
```

## <a name="use-copy-element-in-variable-definition"></a>Använd kopieringselement i variabeldefinitionen

Du kan använda den **kopia** syntaxen för att skapa en variabel med en matris med flera element. Du kan ange ett antal för antalet element. Varje element innehåller egenskaperna i den **inkommande** objekt. Du kan använda kopia antingen i en variabel eller för att skapa variabeln. När du definiera en variabel och Använd **kopia** inom den variabeln som du skapar ett objekt som har en matrisegenskap. När du använder **kopia** på den översta nivån och definiera en eller flera variabler i den skapar du en eller flera matriser. Båda metoderna visas i följande exempel:

```json
"variables": {
    "disk-array-on-object": {
        "copy": [
            {
                "name": "disks",
                "count": 3,
                "input": {
                    "name": "[concat('myDataDisk', copyIndex('disks', 1))]",
                    "diskSizeGB": "1",
                    "diskIndex": "[copyIndex('disks')]"
                }
            }
        ]
    },
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 3,
            "input": {
                "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        }
    ]
},
```

Variabeln **disk-matris på objektet** innehåller följande objekt med en matris med namnet **diskar**:

```json
{
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
    }
  ]
}
```

Variabeln **diskar – top-nivå-matris** innehåller följande matris:

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
  }
]
```

Du kan också ange fler än ett objekt när du använder kopiera för att skapa variabler. I följande exempel definierar två matriser som variabler. En heter **diskar – top-nivå-matris** och har fem element. Den andra heter **en annan matris** och har tre element.

```json
"variables": {
    "copy": [
        {
            "name": "disks-top-level-array",
            "count": 5,
            "input": {
                "name": "[concat('oneDataDisk', copyIndex('disks-top-level-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('disks-top-level-array')]"
            }
        },
        {
            "name": "a-different-array",
            "count": 3,
            "input": {
                "name": "[concat('twoDataDisk', copyIndex('a-different-array', 1))]",
                "diskSizeGB": "1",
                "diskIndex": "[copyIndex('a-different-array')]"
            }
        }
    ]
},
```

Den här metoden fungerar bra när du behöver ta parametervärden och se till att de är i rätt format för ett mall-värde. I följande exempel formaterar parametervärden för användning i definierar säkerhetsregler:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "securityRules": {
      "type": "array"
    }
  },
  "variables": {
    "copy": [
      {
        "name": "securityRules",
        "count": "[length(parameters('securityRules'))]",
        "input": {
          "name": "[parameters('securityRules')[copyIndex('securityRules')].name]",
          "properties": {
            "description": "[parameters('securityRules')[copyIndex('securityRules')].description]",
            "priority": "[parameters('securityRules')[copyIndex('securityRules')].priority]",
            "protocol": "[parameters('securityRules')[copyIndex('securityRules')].protocol]",
            "sourcePortRange": "[parameters('securityRules')[copyIndex('securityRules')].sourcePortRange]",
            "destinationPortRange": "[parameters('securityRules')[copyIndex('securityRules')].destinationPortRange]",
            "sourceAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].sourceAddressPrefix]",
            "destinationAddressPrefix": "[parameters('securityRules')[copyIndex('securityRules')].destinationAddressPrefix]",
            "access": "[parameters('securityRules')[copyIndex('securityRules')].access]",
            "direction": "[parameters('securityRules')[copyIndex('securityRules')].direction]"
          }
        }
      }
    ]
  },
  "resources": [
    {
      "apiVersion": "2015-06-15",
      "type": "Microsoft.Network/networkSecurityGroups",
      "name": "NSG1",
      "location": "[resourceGroup().location]",
      "properties": {
        "securityRules": "[variables('securityRules')]"
      }
    }
  ],
  "outputs": {}
}
```

## <a name="example-templates"></a>Exempel på mallar

Dessa exempel på mallar visar några scenarier för att använda variabler. Distribuera dem för att testa hur variabler ska hanteras i olika scenarier. 

|Mall  |Beskrivning  |
|---------|---------|
| [variabeln definitioner](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Visar de olika typerna av variabler. Mallen distribuerar inte några resurser. Den skapar variabelvärdena och returnerar dessa värden. |
| [konfiguration av variabel](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Visar hur du använder en variabel som definierar konfigurationsvärden. Mallen distribuerar inte några resurser. Den skapar variabelvärdena och returnerar dessa värden. |
| [Nätverkssäkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameterfilen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Skapar en matris i rätt format för att tilldela säkerhetsregler till en nätverkssäkerhetsgrupp. |


## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner du kan använda från inom en mall finns i [Azure Resource Manager-Mallfunktioner](resource-group-template-functions.md).
* Rekommendationer om hur du skapar mallar finns i [Metodtips för Azure Resource Manager-mall](template-best-practices.md).
* Du kan behöva använda resurser som finns i en annan resursgrupp. Det här scenariot är vanligt när du arbetar med lagringskonton eller virtuella nätverk som delas av mer än en resursgrupp. Mer information finns i den [resourceId funktionen](resource-group-template-functions-resource.md#resourceid).
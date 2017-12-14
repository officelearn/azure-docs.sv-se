---
title: Azure Resource Manager mallvariabler | Microsoft Docs
description: Beskriver hur du definierar variabler i en Azure Resource Manager-mallar med deklarativ JSON-syntax.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2017
ms.author: tomfitz
ms.openlocfilehash: 8d9f227ad1f450cf6cdfca1dafb1b51bc6f6c9f9
ms.sourcegitcommit: d247d29b70bdb3044bff6a78443f275c4a943b11
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="variables-section-of-azure-resource-manager-templates"></a>Variabler avsnitt i Azure Resource Manager-mallar
I avsnittet variables kan skapa du värden som kan användas i hela din mall. Du behöver inte definiera variabler, men de förenkla ofta din mall genom att minska komplexa uttryck.

## <a name="define-and-use-a-variable"></a>Definiera och använda en variabel

I följande exempel visas en variabel definition. Ett strängvärde för namnet på ett lagringskonto skapas. Flera Mallfunktioner används för att få ett parametervärde och sammanfoga till en unik sträng.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Du kan använda variabeln när du definierar resursen.

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    ...
```

## <a name="available-definitions"></a>Tillgängliga definitionerna

Föregående exempel visade ett sätt att definiera en variabel. Du kan använda någon av följande definitioner:

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

I parametrar skapar du ett värde som anger vilken konfiguration som värden ska användas.

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

## <a name="use-copy-element-in-variable-definition"></a>Använd kopiera elementet i variabeldefinitionen

Du kan använda den **kopiera** syntax för att skapa en variabel med en matris med flera element. Du kan ange ett antal för antalet element. Varje element innehåller egenskaper i den **inkommande** objekt. Du kan använda kopiera antingen i en variabel eller för att skapa variabeln. När du ska definiera en variabel och använda **kopiera** i variabeln, skapar du ett objekt som har en matrisegenskap. När du använder **kopiera** på den översta nivån och definiera en eller flera variabler i det du skapa en eller flera matriser. Båda metoderna visas i följande exempel:

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

Variabeln **disk array på objektet** innehåller följande objekt med en matris med namnet **diskar**:

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

Variabeln **diskar top-nivå-matriser** innehåller följande matris:

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

Du kan också ange fler än ett objekt när kopiera för att skapa variabler. I följande exempel definierar två matriser som variabler. En heter **diskar top-nivå-matriser** och har fem element. Den andra heter **en annan matris** och har tre element.

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

Den här metoden fungerar bra om du behöver ta parametervärden och kontrollera att de är i rätt format för ett mall-värde. I följande exempel format parametervärden för definition av säkerhetsregler:

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

## <a name="recommendations"></a>Rekommendationer
Följande information kan vara användbart när du arbetar med variabler:

* Använda variabler för värden som du behöver använda mer än en gång i en mall. Om ett värde används bara en gång, ett hårdkodat värde gör mallen lättare att läsa.
* Du kan inte använda den [referens](resource-group-template-functions-resource.md#reference) fungera i den **variabler** avsnitt i mallen. Den **referens** funktionen hämtar sitt värde från resursens runtime-tillståndet. Variabler är dock lösts vid första parsningen av mallen. Konstruktion värden som måste den **referens** fungera direkt i den **resurser** eller **matar ut** avsnitt i mallen.
* Inkludera variabler för resursnamn som måste vara unika.

## <a name="example-templates"></a>Exempel mallar

Mallarna exempel visar några scenarier för att använda variabler. Distribuera dem för att testa hur variabler ska hanteras i olika scenarier. 

|Mall  |Beskrivning  |
|---------|---------|
| [variabeln definitioner](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variables.json) | Visar de olika typerna av variabler. Mallen distribuerar inte några resurser. Den skapar variabelvärden och returnerar dessa värden. |
| [av konfigurationsvariabeln](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/variablesconfigurations.json) | Visar hur du använder för en variabel som definierar konfigurationsvärden. Mallen distribuerar inte några resurser. Den skapar variabelvärden och returnerar dessa värden. |
| [nätverk säkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) och [parameterfilen](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json) | Skapar en matris i rätt format för att tilldela en nätverkssäkerhetsgrupp säkerhetsregler. |


## <a name="next-steps"></a>Nästa steg
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).
* Mer information om de funktioner som du kan använda från i en mall finns [Azure Resource Manager mallen Functions](resource-group-template-functions.md).
* Om du vill kombinera flera mallar under distributionen finns [använda länkade mallar med Azure Resource Manager](resource-group-linked-templates.md).
* Du kan behöva använda resurser som finns i en annan resursgrupp. Det här scenariot är vanligt när du arbetar med lagringskonton eller virtuella nätverk som delas mellan flera resursgrupper. Mer information finns i [resourceId funktionen](resource-group-template-functions-resource.md#resourceid).
---
title: Definiera flera instanser av en egenskap
description: Använd kopierings åtgärden i en Azure Resource Manager-mall för att iterera flera gånger när du skapar en egenskap för en resurs.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: b1e31f981f361b4cfbe7e7930f2c70bfce8b8656
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77210871"
---
# <a name="property-iteration-in-azure-resource-manager-templates"></a>Egenskaps iteration i Azure Resource Manager mallar

Den här artikeln visar hur du skapar fler än en instans av en egenskap i din Azure Resource Manager-mall. Genom att lägga till elementet **Kopiera** i avsnittet Egenskaper i en resurs i mallen kan du dynamiskt ange antalet objekt för en egenskap under distributionen. Du behöver inte heller upprepa syntaxen för mallar.

Du kan också använda kopiera med [resurser](copy-resources.md) och [variabler](copy-variables.md).

## <a name="property-iteration"></a>Egenskaps upprepning

Kopierings elementet har följande allmänna format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

Som **namn**anger du namnet på den resurs egenskap som du vill skapa. Egenskapen **Count** anger antalet iterationer som du vill använda för egenskapen.

Egenskapen **indatamängd** anger de egenskaper som du vill upprepa. Du skapar en matris med element som skapats från värdet i egenskapen **indatamängd** .

I följande exempel visas hur du tillämpar `copy` på data disks-egenskapen på en virtuell dator:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "numberOfDataDisks": {
      "type": "int",
      "minValue": 0,
      "maxValue": 16,
      "defaultValue": 16,
      "metadata": {
        "description": "The number of dataDisks to create."
      }
    },
    ...
  },
  "resources": [
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2017-03-30",
      ...
      "properties": {
        "storageProfile": {
          ...
          "copy": [
            {
              "name": "dataDisks",
              "count": "[parameters('numberOfDataDisks')]",
              "input": {
                "diskSizeGB": 1023,
                "lun": "[copyIndex('dataDisks')]",
                "createOption": "Empty"
              }
            }
          ]
        }
      }
    }
  ]
}
```

Observera att när du använder `copyIndex` inuti en egenskap iteration måste du ange namnet på iterationen.

> [!NOTE]
> Egenskapen iteration stöder också ett offset-argument. Förskjutningen måste komma efter namnet på iterationen, till exempel copyIndex (' data disks ', 1).
>

Resource Manager expanderar `copy` matris under distributionen. Namnet på matrisen blir namnet på egenskapen. De angivna värdena blir objekt egenskaperna. Den distribuerade mallen blir:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "dataDisks": [
        {
          "lun": 0,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 1,
          "createOption": "Empty",
          "diskSizeGB": 1023
        },
        {
          "lun": 2,
          "createOption": "Empty",
          "diskSizeGB": 1023
        }
      ],
      ...
```

Kopierings elementet är en matris så att du kan ange mer än en egenskap för resursen.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "examleLB",
  "properties": {
    "copy": [
      {
        "name": "loadBalancingRules",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      },
      {
        "name": "probes",
        "count": "[length(parameters('loadBalancingRules'))]",
        "input": {
          ...
        }
      }
    ]
  }
}
```

Du kan använda en iteration av resurs och egenskap tillsammans. Referera till egenskapen iteration efter namn.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "apiVersion": "2018-04-01",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "copy":{
    "count": 2,
    "name": "vnetloop"
  },
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[parameters('addressPrefix')]"
      ]
    },
    "copy": [
      {
        "name": "subnets",
        "count": 2,
        "input": {
          "name": "[concat('subnet-', copyIndex('subnets'))]",
          "properties": {
            "addressPrefix": "[variables('subnetAddressPrefix')[copyIndex('subnets')]]"
          }
        }
      }
    ]
  }
}
```

## <a name="copy-limits"></a>Kopierings gränser

Antalet får inte överskrida 800.

Antalet får inte vara ett negativt tal. Om du distribuerar en mall med Azure PowerShell 2,6 eller senare, Azure CLI 2.0.74 eller senare, eller REST API version **2019-05-10** eller senare, kan du ange antal till noll. Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för Count.

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas ett vanligt scenario för att skapa mer än ett värde för en egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[VM-distribution med ett variabel antal data diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera data diskar till en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: skapa flera resurs instanser med Resource Manager-mallar](template-tutorial-create-multiple-instances.md).
* För annan användning av kopierings elementet, se [resurs upprepning i Azure Resource Manager mallar](copy-resources.md) och [variabel iteration i Azure Resource Manager mallar](copy-variables.md).
* Om du vill lära dig mer om avsnitten i en mall, se [redigera Azure Resource Manager mallar](template-syntax.md).
* Information om hur du distribuerar din mall finns i [distribuera ett program med Azure Resource Manager-mall](deploy-powershell.md).


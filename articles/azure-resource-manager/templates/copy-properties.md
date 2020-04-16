---
title: Definiera flera instanser av en egenskap
description: Använd kopieringsåtgärd i en Azure Resource Manager-mall för att iterera flera gånger när du skapar en egenskap på en resurs.
ms.topic: conceptual
ms.date: 04/14/2020
ms.openlocfilehash: 831ae1af202a1cdf52bdd2bdf0d9a042a97ba52f
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391344"
---
# <a name="property-iteration-in-arm-templates"></a>Egenskapsiter iteration i ARM-mallar

Den här artikeln visar hur du skapar mer än en instans av en egenskap i din AZURE Resource Manager -mall (ARM). Genom att **copy** lägga till kopieringselementet i egenskapsavsnittet för en resurs i mallen kan du ange antalet objekt för en egenskap dynamiskt under distributionen. Du undviker också att behöva upprepa mallsyntaxen.

Du kan också använda kopia med [resurser,](copy-resources.md) [variabler](copy-variables.md)och [utdata](copy-outputs.md).

## <a name="property-iteration"></a>Egenskap iteration

Kopieringselementet har följande allmänna format:

```json
"copy": [
  {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "input": <values-for-the-property>
  }
]
```

För **namn**anger du namnet på den resursegenskap som du vill skapa.

Egenskapen **Count** anger hur många iterationer du vill använda för egenskapen.

**Indataegenskapen** anger de egenskaper som du vill upprepa. Du kan skapa en matris med element som skapats från värdet i **indataegenskapen.**

I följande exempel visas `copy` hur du använder egenskapen dataDisks på en virtuell dator:

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

Observera att `copyIndex` när du använder inuti en egenskap iteration, måste du ange namnet på iterationen. Egenskapsiteration stöder också ett förskjutningsargument. Förskjutningen måste komma efter namnet på iterationen, till exempel copyIndex('dataDisks', 1).

Resource Manager utökar matrisen `copy` under distributionen. Namnet på matrisen blir namnet på egenskapen. Indatavärdena blir objektegenskaperna. Den distribuerade mallen blir:

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

Kopieringen är användbar när du arbetar med matriser eftersom du kan iterera genom varje element i matrisen. Använd `length` funktionen på matrisen för att ange `copyIndex` antalet för iterationer och för att hämta det aktuella indexet i matrisen.

I följande exempelmall skapas en redundansgrupp för databaser som skickas in som en matris.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "primaryServerName": {
            "type": "string"
        },
        "secondaryServerName": {
            "type": "string"
        },
        "databaseNames": {
            "type": "array",
            "defaultValue": [
                "mydb1",
                "mydb2",
                "mydb3"
            ]
        }
    },
    "variables": {
        "failoverName": "[concat(parameters('primaryServerName'),'/', parameters('primaryServerName'),'failovergroups')]"
    },
    "resources": [
        {
            "type": "Microsoft.Sql/servers/failoverGroups",
            "apiVersion": "2015-05-01-preview",
            "name": "[variables('failoverName')]",
            "properties": {
                "readWriteEndpoint": {
                    "failoverPolicy": "Automatic",
                    "failoverWithDataLossGracePeriodMinutes": 60
                },
                "readOnlyEndpoint": {
                    "failoverPolicy": "Disabled"
                },
                "partnerServers": [
                    {
                        "id": "[resourceId('Microsoft.Sql/servers', parameters('secondaryServerName'))]"
                    }
                ],
                "copy": [
                    {
                        "name": "databases",
                        "count": "[length(parameters('databaseNames'))]",
                        "input": "[resourceId('Microsoft.Sql/servers/databases', parameters('primaryServerName'), parameters('databaseNames')[copyIndex('databases')])]"
                    }
                ]
            }
        }
    ],
    "outputs": {
    }
}
```

Kopieringselementet är en matris så att du kan ange mer än en egenskap för resursen.

```json
{
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
  "name": "exampleLB",
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

Du kan använda resurs- och egenskapsiteration tillsammans. Referera till egenskapsiterationen efter namn.

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

## <a name="copy-limits"></a>Kopiera gränser

Antalet får inte överstiga 800.

Antalet kan inte vara ett negativt tal. Om du distribuerar en mall med Azure PowerShell 2.6 eller senare, Azure CLI 2.0.74 eller senare, eller REST API version **2019-05-10** eller senare, kan du ange antal till noll. Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för antal.

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas ett vanligt scenario för att skapa mer än ett värde för en egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[VM-distribution med ett varierande antal datadiskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera datadiskar med en virtuell dator. |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en självstudiekurs finns i [Självstudiekurs: skapa flera resursinstanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
* För annan användning av kopieringselementet, se:
  * [Resursiteration i ARM-mallar](copy-resources.md)
  * [Variabel iteration i ARM-mallar](copy-variables.md)
  * [Utdataiteration i ARM-mallar](copy-outputs.md)
* Om du vill lära dig mer om avsnitten i en mall läser [du Skapa ARM-mallar](template-syntax.md).
* Mer information om hur du distribuerar mallen finns i [Distribuera ett program med ARM-mall](deploy-powershell.md).


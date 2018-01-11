---
title: Distribuera flera instanser av Azure-resurser | Microsoft Docs
description: "Använd kopieringen och matriser i en Azure Resource Manager-mall för att iterera flera gånger när du distribuerar resurser."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: 94d95810-a87b-460f-8e82-c69d462ac3ca
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: e19833cb58f37f5f8b83d5558d74255583137684
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2017
---
# <a name="deploy-multiple-instances-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Distribuera flera instanser av en resurs eller en egenskap i Azure Resource Manager-mallar
Den här artikeln visar hur du distribuerar villkorligt en resurs och hur du iterera i Azure Resource Manager-mall för att skapa flera instanser av en resurs.

## <a name="conditionally-deploy-resource"></a>Villkorligt distribuera resurs

När du måste välja under distributionen för att skapa en instans eller inga instanser av en resurs, använda den `condition` element. Värdet för det här elementet matchar true eller false. När värdet är true, distribueras resursen. Om värdet är FALSKT har resursen inte distribuerats. Till exempel vill ange om ett nytt lagringskonto distribueras eller ett befintligt lagringskonto används, använder du:

```json
{
    "condition": "[equals(parameters('newOrExisting'),'new')]",
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[variables('storageAccountName')]",
    "apiVersion": "2017-06-01",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "[variables('storageAccountType')]"
    },
    "kind": "Storage",
    "properties": {}
}
```

## <a name="resource-iteration"></a>Resursen upprepning
När du måste välja under distributionen för att skapa en eller flera instanser av en resurs, lägga till en `copy` elementet för resurstypen. I elementet kopia anger du antalet upprepningar och ett namn för den här loop. Värdet för antal måste vara ett positivt heltal och får inte överskrida 800. 

Resursen ska skapa flera gånger tar följande format:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        }
    ],
    "outputs": {}
}
```

Observera att varje resurs ingår i `copyIndex()` som returnerar den aktuella upprepningen i en slinga. `copyIndex()`är nollbaserade. Det, i följande exempel:

```json
"name": "[concat('storage', copyIndex())]",
```

Skapar dessa namn:

* storage0
* storage1
* storage2.

Om du vill förskjuta indexvärdet skickar du ett värde i funktionen copyIndex(). Antal iterationer för att utföra fortfarande har angetts i elementet kopia, men värdet för copyIndex förskjutas av det angivna värdet. Det, i följande exempel:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Skapar dessa namn:

* storage1
* storage2
* storage3

Kopieringen är användbart när du arbetar med matriser eftersom du kan söka igenom varje element i matrisen. Använd den `length` funktion på matrisen för att ange antalet för iterationer, och `copyIndex` att hämta det aktuella indexet i matrisen. Det, i följande exempel:

```json
"parameters": { 
  "org": { 
     "type": "array", 
     "defaultValue": [ 
         "contoso", 
         "fabrikam", 
         "coho" 
      ] 
  }
}, 
"resources": [ 
  { 
      "name": "[concat('storage', parameters('org')[copyIndex()])]", 
      "copy": { 
         "name": "storagecopy", 
         "count": "[length(parameters('org'))]" 
      }, 
      ...
  } 
]
```

Skapar dessa namn:

* storagecontoso
* storagefabrikam
* storagecoho

Som standard skapar Resource Manager resurserna parallellt. Den ordning som de har skapats kan därför inte garanteras. Du kanske vill ange att resurserna som distribueras i följd. Till exempel när du uppdaterar en produktionsmiljö måste du kanske vill sprida uppdateringar så bara ett visst antal uppdateras samtidigt.

För att distribuera seriellt flera instanser av en resurs, ange `mode` till **seriella** och `batchSize` med antalet instanser som ska distribueras i taget. Med seriella läge skapar Resource Manager ett beroende på tidigare instanser i en slinga så att den inte startar en batch tills den föregående batchen har slutförts.

Till exempel för att distribuera seriellt lagringskonton två samtidigt, använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 4,
                "mode": "serial",
                "batchSize": 2
            }
        }
    ],
    "outputs": {}
}
``` 

Egenskapen läget accepterar också **parallella**, vilket är standardvärdet.

## <a name="property-iteration"></a>Egenskapen upprepning

Om du vill skapa flera värden för en egenskap för en resurs lägger du till en `copy` matris i elementet egenskaper. Denna matris innehåller objekt och alla objekt som har följande egenskaper:

* namn – namnet på egenskapen för att skapa flera värden för
* Antal - värden för att skapa
* indata - ett objekt som innehåller värdena som att tilldela egenskapen  

I följande exempel visas hur du använder `copy` dataDisks-egenskapen på en virtuell dator:

```json
{
  "name": "examplevm",
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2017-03-30",
  "properties": {
    "storageProfile": {
      "copy": [{
          "name": "dataDisks",
          "count": 3,
          "input": {
              "lun": "[copyIndex('dataDisks')]",
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Observera att när du använder `copyIndex` inuti en egenskapen iteration måste du ange namnet på upprepning. Du behöver inte ange namnet när det används med resurs iteration.

Hanteraren för filserverresurser expanderar den `copy` matris under distributionen. Namnet på matrisen blir namnet på egenskapen. Indatavärdena bli objektets egenskaper. Den distribuerade mallen blir:

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
              "diskSizeGB": "1023"
          },
          {
              "lun": 1,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          },
          {
              "lun": 2,
              "createOption": "Empty",
              "diskSizeGB": "1023"
          }
      }],
      ...
```

Du kan använda resursen och egenskapen iteration tillsammans. Referens egenskapen iteration efter namn.

```json
{
    "type": "Microsoft.Network/virtualNetworks",
    "name": "[concat(parameters('vnetname'), copyIndex())]",
    "apiVersion": "2016-06-01",
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

## <a name="variable-iteration"></a>Variabeln upprepning

Du kan skapa flera instanser av en variabel med det `copy` element i avsnittet variables. Du kan skapa flera instanser av objekt med relaterade värden och sedan tilldela dessa värden till instanser av resursen. Du kan använda Kopiera för att skapa ett objekt med en matrisegenskap eller en matris. Båda metoderna visas i följande exempel:

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
        }
      ]
    },
    "copy": [
      {
        "name": "disks-top-level-array",
        "count": 5,
        "input": {
          "name": "[concat('myDataDisk', copyIndex('disks-top-level-array', 1))]",
          "diskSizeGB": "1",
          "diskIndex": "[copyIndex('disks-top-level-array')]"
        }
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
    "exampleArray": {
      "value": "[variables('disks-top-level-array')]",
      "type" : "array"
    }
  }
}
```

## <a name="depend-on-resources-in-a-loop"></a>Beroende av resurser i en loop
Du anger att en resurs distribueras efter en annan resurs med hjälp av den `dependsOn` element. Ange namnet på kopian loop i dependsOn-elementet för att distribuera en resurs som är beroende av mängd resurser i en slinga. I följande exempel visas hur du distribuerar tre storage-konton innan du distribuerar den virtuella datorn. Fullständig definitionen för virtuell dator visas inte. Observera att kopiera elementet har name angivet till `storagecopy` och dependsOn-elementet för virtuella datorer har också `storagecopy`.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "apiVersion": "2016-01-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {},
            "copy": {
                "name": "storagecopy",
                "count": 3
            }
        },
        {
            "apiVersion": "2015-06-15", 
            "type": "Microsoft.Compute/virtualMachines", 
            "name": "[concat('VM', uniqueString(resourceGroup().id))]",  
            "dependsOn": ["storagecopy"],
            ...
        }
    ],
    "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Upprepningen för en underordnad-resurs
Du kan inte använda en kopia skapas för en underordnad resurs. Du måste i stället skapa resursen som översta resurs för att skapa flera instanser av en resurs som du vanligtvis definiera som kapslad i en annan resurs. Du definierar relationen med den överordnade resursen via egenskaperna typ och namn.

Anta exempelvis att du definierar en datamängd vanligtvis som en underordnad resurs i en datafabrik.

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
    "resources": [
    {
        "type": "datasets",
        "name": "exampleDataSet",
        "dependsOn": [
            "exampleDataFactory"
        ],
        ...
    }
}]
```

Om du vill skapa flera instanser av datauppsättningar flyttas utanför datafabriken. Dataset måste vara på samma nivå som data factory, men det är fortfarande en underordnad resurs i data factory. Du bevara relationen mellan datauppsättning och datafabriken via egenskaperna typ och namn. Eftersom typen inte kan härledas från dess position i mallen, måste du ange typen av fullständigt kvalificerat i formatet: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Ange ett namn för den datamängd som innehåller namnet på överordnade resurs för att upprätta en överordnad-underordnad relation med en instans av datafabriken. Använd formatet: `{parent-resource-name}/{child-resource-name}`.  

I följande exempel visas implementeringen:

```json
"resources": [
{
    "type": "Microsoft.DataFactory/datafactories",
    "name": "exampleDataFactory",
    ...
},
{
    "type": "Microsoft.DataFactory/datafactories/datasets",
    "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
    "dependsOn": [
        "exampleDataFactory"
    ],
    "copy": { 
        "name": "datasetcopy", 
        "count": "3" 
    } 
    ...
}]
```

## <a name="example-templates"></a>Exempel mallar

Följande exempel visar vanliga scenarier för att skapa flera resurser eller egenskaper.

|Mall  |Beskrivning  |
|---------|---------|
|[Lagringsutrymmet](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuerar flera lagringskonton med index i namnet. |
|[Seriell lagringsutrymmet](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuerar flera lagringskonton som för närvarande. Namnet innehåller antalet index. |
|[Lagringsutrymmet med matris](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuerar flera lagringskonton. Namnet innehåller ett värde från en matris. |
|[Virtuell dator med en ny eller befintlig virtuellt nätverk, lagring och offentlig IP-adress](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-new-or-existing-conditions) |Villkorligt distribuerar nya eller befintliga resurser med en virtuell dator. |
|[Distribution av Virtuella datorer med en variabel antalet datadiskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera datadiskar med en virtuell dator. |
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Visar de olika sätt för att gå på variabler. |
|[Flera säkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuerar flera säkerhetsregler till en nätverkssäkerhetsgrupp. Den skapar säkerhetsregler från en parameter. |

## <a name="next-steps"></a>Nästa steg
* Om du vill veta om avsnitt i en mall finns [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Information om hur du distribuerar mallen finns [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).


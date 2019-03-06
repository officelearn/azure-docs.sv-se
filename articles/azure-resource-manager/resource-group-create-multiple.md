---
title: Distribuera flera instanser av Azure-resurser | Microsoft Docs
description: Använda kopieringen och matriser i en Azure Resource Manager-mall för att iterera flera gånger när du distribuerar resurser.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2019
ms.author: tomfitz
ms.openlocfilehash: c60983dbbe72515fd8f0f4860e169ce1ba69ed45
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57407093"
---
# <a name="deploy-more-than-one-instance-of-a-resource-or-property-in-azure-resource-manager-templates"></a>Distribuera fler än en instans av en resurs eller egenskapen i Azure Resource Manager-mallar

Den här artikeln visar hur du iterera på dina Azure Resource Manager-mall för att skapa fler än en instans av en resurs. Om du vill ange om en resurs är distribuerad på alla, se [elementet](resource-group-authoring-templates.md#condition).

En självstudiekurs finns i [självstudie: skapa flera resursinstanser med hjälp av Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md).

## <a name="resource-iteration"></a>Resursen iteration

När du måste välja under distributionen för att skapa en eller flera instanser av en resurs, lägga till en `copy` elementet så att den resurstypen. I kopieringselementet anger du antalet iterationer och ett namn för den här loopen. Värdet för antal måste vara ett positivt heltal och får inte vara mer än 800. 

Resursen att skapa flera gånger tar följande format:

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

Observera att namnet på varje resurs som innehåller den `copyIndex()` som returnerar den aktuella iterationen loopen. `copyIndex()` är nollbaserat. Detta visas i följande exempel:

```json
"name": "[concat('storage', copyIndex())]",
```

Skapar de här namnen:

* storage0
* storage1
* storage2.

Om du vill åsidosätta indexvärdet kan du skicka ett värde i funktionen copyIndex(). Antal upprepningar att utföra fortfarande har angetts i elementet kopia, men värdet för copyIndex förskjutas av det angivna värdet. Detta visas i följande exempel:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Skapar de här namnen:

* storage1
* storage2
* storage3

Kopieringsåtgärden är användbart när du arbetar med matriser eftersom du kan gå igenom varje element i matrisen. Använd den `length` funktion på matrisen att ange antalet för iterationer, och `copyIndex` att hämta det aktuella indexet i matrisen. Detta visas i följande exempel:

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

Skapar de här namnen:

* storagecontoso
* storagefabrikam
* storagecoho

Som standard skapar Resource Manager resurserna parallellt. Den ordning som de skapas är inte garanterad. Dock kan du ange att resurserna distribueras i följd. Till exempel när du uppdaterar en produktionsmiljö, kanske du vill att sprida ut uppdateringarna så bara ett visst antal uppdateras samtidigt.

För att distribuera seriellt fler än en instans av en resurs, ange `mode` till **seriell** och `batchSize` för antalet instanser som ska distribueras i taget. Med seriell läge skapar Resource Manager ett beroende på tidigare instanser i loopen, så att den inte startar en batch tills den föregående batchen har slutförts.

Till exempel för att distribuera seriellt lagringskonton två i taget, använder du:

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

Egenskapen läge också godkänner **parallella**, vilket är standardvärdet.

## <a name="property-iteration"></a>Egenskapen iteration

Om du vill skapa fler än ett värde för en egenskap för en resurs lägger du till en `copy` matris i properties-elementet. Den här matrisen innehåller objekt och varje objekt har följande egenskaper:

* namn – namnet på egenskapen att skapa flera värden för
* antal – hur många värden för att skapa. Värdet för antal måste vara ett positivt heltal och får inte vara mer än 800.
* indata - ett objekt som innehåller värdena för att tilldela egenskapen  

I följande exempel visas hur du använder `copy` till egenskapen dataDisks på en virtuell dator:

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

Observera att när du använder `copyIndex` inuti en iteration av egenskapen, måste du ange namnet i iteration. Du behöver inte ange namnet när de används med resursen iteration.

Resource Manager expanderar den `copy` matris under distributionen. Namnet på matrisen blir namnet på egenskapen. Indatavärdena bli objektets egenskaper. Den distribuerade mallen blir:

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
      ],
      ...
```

Kopieringselement är en matris, så att du kan ange flera egenskaper för resursen. Lägg till ett objekt för varje egenskap att skapa.

```json
{
  "name": "string",
  "type": "Microsoft.Network/loadBalancers",
  "apiVersion": "2017-10-01",
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

Du kan använda resursen och egenskapen iteration tillsammans. Referens egenskapen iteration efter namn.

```json
{
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[concat(parameters('vnetname'), copyIndex())]",
  "apiVersion": "2018-04-01",
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

## <a name="variable-iteration"></a>Variabeln iteration

Du kan skapa flera instanser av en variabel med det `copy` -egenskapen i variables-avsnittet. Du skapar en matris med element som skapas från värdet i den `input` egenskapen. Du kan använda den `copy` egenskap i en variabel eller på den översta nivån av variables-avsnittet. När du använder `copyIndex` i en variabel iteration måste du ange namnet på iterationen.

Ett enkelt exempel på hur du skapar en matris med strängvärden, se [matris mall](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

I följande exempel visar flera olika sätt att skapa matrisvariabler med dynamiskt konstruerade element. Den visar hur du använder kopiera i en variabel för att skapa matriser av objekt och strängar. Den visar även hur du använder Kopiera på den översta nivån för att skapa matriser av objekt, strängar och heltal.

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

Typen av variabel som skapas beror på indataobjektet. Till exempel variabeln med namnet **top-på flera nivåer – objekt-matris** i föregående exempel returnerar:

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

Och variabeln med namnet **top-nivå--Strängmatrisen** returnerar:

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Beroende resurser i en loop

Du anger att en resurs distribueras efter en annan resurs med hjälp av den `dependsOn` element. Ange namnet på Kopiera loop i dependsOn-elementet för att distribuera en resurs som är beroende av samlingen resurser i en loop. I följande exempel visas hur du distribuerar tre lagringskonton innan du distribuerar den virtuella datorn. Den fullständiga definitionen för virtuell dator visas inte. Observera att kopieringselement har namn som har angetts till `storagecopy` och dependsOn-element för de virtuella datorerna också anges `storagecopy`.

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

<a id="looping-on-a-nested-resource" />

## <a name="iteration-for-a-child-resource"></a>Upprepningen för en underordnad resurs
Du kan inte använda en kopia skapas för en underordnad resurs. Om du vill skapa fler än en instans av en resurs som du vanligtvis definierar som kapslade i en annan resurs, måste du i stället skapa resursen som en resurs på toppnivå. Du definierar relationen med den överordnade resursen via egenskaperna typ och namn.

Anta exempelvis att du vanligtvis definierar en datauppsättning som en underordnad resurs i en data factory.

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
  ]
```

Om du vill skapa fler än en datauppsättning, flytta den utanför data factory. Datauppsättningen måste vara på samma nivå som data factory, men det är fortfarande en underordnad resurs på data factory. Du bevara relationen mellan datauppsättning och data factory via egenskaperna typ och namn. Eftersom typen kan inte längre härledas från dess position i mallen, måste du ange den fullständiga typen i formatet: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Ange ett namn för den datauppsättning som innehåller namnet på överordnade resursen för att upprätta en överordnad/underordnad relation med en instans av data factory. Använd formatet: `{parent-resource-name}/{child-resource-name}`.  

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
  },
  ...
}]
```

## <a name="example-templates"></a>Exempel på mallar

I följande exempel visas vanliga scenarier för att skapa fler än en instans av en resurs eller en egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[Lagringsutrymme på skuggkopian](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuerar mer än ett lagringskonto med index i namnet. |
|[Seriell lagringsutrymmet](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuerar flera lagringskonton som på gång. Namnet innehåller antalet index. |
|[Lagringsutrymmet med matris](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuerar flera lagringskonton. Namnet innehåller ett värde från en matris. |
|[Distribution av virtuella datorer med ett variabelt antal datadiskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera datadiskar med en virtuell dator. |
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Visar de olika sätt för att gå på variabler. |
|[Flera säkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuerar flera säkerhetsregler till en nätverkssäkerhetsgrupp. Den skapar säkerhetsregler från en parameter. Parametern, se [flera NSG-parameterfil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nästa steg

* Om du vill gå igenom en självstudiekurs, se [självstudie: skapa flera resursinstanser med hjälp av Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md).

* Om du vill lära dig om delar av en mall finns i [redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Läs hur du distribuerar mallen i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).


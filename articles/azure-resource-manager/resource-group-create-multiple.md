---
title: Distribuera flera instanser av Azure-resurser | Microsoft Docs
description: Använd kopierings åtgärd och matriser i en Azure Resource Manager-mall för att iterera flera gånger när du distribuerar resurser.
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: tomfitz
ms.openlocfilehash: 3a0761fad32b2cfb0387cca79b6c1c0dc83c8e98
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/27/2019
ms.locfileid: "71345419"
---
# <a name="resource-property-or-variable-iteration-in-azure-resource-manager-templates"></a>Resurs, egenskap eller variabel iteration i Azure Resource Manager mallar

Den här artikeln visar hur du skapar fler än en instans av en resurs, variabel eller egenskap i din Azure Resource Manager-mall. Om du vill skapa flera instanser lägger du till objektet `copy` i mallen.

När det används med en resurs, har objektet Copy följande format:

```json
"copy": {
    "name": "<name-of-loop>",
    "count": <number-of-iterations>,
    "mode": "serial" <or> "parallel",
    "batchSize": <number-to-deploy-serially>
}
```

När det används med en variabel eller egenskap, har objektet Copy följande format:

```json
"copy": [
  {
      "name": "<name-of-loop>",
      "count": <number-of-iterations>,
      "input": <values-for-the-property-or-variable>
  }
]
```

Båda användningarna beskrivs mer ingående i den här artikeln. En själv studie kurs finns i [Självstudier: skapa flera resurs instanser med Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md).

Om du behöver ange om en resurs har distribuerats alls, se [villkors element](conditional-resource-deployment.md).

## <a name="copy-limits"></a>Kopierings gränser

Om du vill ange antalet iterationer anger du ett värde för egenskapen Count. Antalet får inte överskrida 800.

Antalet får inte vara ett negativt tal. Om du distribuerar en mall med Azure PowerShell 2,6 eller senare, Azure CLI 2.0.74 eller senare, eller REST API version **2019-05-10** eller senare, kan du ange antal till noll. Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för Count.

Var försiktig med att använda [fullständig läges distribution](deployment-modes.md) med Copy. Om du omdistribuerar med slutfört läge till en resurs grupp raderas alla resurser som inte är angivna i mallen när du har löst kopierings slingen.

Begränsningarna för antalet är detsamma om de används med en resurs, variabel eller egenskap.

## <a name="resource-iteration"></a>Resurs upprepning

När du måste bestämma under distributionen för att skapa en eller flera instanser av en resurs, lägger du till ett `copy`-element i resurs typen. I elementet kopiera anger du antalet iterationer och ett namn för den här slingan.

Resursen som ska skapas flera gånger tar följande format:

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

Observera att namnet på varje resurs innehåller funktionen `copyIndex()`, som returnerar den aktuella iterationen i slingan. `copyIndex()` är nollbaserat. I följande exempel:

```json
"name": "[concat('storage', copyIndex())]",
```

Skapar följande namn:

* storage0
* storage1
* storage2.

Om du vill åsidosätta indexvärdet kan du skicka ett värde i funktionen copyIndex(). Antalet iterationer har fortfarande angetts i kopierings elementet, men värdet för copyIndex motbokas med det angivna värdet. I följande exempel:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Skapar följande namn:

* storage1
* storage2
* storage3

Kopierings åtgärden är användbar när du arbetar med matriser eftersom du kan iterera igenom varje element i matrisen. Använd funktionen `length` på matrisen för att ange antalet iterationer och `copyIndex` för att hämta det aktuella indexet i matrisen. I följande exempel:

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

Skapar följande namn:

* storagecontoso
* storagefabrikam
* storagecoho

Som standard skapar Resource Manager resurserna parallellt. Den tillämpar ingen gräns för antalet resurser som distribueras parallellt, förutom den totala gränsen på 800-resurser i mallen. Ordningen som de har skapats i är inte garanterad.

Men du kanske vill ange att resurserna distribueras i följd. Till exempel, när du uppdaterar en produktions miljö, kanske du vill sprida uppdateringarna så att bara ett visst nummer uppdateras vid ett tillfälle. Om du vill distribuera mer än en instans av en resurs kan du ange `mode` till **seriell** och `batchSize` till antalet instanser som ska distribueras i taget. Med seriellt läge skapar Resource Manager ett beroende på tidigare instanser i slingan, så det går inte att starta en batch förrän den föregående batchen har slutförts.

Om du till exempel vill distribuera lagrings konton två i taget, använder du:

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

Egenskapen mode godkänner också **Parallel**, vilket är standardvärdet.

Information om hur du använder kopiera med kapslade mallar finns i [använda kopiera](resource-group-linked-templates.md#using-copy).

## <a name="property-iteration"></a>Egenskaps upprepning

Om du vill skapa mer än ett värde för en egenskap i en resurs lägger du till en `copy`-matris i egenskaper-elementet. Den här matrisen innehåller objekt, och varje objekt har följande egenskaper:

* Namn – namnet på egenskapen för att skapa flera värden för
* Count – antalet värden som ska skapas.
* Ange ett objekt som innehåller de värden som ska tilldelas egenskapen

I följande exempel visas hur du tillämpar `copy` på data disks-egenskapen på en virtuell dator:

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

Observera att när du använder `copyIndex` inuti en egenskap iteration måste du ange namnet på iterationen. Du behöver inte ange namnet när det används med resurs upprepning.

Resource Manager expanderar `copy`-matrisen under distributionen. Namnet på matrisen blir namnet på egenskapen. De angivna värdena blir objekt egenskaperna. Den distribuerade mallen blir:

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

Kopierings elementet är en matris så att du kan ange mer än en egenskap för resursen. Lägg till ett objekt för varje egenskap som ska skapas.

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

Du kan använda en iteration av resurs och egenskap tillsammans. Referera till egenskapen iteration efter namn.

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

## <a name="variable-iteration"></a>Variabel iteration

Om du vill skapa flera instanser av en variabel använder du egenskapen `copy` i avsnittet Variables. Du skapar en matris med element som skapats från värdet i egenskapen `input`. Du kan använda egenskapen `copy` i en variabel eller på den översta nivån i avsnittet Variables. När du använder `copyIndex` inuti en variabel iteration måste du ange namnet på iterationen.

Ett enkelt exempel på hur du skapar en matris med sträng värden finns i [Kopiera array-mall](https://github.com/bmoore-msft/AzureRM-Samples/blob/master/copy-array/azuredeploy.json).

I följande exempel visas flera olika sätt att skapa mat ris variabler med dynamiskt konstruerade element. Det visar hur du använder kopiera inuti en variabel för att skapa matriser med objekt och strängar. Det visar också hur du använder kopiera på översta nivån för att skapa matriser med objekt, strängar och heltal.

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

Vilken typ av variabel som skapas beror på det inmatade objektet. Till exempel returnerar variabeln **toppnivå objekts mat ris** i föregående exempel:

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

Och returnerar variabeln med namnet **toppnivå sträng mat ris** :

```json
[
  "myDataDisk1",
  "myDataDisk2",
  "myDataDisk3",
  "myDataDisk4",
  "myDataDisk5"
]
```

## <a name="depend-on-resources-in-a-loop"></a>Är beroende av resurser i en slinga

Du anger att en resurs distribueras efter en annan resurs med hjälp av elementet `dependsOn`. Om du vill distribuera en resurs som är beroende av resurs samlingen i en slinga, anger du namnet på kopierings slingan i dependsOn-elementet. I följande exempel visas hur du distribuerar tre lagrings konton innan du distribuerar den virtuella datorn. Den fullständiga definitionen av virtuell dator visas inte. Observera att kopierings elementet har ett namn angivet till `storagecopy` och dependsOn-elementet för Virtual Machines också är inställt på `storagecopy`.

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

## <a name="iteration-for-a-child-resource"></a>Iteration för en underordnad resurs
Du kan inte använda en kopierings slinga för en underordnad resurs. Om du vill skapa mer än en instans av en resurs som du vanligt vis definierar som kapslad i en annan resurs måste du i stället skapa den resursen som en resurs på den översta nivån. Du definierar relationen med den överordnade resursen genom egenskaperna typ och namn.

Anta till exempel att du vanligt vis definierar en data uppsättning som en underordnad resurs i en data fabrik.

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

Om du vill skapa mer än en data uppsättning flyttar du den utanför data fabriken. Data uppsättningen måste vara på samma nivå som data fabriken, men den är fortfarande en underordnad resurs till data fabriken. Du bevarar relationen mellan data uppsättningen och data fabriken genom egenskaperna typ och namn. Eftersom typen inte längre kan härledas från dess position i mallen måste du ange den fullständigt kvalificerade typen i formatet: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`.

Om du vill upprätta en överordnad/underordnad relation med en instans av data fabriken anger du ett namn för den data uppsättning som innehåller namnet på den överordnade resursen. Använd formatet: `{parent-resource-name}/{child-resource-name}`.

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

I följande exempel visas vanliga scenarier för att skapa mer än en instans av en resurs eller egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera lagring](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuerar fler än ett lagrings konto med ett index nummer i namnet. |
|[Lagring av serie kopia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuerar flera lagrings konton en i taget. Namnet innehåller index numret. |
|[Kopiera lagring med matris](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuerar flera lagrings konton. Namnet innehåller ett värde från en matris. |
|[VM-distribution med ett variabel antal data diskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera data diskar till en virtuell dator. |
|[Kopiera variabler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copyvariables.json) |Visar de olika sätten att iterera på variabler. |
|[Flera säkerhets regler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuerar flera säkerhets regler till en nätverks säkerhets grupp. Den skapar säkerhets reglerna från en parameter. För-parametern, se [flera NSG-parameter fil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom självstudierna finns i [Självstudier: skapa flera resurs instanser med Resource Manager-mallar](./resource-manager-tutorial-create-multiple-instances.md).

* Om du vill lära dig mer om avsnitten i en mall, se [redigera Azure Resource Manager mallar](resource-group-authoring-templates.md).
* Information om hur du distribuerar din mall finns i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).


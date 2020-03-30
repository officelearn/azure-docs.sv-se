---
title: Distribuera flera instanser av resurser
description: Använd kopieringsåtgärd och matriser i en Azure Resource Manager-mall för att distribuera resurstyp många gånger.
ms.topic: conceptual
ms.date: 09/27/2019
ms.openlocfilehash: e65ab93c21daffa0053e53d953fe95fa9f28e2a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153326"
---
# <a name="resource-iteration-in-arm-templates"></a>Resursiteration i ARM-mallar

Den här artikeln visar hur du skapar mer än en instans av en resurs i din AZURE Resource Manager -mall (ARM). Genom att **copy** lägga till kopieringselementet i avsnittet resurser i mallen kan du ange hur många resurser som ska distribueras dynamiskt. Du undviker också att behöva upprepa mallsyntaxen.

Du kan också använda kopia med [egenskaper,](copy-properties.md) [variabler](copy-variables.md) och [utdata](copy-outputs.md).

Om du behöver ange om en resurs ska distribueras alls läser du [villkorselementet](conditional-resource-deployment.md).

## <a name="resource-iteration"></a>Resursiteration

Kopieringselementet har följande allmänna format:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Namnegenskapen är ett värde som identifierar loopen. **name** Egenskapen **Count** anger hur många iterationer du vill använda för resurstypen.

Använd egenskaperna **läge** och **batchSize** för att ange om resurserna distribueras parallellt eller i följd. Dessa egenskaper beskrivs i [Seriell eller Parallell](#serial-or-parallel).

I följande exempel skapas antalet lagringskonton som anges i parametern **storageCount.**

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
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
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
    "outputs": {}
}
```

Observera att namnet på varje `copyIndex()` resurs innehåller funktionen, som returnerar den aktuella iterationen i loopen. `copyIndex()` är nollbaserat. Så, följande exempel:

```json
"name": "[concat('storage', copyIndex())]",
```

Skapar följande namn:

* lagring0
* lagring1
* lagring2.

Om du vill åsidosätta indexvärdet kan du skicka ett värde i funktionen copyIndex(). Antalet iterationer anges fortfarande i kopieringselementet, men värdet för copyIndex kompenseras av det angivna värdet. Så, följande exempel:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Skapar följande namn:

* lagring1
* lagring2
* lagring3

Kopieringen är användbar när du arbetar med matriser eftersom du kan iterera genom varje element i matrisen. Använd `length` funktionen på matrisen för att ange `copyIndex` antalet för iterationer och för att hämta det aktuella indexet i matrisen.

I följande exempel skapas ett lagringskonto för varje namn som anges i parametern.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
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
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

Om du vill returnera värden från de distribuerade resurserna kan du använda [kopia i utdataavsnittet](copy-outputs.md).

## <a name="serial-or-parallel"></a>Seriell eller parallell

Som standard skapar Resource Manager resurserna parallellt. Den gäller ingen gräns för antalet resurser som distribueras parallellt, förutom den totala gränsen på 800 resurser i mallen. Den ordning i vilken de skapas är inte garanterad.

Du kanske vill ange att resurserna ska distribueras i följd. När du till exempel uppdaterar en produktionsmiljö kanske du vill sprida uppdateringarna så att endast ett visst antal uppdateras samtidigt. Om du vill distribuera mer än en `mode` instans `batchSize` av en resurs i seriell tid anger du till **seriell och** antalet instanser som ska distribueras åt gången. Med seriellt läge skapar Resource Manager ett beroende av tidigare instanser i loopen, så att den inte startar en batch förrän den föregående batchen är klar.

Om du till exempel vill distribuera lagringskonton två i taget flera serier använder du:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

Egenskapen mode accepterar också **parallell**, vilket är standardvärdet.

## <a name="depend-on-resources-in-a-loop"></a>Lita på resurser i en loop

Du anger att en resurs ska distribueras efter en annan resurs med hjälp av elementet. `dependsOn` Om du vill distribuera en resurs som är beroende av insamlingen av resurser i en loop anger du namnet på kopieringsloopen i elementet dependsOn. I följande exempel visas hur du distribuerar tre lagringskonton innan du distribuerar den virtuella datorn. Den fullständiga definitionen av virtuella datorer visas inte. Observera att kopieringselementet `storagecopy` har namnet inställt på och att elementet dependsOn för den virtuella datorn också är inställt på `storagecopy`.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 3
      },
      "properties": {}
    },
    {
      "type": "Microsoft.Compute/virtualMachines",
      "apiVersion": "2015-06-15",
      "name": "[concat('VM', uniqueString(resourceGroup().id))]",
      "dependsOn": ["storagecopy"],
      ...
    }
  ],
  "outputs": {}
}
```

## <a name="iteration-for-a-child-resource"></a>Iteration för en underordnad resurs

Du kan inte använda en kopieringsloop för en underordnad resurs. Om du vill skapa mer än en instans av en resurs som du vanligtvis definierar som kapslad inom en annan resurs måste du i stället skapa resursen som en resurs på den högsta nivån. Du definierar relationen med den överordnade resursen via typ- och namnegenskaperna.

Anta till exempel att du vanligtvis definierar en datauppsättning som en underordnad resurs i en datafabrik.

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

Om du vill skapa mer än en datauppsättning flyttar du den utanför datafabriken. Datauppsättningen måste vara på samma nivå som datafabriken, men den är fortfarande en underordnad resurs för datafabriken. Du kan bevara relationen mellan datauppsättning och datafabrik genom typ- och namnegenskaperna. Eftersom typ inte längre kan härledas från dess position i mallen `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}`måste du ange den fullständigt kvalificerade typen i formatet: .

Om du vill upprätta en överordnad/underordnad relation med en instans av datafabriken anger du ett namn på den datauppsättning som innehåller det överordnade resursnamnet. Använd formatet `{parent-resource-name}/{child-resource-name}`.

Följande exempel visar implementeringen:

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

## <a name="copy-limits"></a>Kopiera gränser

Antalet får inte överstiga 800.

Antalet kan inte vara ett negativt tal. Om du distribuerar en mall med Azure PowerShell 2.6 eller senare, Azure CLI 2.0.74 eller senare, eller REST API version **2019-05-10** eller senare, kan du ange antal till noll. Tidigare versioner av PowerShell, CLI och REST API stöder inte noll för antal.

Var försiktig med att använda [fullständig lägesdistribution](deployment-modes.md) med kopia. Om du distribuerar om med fullständigt läge till en resursgrupp tas alla resurser som inte anges i mallen efter att du har löst kopieringsloopen bort.

## <a name="example-templates"></a>Exempel på mallar

Följande exempel visar vanliga scenarier för att skapa mer än en instans av en resurs eller egenskap.

|Mall  |Beskrivning  |
|---------|---------|
|[Kopiera lagring](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Distribuerar mer än ett lagringskonto med ett indexnummer i namnet. |
|[Lagring av seriell kopia](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Distribuerar flera lagringskonton en i taget. Namnet innehåller indexnumret. |
|[Kopiera lagring med matris](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Distribuerar flera lagringskonton. Namnet innehåller ett värde från en matris. |
|[VM-distribution med ett varierande antal datadiskar](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-windows-copy-datadisks) |Distribuerar flera datadiskar med en virtuell dator. |
|[Flera säkerhetsregler](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.json) |Distribuerar flera säkerhetsregler till en nätverkssäkerhetsgrupp. Säkerhetsregler na konstrueras från en parameter. För parametern finns [i flera NSG-parameterfil](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/multiplesecurityrules.parameters.json). |

## <a name="next-steps"></a>Nästa steg

* Information om hur du går igenom en självstudiekurs finns i [Självstudiekurs: skapa flera resursinstanser med ARM-mallar](template-tutorial-create-multiple-instances.md).
* För annan användning av kopieringselementet, se:
  * [Egenskapsiter iteration i ARM-mallar](copy-properties.md)
  * [Variabel iteration i ARM-mallar](copy-variables.md)
  * [Utdataiteration i ARM-mallar](copy-outputs.md)
* Information om hur du använder kopiera med kapslade mallar finns i [Använda kopia](linked-templates.md#using-copy).
* Om du vill lära dig mer om avsnitten i en mall läser [du Skapa ARM-mallar](template-syntax.md).
* Mer information om hur du distribuerar mallen finns i [Distribuera ett program med ARM-mall](deploy-powershell.md).


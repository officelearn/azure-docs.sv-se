---
title: Exportera Resource Manager-mall med Azure PowerShell | Microsoft Docs
description: Använda Azure Resource Manager och Azure PowerShell för att exportera en mall från en resursgrupp.
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
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: cca81bf3f5a46b32cc901a0ac6024eb7888685f7
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55081613"
---
# <a name="export-azure-resource-manager-templates-with-powershell"></a>Exportera Azure Resource Manager-mallar med PowerShell

Med Resource Manager kan du exportera en Resource Manager-mall från befintliga resurser i din prenumeration. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs.

Det är viktigt att Observera att det finns två sätt att exportera en mall:

* Du kan exportera de **mall som används för en distribution**. Den exporterade mallen innehåller alla parametrar och variabler exakt som de visas i den ursprungliga mallen. Den här metoden är användbar när du vill hämta en mall.
* Du kan exportera en **genererad mall som representerar resursgruppens aktuella tillstånd**. Den exporterade mallen baseras inte på en mall som du har använt för distribution. I stället skapar den en mall som är en ”ögonblicksbild” eller ”säkerhetskopiering” för resursgruppen. Den exporterade mallen har många hårdkodade värden och troligen inte så många parametrar som du vanligtvis definierar. Använd det här alternativet för att distribuera om resurser till samma resursgrupp. Du kan behöva ändra avsevärt den om du vill använda den här mallen för en annan resursgrupp.

Den här artikeln visar båda metoderna.

## <a name="deploy-a-solution"></a>Distribuera en lösning

För att illustrera båda metoderna för att exportera en mall, låt oss börja med att distribuera en lösning till din prenumeration. Om du redan har en resursgrupp i din prenumeration som du vill exportera, har du inte distribuera den här lösningen. Men handlar resten av den här artikeln om att mallen för den här lösningen. Exempelskriptet distribuerar ett lagringskonto.

```powershell
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -DeploymentName NewStorage
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```  

## <a name="save-template-from-deployment-history"></a>Spara mallen från distributionshistoriken

Du kan hämta en mall från distributionshistoriken med hjälp av den [spara AzureRmResourceGroupDeploymentTemplate](/powershell/module/az.resources/save-azresourcegroupdeploymenttemplate) kommando. I följande exempel sparar den mall som du tidigare har distribuerat:

```powershell
Save-AzResourceGroupDeploymentTemplate -ResourceGroupName ExampleGroup -DeploymentName NewStorage
```

Den returnerar platsen för mallen.

```powershell
Path
----
C:\Users\exampleuser\NewStorage.json
```

Öppna filen och Lägg märke till att det är den exakta mallen som du använde för distributionen. Parametrar och variabler matchar mallen från GitHub. Du kan distribuera om den här mallen.

## <a name="export-resource-group-as-template"></a>Exportera resursgrupp som mall

I stället för att hämta en mall från distributionshistoriken, kan du hämta en mall som representerar det aktuella tillståndet för en resursgrupp med hjälp av den [Export-AzureRmResourceGroup](/powershell/module/az.resources/export-azresourcegroup) kommando. Du använder det här kommandot när du har gjort många ändringar i resursgruppen och inga befintliga mall som representerar alla ändringar. Den är avsedd som en ögonblicksbild av resursgruppen som du kan använda för att distribuera om till samma resursgrupp. Om du vill använda den exporterade mallen andra lösningar, måste du avsevärt ändra den.

```powershell
Export-AzResourceGroup -ResourceGroupName ExampleGroup
```

Den returnerar platsen för mallen.

```powershell
Path
----
C:\Users\exampleuser\ExampleGroup.json
```

Öppna filen och Lägg märke till att det är annorlunda än mallen i GitHub. Det har olika parametrar och inga variabler. Lagrings-SKU och plats är hårdkodade värden. I följande exempel visas den exporterade mallen, men mallen har ett något annorlunda parameternamn:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccounts_nf3mvst4nqb36standardsa_name": {
      "defaultValue": null,
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[parameters('storageAccounts_nf3mvst4nqb36standardsa_name')]",
      "apiVersion": "2016-01-01",
      "location": "southcentralus",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Du kan distribuera om den här mallen, men det krävs att gissa ett unikt namn för lagringskontot. Namnet på parametern är något annorlunda.

```powershell
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup `
  -TemplateFile C:\Users\exampleuser\ExampleGroup.json `
  -storageAccounts_nf3mvst4nqb36standardsa_name tfnewstorage0501
```

## <a name="customize-exported-template"></a>Anpassa exporterad mall

Du kan ändra den här mallen för att göra det enklare att använda och mer flexibel. Om du vill tillåta för flera platser, ändrar du egenskapen location om du vill använda samma plats som resursgruppen:

```json
"location": "[resourceGroup().location]",
```

Ta bort parametern för lagringskontonamn så att du slipper gissa ett uniques namn för lagringskontot. Lägg till en parameter för ett storage-namnsuffix och en lagrings-SKU:

```json
"parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
},
```

Lägg till en variabel som konstruerar lagringskontonamnet med funktionen uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Ange namnet på lagringskontot till variabeln:

```json
"name": "[variables('storageAccountName')]",
```

Ange SKU-parameter:

```json
"sku": {
    "name": "[parameters('storageAccountType')]",
    "tier": "Standard"
},
```

Nu ser din mall ut så här:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageSuffix": {
      "type": "string",
      "defaultValue": "standardsa"
    },
    "storageAccountType": {
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS"
      ],
      "type": "string",
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), parameters('storageSuffix'))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "sku": {
        "name": "[parameters('storageAccountType')]",
        "tier": "Standard"
      },
      "kind": "Storage",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {},
      "dependsOn": []
    }
  ]
}
```

Distribuera om den ändrade mallen.

## <a name="next-steps"></a>Nästa steg
* Information om hur du använder portalen för att exportera en mall finns i [exportera en Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).
* För att definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](resource-manager-common-deployment-errors.md).

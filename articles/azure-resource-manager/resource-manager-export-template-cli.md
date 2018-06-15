---
title: Exportera Resource Manager-mall med Azure CLI | Microsoft Docs
description: Använda Azure Resource Manager och Azure CLI för att exportera en mall från en resursgrupp.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/23/2018
ms.author: tomfitz
ms.openlocfilehash: 1d73142931a5cfa84cb24df7a85c799a0f508385
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
ms.locfileid: "34358837"
---
# <a name="export-azure-resource-manager-templates-with-azure-cli"></a>Exportera Azure Resource Manager-mallar med Azure CLI

Med Resource Manager kan du exportera en Resource Manager-mall från befintliga resurser i din prenumeration. Du kan använda mallen som genereras för att lära dig mer om mallsyntaxen eller för att automatisera omdistributionen av din lösning om det behövs.

Det är viktigt att Observera att det finns två sätt att exportera en mall:

* Du kan exportera den **faktiska mall som används för en distribution**. Den exporterade mallen innehåller alla parametrar och variabler exakt som de visas i den ursprungliga mallen. Den här metoden är användbar när du vill hämta en mall.
* Du kan exportera en **genererad mall som representerar resursgruppens aktuella tillstånd**. Den exporterade mallen baseras inte på en mall som du har använt för distribution. I stället skapar en mall som är en ”ögonblicksbild” eller ”backup” av resursgruppen. Den exporterade mallen har många hårdkodade värden och troligen inte så många parametrar som du vanligtvis definierar. Använd det här alternativet om du vill distribuera resurser i samma resursgrupp. För att använda den här mallen för en annan resursgrupp måste kanske du ändra avsevärt den.

Den här artikeln visar båda metoderna.

## <a name="deploy-a-solution"></a>Distribuera en lösning

För att illustrera båda metoderna för att exportera en mall, börja med att distribuera en lösning till din prenumeration. Om du redan har en resursgrupp i din prenumeration som du vill exportera, behöver du inte distribuera den här lösningen. Men handlar resten av den här artikeln mallen för den här lösningen. Exempelskriptet distribuerar ett lagringskonto.

```azurecli
az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name NewStorage \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
```  

## <a name="save-template-from-deployment-history"></a>Spara mallen från distributionshistoriken

Du kan hämta en mall från distributionshistoriken med hjälp av den [az distribution exportera](/cli/azure/group/deployment#az_group_deployment_export) kommando. I följande exempel sparas den mall som du distribuerar tidigare:

```azurecli
az group deployment export --name NewStorage --resource-group ExampleGroup
```

Returnerar den mallen. Kopiera JSON och spara som en fil. Observera att det är exakt mallen som du använde för distribution. Parametrar och variabler matchar mallen från GitHub. Du kan distribuera den här mallen.


## <a name="export-resource-group-as-template"></a>Exportera resursgrupp som mall

I stället för att hämta en mall från distributionshistoriken, kan du hämta en mall som representerar det aktuella tillståndet för en resursgrupp med hjälp av den [az exportera](/cli/azure/group#az_group_export) kommando. Du kan använda det här kommandot när du har gjort många ändringar i resursgruppen och inga befintliga mallen som representerar alla ändringar. Den är avsedd som en ögonblicksbild av resursgrupp, där du kan distribuera att samma resursgrupp. Om du vill använda den exporterade mallen andra lösningar ändra du avsevärt den.

```azurecli
az group export --name ExampleGroup
```

Returnerar den mallen. Kopiera JSON och spara som en fil. Observera att det skiljer sig från mallen i GitHub. Mallen har olika parametrar och inga variabler. SKU-lagring och plats är hårdkodat till värden. I följande exempel visas den exporterade mallen, men mallen har ett något annorlunda parameternamn:

```json
{
  "parameters": {
    "storageAccounts_mcyzaljiv7qncstandardsa_name": {
      "type": "String",
      "defaultValue": null
    }
  },
  "variables": {},
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "resources": [
    {
      "tags": {},
      "dependsOn": [],
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
      },
      "kind": "Storage",
      "type": "Microsoft.Storage/storageAccounts",
      "location": "centralus",
      "name": "[parameters('storageAccounts_mcyzaljiv7qncstandardsa_name')]",
      "properties": {}
    }
  ],
  "contentVersion": "1.0.0.0"
}
```

Du kan distribuera den här mallen, men det krävs ett unikt namn för storage-konto för att gissa. Namnet på parametern är något annorlunda.

```azurecli
az group deployment create --name NewStorage --resource-group ExampleGroup \
  --template-file examplegroup.json \
  --parameters "{\"storageAccounts_mcyzaljiv7qncstandardsa_name\":{\"value\":\"tfstore0501\"}}"
```

## <a name="customize-exported-template"></a>Anpassa exporterad mall

Du kan ändra den här mallen för att göra det enklare att använda och mer flexibelt. Ändra Platsegenskapen om du vill använda samma plats som resursgruppen för att tillåta för flera platser:

```json
"location": "[resourceGroup().location]",
```

Ta bort parametern för lagringskontonamn om du vill undvika att gissa ett uniques namn för storage-konto. Lägga till en parameter för ett namnsuffix för lagring och lagring SKU:

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

Lägg till en variabel som konstruktioner lagringskontonamnet med funktionen uniqueString:

```json
"variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
```

Ange namnet på lagringskontot till variabeln:

```json
"name": "[variables('storageAccountName')]",
```

Ange SKU: N i parametern:

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
* Om du vill definiera parametrar i mallen, se [Webbsidemallar](resource-group-authoring-templates.md#parameters).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
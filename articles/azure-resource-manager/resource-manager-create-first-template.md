---
title: "Skapa din första Azure Resource Manager-mall | Microsoft Docs"
description: "Stegvisa instruktioner för hur du skapar din första Azure Resource Manager-mall. I guiden beskrivs hur du skapar mallen med hjälp av mallreferensen för ett lagringskonto."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/21/2017
ms.topic: get-started-article
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: f861f2ba0376f00ff21e03cbb81a04a266f38630
ms.lasthandoff: 03/22/2017

---

# <a name="create-your-first-azure-resource-manager-template"></a>Skapa din första Azure Resource Manager-mall
Den här artikeln beskriver steg för steg hur du skapar din första Azure Resource Manager-mall. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. En beskrivning av de begrepp som används i samband med distribution och hantering av Azure-lösningar finns i [Översikt över Azure Resource Manager](resource-group-overview.md). Om du har befintliga resurser och behöver en mall för dessa resurser kan du läsa [Exportera en Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).

Du behöver en JSON-redigerare för att skapa och ändra mallar. [Visual Studio Code](https://code.visualstudio.com/) är en enkel, plattformsoberoende kodredigerare med öppen källkod. Med den kan du skapa och redigera Resource Manager-mallar via ett tillägg. Den här artikeln förutsätter att du använder Virtual Studio Code, men om du har en annan JSON-redigerare kan du använda den.

## <a name="get-vs-code-and-extension"></a>Hämta VS Code och tillägget
1. Installera VS Code på [https://code.visualstudio.com/](https://code.visualstudio.com/) (om det behövs).

2. Installera tillägget [Azure Resource Manager Tools](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools) via snabböppning (Ctrl+P) och kör: 

   ```
   ext install msazurermtools.azurerm-vscode-tools
   ```

3. Starta om VS Code när du uppmanas att aktivera tillägget.

## <a name="create-blank-template"></a>Skapa en tom mall

Låt oss börja med en tom mall som endast innehåller de grundläggande delarna i en mall.

1. Skapa en fil. 

2. Kopiera och klistra in följande JSON-syntax i filen:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [  ],
     "outputs": {  }
   }
   ```

3. Spara filen som **azuredeploy.json**. 

## <a name="add-storage-account"></a>Lägg till lagringskonto
1. Om du vill definiera ett lagringskonto för distribution lägger du till lagringskontot i avsnittet **resurser** i mallen. Tillgängliga värden för lagringskontot finns i [mallreferensen för lagringskonton](/azure/templates/microsoft.storage/storageaccounts). Kopiera JSON som visas för lagringskontot. 

3. Klistra in JSON i avsnittet **resurser** i din mall enligt följande exempel: 

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {  },
     "variables": {  },
     "resources": [
       {
         "name": "string",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-05-01",
         "sku": {
           "name": "string"
         },
         "kind": "string",
         "location": "string",
         "tags": {},
         "properties": {
           "customDomain": {
             "name": "string",
             "useSubDomain": boolean
           },
           "encryption": {
             "services": {
               "blob": {
                 "enabled": boolean
               }
             },
             "keySource": "Microsoft.Storage"
           },
           "accessTier": "string"
         }
       }
     ],
     "outputs": {  }
   }
   ```

  Föregående exempel innehåller många platshållarvärden och vissa egenskaper som kanske inte behövs i ditt lagringskonto.

## <a name="set-values-for-storage-account"></a>Ange värden för lagringskontot

Nu är du redo att ange värden för ditt lagringskonto. 

1. Titta igen i [mallreferensen för lagringskonton](/azure/templates/microsoft.storage/storageaccounts) på den plats som du kopierade JSON till. Det finns flera tabeller som beskriver egenskaper och visar tillgängliga värden. 

2. Observera att **customDomain**, **encryption** och **accessTier** i elementet **properties** visas som valfria värden. Dessa värden kan vara viktiga beroende på dina behov, men för enkelhetens skull tar vi bort dem i det här exemplet.

   ```json
   "resources": [
     {
       "name": "string",
       "type": "Microsoft.Storage/storageAccounts",
       "apiVersion": "2016-05-01",
       "sku": {
         "name": "string"
       },
       "kind": "string",
       "location": "string",
       "tags": {},
       "properties": {
       }
     }
   ],
   ```

3. Elementet **kind** har ett platshållarvärde ("string"). I VS Code finns flera funktioner som hjälper dig att förstå de värden som används i mallen. Observera att VS Code indikerar att det här värdet inte är giltigt. Om du hovrar över ”string” anger VS Code att giltiga värden för **kind** är `Storage` eller `BlobStorage`. 

   ![visa föreslagna värden i VS Code](./media/resource-manager-create-first-template/vs-code-show-values.png)

   Om du vill se tillgängliga värden tar du bort tecknen innanför citattecknen och väljer **Ctrl+Blanksteg**. Välj **Storage** bland de tillgängliga alternativen.
  
   ![visa IntelliSense](./media/resource-manager-create-first-template/intellisense.png)

   Om du inte använder VS Code kan du titta på mallreferenssidan för lagringskonton. Observera att beskrivningen innehåller samma giltiga värden. Ange **Storage** för elementet.

   ```json
   "kind": "Storage",
   ```

Nu ser din mall ut så här:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "string",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-template-function"></a>Funktionen Lägg till mall

Du använder funktioner i din mall för att förenkla syntaxen för mallen och för att hämta värden som endast är tillgängliga när mallen distribueras. En fullständig lista med mallfunktioner finns i [Azure Resource Manager-mallfunktioner](resource-group-template-functions.md).

Om du vill att lagringskontot ska distribueras till samma plats som resursgruppen anger du följande värde för egenskapen **location**:

```json
"location": "[resourceGroup().location]",
```

Även här får du förslag på tillgängliga funktioner i VS Code. 

![visa funktioner](./media/resource-manager-create-first-template/show-functions.png)

Observera att funktionen omges av hakparenteser. Funktionen [resourceGroup](resource-group-template-functions.md#resourcegroup) returnerar ett objekt med egenskapen `location`. Resursgruppen innehåller alla relaterade resurser för din lösning. Du skulle kunna hårdkoda platsegenskapen (”location”) till ett värde som ”USA, centrala”, men i så fall skulle du behöva ändra mallen manuellt för att distribuera om den till en annan plats. Med funktionen `resourceGroup` är det enkelt att distribuera om mallen till en annan resursgrupp på en annan plats.

Nu ser din mall ut så här:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {  },
  "variables": {  },
  "resources": [
    {
      "name": "string",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "string"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="add-parameters-and-variables"></a>Lägga till parametrar och variabler
Det finns bara två värden kvar att ange i din mall: **name** och **sku.name**. För dessa egenskaper lägger du till parametrar som gör att du kan ändra dessa värden under distributionen. 

Lagringskontonamn har flera begränsningar som gör att de är svåra att ange. Namnet måste vara mellan 3 och 24 tecken långt, endast bestå av siffror och gemener samt vara unikt. I stället för att försöka gissa ett värde som uppfyller kraven och som är unikt kan du generera ett hashvärde med funktionen [uniqueString](resource-group-template-functions.md#uniquestring). Du kan göra hashvärdet mer begripligt genom att lägga till ett prefix som hjälper dig att identifiera kontot som ett lagringskonto efter distributionen. 

1. Om du vill skicka ett prefix för namnet som matchar dina namngivningskonventioner går du till avsnittet för **parametrar** för din mall. Lägg till en parameter i mallen som godtar ett prefix i lagringskontonamnet:

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     }
   },
   ```

  Prefixet får högst innehålla 11 tecken eftersom `uniqueString` returnerar 13 tecken (hela namnet får inte överskrida 24 tecken). Om du inte skickar något värde för parametern under distributionen används standardvärdet.

2. Gå till avsnittet för **variabler** i mallen. Lägg till följande variabel för att bygga namnet med prefixet och den unika strängen:

   ```json
   "variables": {
     "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
   },
   ```

3. I avsnittet för **resurser** anger du lagringskontonamnet till den variabeln.

   ```json
   "name": "[variables('storageName')]",
   ```

3. Om du vill skicka andra SKU:er för lagringskontot går du till avsnittet för **parametrar**. Efter parametern för lagringsnamnsprefixet lägger du till en parameter som anger tillåtna SKU-värden och ett standardvärde. Tillåtna värden finns på mallreferenssidan eller i VS Code. I följande exempel tar du med alla giltiga värden för SKU. Du kan dock begränsa de tillåtna värdena till endast de typer av SKU:er som du vill distribuera via den här mallen.

   ```json
   "parameters": {
     "storageNamePrefix": {
       "type": "string",
       "maxLength": 11,
       "defaultValue": "storage",
       "metadata": {
         "description": "The value to use for starting the storage account name."
       }
     },
     "storageSKU": {
       "type": "string",
       "allowedValues": [
         "Standard_LRS",
         "Standard_ZRS",
         "Standard_GRS",
         "Standard_RAGRS",
         "Premium_LRS"
       ],
       "defaultValue": "Standard_LRS",
       "metadata": {
         "description": "The type of replication to use for the storage account."
       }
     }
   },
   ```

3. Ändra SKU-egenskapen för att använda värdet från parametern:

   ```json
   "sku": {
     "name": "[parameters('storageSKU')]"
   },
   ```    

4. Spara filen.

Nu ser din mall ut så här:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name."
      }
    },
    "storageSKU": {
      "type": "string",
      "allowedValues": [
        "Standard_LRS",
        "Standard_ZRS",
        "Standard_GRS",
        "Standard_RAGRS",
        "Premium_LRS"
      ],
      "defaultValue": "Standard_LRS",
      "metadata": {
        "description": "The type of replication to use for the storage account."
      }
    }
  },
  "variables": {
    "storageName": "[concat(parameters('storageNamePrefix'), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-05-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
      }
    }
  ],
  "outputs": {  }
}
```

## <a name="next-steps"></a>Nästa steg
* Mallen har slutförts och du är redo att distribuera den till din prenumeration. Information om distribution finns i [Distribuera resurser till Azure](resource-manager-quickstart-deploy.md).
* Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).


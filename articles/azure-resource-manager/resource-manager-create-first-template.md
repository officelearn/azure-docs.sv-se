---
title: "Skapa din första Azure Resource Manager-mall | Microsoft Docs"
description: "Stegvisa instruktioner för hur du skapar din första Azure Resource Manager-mall. I guiden beskrivs hur du skapar mallen med hjälp av mallreferensen för ett lagringskonto."
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/02/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.openlocfilehash: 7d20469aaf2dfdd7a5f3650983b59152de837837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Skapa och distribuera din första Azure Resource Manager-mall
Den här artikeln beskriver steg för steg hur du skapar din första Azure Resource Manager-mall. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. En beskrivning av de begrepp som används i samband med distribution och hantering av Azure-lösningar finns i [Översikt över Azure Resource Manager](resource-group-overview.md). Om du har befintliga resurser och behöver en mall för dessa resurser kan du läsa [Exportera en Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).

Du behöver en JSON-redigerare för att skapa och ändra mallar. [Visual Studio Code](https://code.visualstudio.com/) är en enkel, plattformsoberoende kodredigerare med öppen källkod. Vi rekommenderar starkt att du använder Visual Studio Code för att skapa Resource Manager-mallar. I den här artikeln förutsätter vi att du använder VS Code. Om du har en annan JSON-redigerare (t.ex. Visual Studio) kan du använda den.

## <a name="prerequisites"></a>Krav

* Visual Studio Code. Om det behövs installerar du det från [https://code.visualstudio.com/](https://code.visualstudio.com/).
* En Azure-prenumeration. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-template"></a>Skapa mallen

Vi ska börja med en enkel mall som distribuerar ett lagringskonto till din prenumeration.

1. Välj **Arkiv** > **Ny fil**. 

   ![Ny fil](./media/resource-manager-create-first-template/new-file.png)

2. Kopiera och klistra in följande JSON-syntax i filen:

   ```json
   {
     "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
     "contentVersion": "1.0.0.0",
     "parameters": {
     },
     "variables": {
     },
     "resources": [
       {
         "name": "[concat('storage', uniqueString(resourceGroup().id))]",
         "type": "Microsoft.Storage/storageAccounts",
         "apiVersion": "2016-01-01",
         "sku": {
           "name": "Standard_LRS"
         },
         "kind": "Storage",
         "location": "South Central US",
         "tags": {},
         "properties": {}
       }
     ],
     "outputs": {  }
   }
   ```

   Lagringskontonamn har flera begränsningar som gör att de är svåra att ange. Namnet måste vara mellan 3 och 24 tecken långt, endast bestå av siffror och gemener samt vara unikt. I den föregående mallen används funktionen [uniqueString](resource-group-template-functions-string.md#uniquestring) för att generera ett hash-värde. För att hash-värdet ska vara lättare att förstå har prefixet *storage* lagts till. 

3. Spara den här filen som **azuredeploy.json** i en lokal mapp.

   ![Spara mallen](./media/resource-manager-create-first-template/save-template.png)

## <a name="deploy-template"></a>Distribuera mallen

Nu är det dags att distribuera den här mallen. Du använder PowerShell eller Azure CLI för att skapa en resursgrupp. Sedan distribuerar du ett lagringskonto till resursgruppen.

* Om du använder PowerShell använder du följande kommandon från mappen som innehåller mallen:

   ```powershell
   Login-AzureRmAccount
   
   New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
   New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json
   ```

* Om du använder en lokal installation av Azure CLI använder du följande kommandon från mappen som innehåller mallen:

   ```azurecli
   az login

   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file azuredeploy.json
   ```

När distributionen är klar finns ditt lagringskonto i resursgruppen.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

För Azure CLI använder du följande kommandon:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
```

För närvarande är PowerShell tillgängligt i Cloud Shell som förhandsversion. För PowerShell använder du följande kommandon:

```powershell
New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile $home\CloudDrive\templates\azuredeploy.json
```

När distributionen är klar finns ditt lagringskonto i resursgruppen.

## <a name="customize-the-template"></a>Anpassa mallen

Mallen fungerar bra, men den är inte flexibel. Den distribuerar alltid lokal redundant lagring till Södra centrala USA. Namnet är alltid *storage* följt av ett hash-värde. Lägg till parametrar till mallen om du vill använda den i andra scenarier.

Följande exempel illustrerar parameters-avsnittet med två parametrar. Med den första parametern, `storageSKU`, kan du ange typen av redundans. Den begränsar de värden som du kan använda till värden som är giltiga för ett lagringskonto. Den definierar också ett standardvärde. Den andra parametern, `storageNamePrefix`, har konfigurerats att tillåta högst 11 tecken. Den definierar ett standardvärde.

```json
"parameters": {
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
  },
  "storageNamePrefix": {
    "type": "string",
    "maxLength": 11,
    "defaultValue": "storage",
    "metadata": {
      "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
    }
  }
},
```

Lägg till en variabel med namnet `storageName` i variables-avsnittet. Den kombinerar prefixvärdet från parametrarna och ett hash-värde från funktionen [uniqueString](resource-group-template-functions-string.md#uniquestring). Den använder funktionen [toLower](resource-group-template-functions-string.md#tolower) för att konvertera alla tecken till gemener.

```json
"variables": {
  "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
},
```

Ändra resursdefinitionen om du vill använda dessa nya värden för ditt lagringskonto:

```json
"resources": [
  {
    "name": "[variables('storageName')]",
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2016-01-01",
    "sku": {
      "name": "[parameters('storageSKU')]"
    },
    "kind": "Storage",
    "location": "[resourceGroup().location]",
    "tags": {},
    "properties": {}
  }
],
```

Observera att namnet på lagringskontot använder variabeln som du lade till. SKU-namnet motsvarar värdet för parametern. Platsen är samma som för resursgruppen.

Spara filen. 

Nu ser din mall ut så här:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {}
    }
  ],
  "outputs": {  }
}
```

## <a name="redeploy-template"></a>Distribuera om mallen

Distribuera om mallen med andra värden.

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix newstore -storageSKU Standard_RAGRS
```

Om du använder Azure CLI använder du:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

Om du använder Cloud Shell laddar du upp den ändrade mallen till filresursen. Skriv över den befintliga filen. Använd sedan följande kommando:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageSKU=Standard_RAGRS storageNamePrefix=newstore
```

## <a name="use-autocomplete"></a>Använda automatisk komplettering

Hittills har du bara kopierat och klistrat in JSON-kod från den här artikeln. Men när du skapar en egen mall vill du hitta och ange egenskaper och värden som är tillgängliga för resurstypen. VS Code läser schemat för resurstypen och föreslår egenskaper och värden. Om du vill se hur automatisk komplettering fungerar går du till properties-elementet i mallen och lägger till en ny rad. Skriv ett citattecken och lägg märke till att VS Code genast föreslår namn som är tillgängliga i properties-elementet.

![Visa tillgängliga egenskaper](./media/resource-manager-create-first-template/show-properties.png)

Välj **encryption**. Skriv ett kolon (:) så föreslår VS Code att du lägger till ett nytt objekt.

![Lägg till objekt](./media/resource-manager-create-first-template/add-object.png)

Lägg till objektet genom att trycka på Tabb eller Retur.

Skriv återigen ett citattecken och notera att VS Code nu föreslår egenskaper som är tillgängliga för kryptering.

![Visa egenskaper för kryptering](./media/resource-manager-create-first-template/show-encryption-properties.png)

Välj **services** och fortsätt att lägga till värden baserat på VS Code-förslagen tills du har följande:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        }
    }
}
```

Du har aktiverat blobbkryptering för lagringskontot. VS Code har dock identifierat ett problem. Observera att encryption visas med en varning.

![Krypteringsvarning](./media/resource-manager-create-first-template/encryption-warning.png)

Hovra över den gröna linjen så ser du varningen.

![Egenskap saknas](./media/resource-manager-create-first-template/missing-property.png)

Du ser att encryption-elementet kräver en keySource-egenskap. Lägg till ett kommatecken efter services-objektet och lägg till keySource-egenskapen. VS Code föreslår **"Microsoft.Storage"** som ett giltigt värde. När du är klar ser properties-elementet ut så här:

```json
"properties": {
    "encryption":{
        "services":{
            "blob":{
              "enabled":true
            }
        },
        "keySource":"Microsoft.Storage"
    }
}
```

Så här ser den färdiga mallen ut:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
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
    },   
    "storageNamePrefix": {
      "type": "string",
      "maxLength": 11,
      "defaultValue": "storage",
      "metadata": {
        "description": "The value to use for starting the storage account name. Use only lowercase letters and numbers."
      }
    }
  },
  "variables": {
    "storageName": "[concat(toLower(parameters('storageNamePrefix')), uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "name": "[variables('storageName')]",
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2016-01-01",
      "sku": {
        "name": "[parameters('storageSKU')]"
      },
      "kind": "Storage",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "encryption":{
          "services":{
            "blob":{
              "enabled":true
            }
          },
          "keySource":"Microsoft.Storage"
        }
      }
    }
  ],
  "outputs": {}
}
```

## <a name="deploy-encrypted-storage"></a>Distribuera krypterad lagring

Distribuera mallen igen och ange ett nytt namn för lagringskontot.

Om du använder PowerShell använder du:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile azuredeploy.json -storageNamePrefix storesecure
```

Om du använder Azure CLI använder du:

```azurecli
az group deployment create --resource-group examplegroup --template-file azuredeploy.json --parameters storageNamePrefix=storesecure
```

Om du använder Cloud Shell laddar du upp den ändrade mallen till filresursen. Skriv över den befintliga filen. Använd sedan följande kommando:

```azurecli
az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json --parameters storageNamePrefix=storesecure
```

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs rensar du de resurser som du har distribuerat genom att ta bort resursgruppen.

Om du använder PowerShell använder du:

```powershell
Remove-AzureRmResourceGroup -Name examplegroup
```

Om du använder Azure CLI använder du:

```azurecli
az group delete --name examplegroup
```

## <a name="next-steps"></a>Nästa steg
* Om du vill få mer hjälp när du skapar mallar kan du installera ett VS Code-tillägg. Mer information finns i [Skapa en Azure Resource Manager-mall med hjälp av Visual Studio Code-tillägget](resource-manager-vscode-extension.md)
* Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Mer information om egenskaperna för ett lagringskonto finns i [mallreferensen för lagringskonton](/azure/templates/microsoft.storage/storageaccounts).
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).

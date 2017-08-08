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
ms.date: 07/27/2017
ms.topic: get-started-article
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 6e76ac40e9da2754de1d1aa50af3cd4e04c067fe
ms.openlocfilehash: 49086b51e2db1aebed45746306ae14b6f1feb631
ms.contentlocale: sv-se
ms.lasthandoff: 07/31/2017

---

# <a name="create-and-deploy-your-first-azure-resource-manager-template"></a>Skapa och distribuera din första Azure Resource Manager-mall
Den här artikeln beskriver steg för steg hur du skapar din första Azure Resource Manager-mall. Resource Manager-mallar är JSON-filer som definierar de resurser du behöver för att distribuera lösningen. En beskrivning av de begrepp som används i samband med distribution och hantering av Azure-lösningar finns i [Översikt över Azure Resource Manager](resource-group-overview.md). Om du har befintliga resurser och behöver en mall för dessa resurser kan du läsa [Exportera en Azure Resource Manager-mall från befintliga resurser](resource-manager-export-template.md).

Du behöver en JSON-redigerare för att skapa och ändra mallar. [Visual Studio Code](https://code.visualstudio.com/) är en enkel, plattformsoberoende kodredigerare med öppen källkod. Vi rekommenderar starkt att du använder Visual Studio Code för att skapa Resource Manager-mallar. Den här artikeln förutsätter att du använder Virtual Studio Code, men om du har en annan JSON-redigerare kan du använda den.

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

## <a name="deploy-template-from-cloud-shell"></a>Distribuera mallen från Cloud Shell

Du kan använda [Cloud Shell](../cloud-shell/overview.md) för att köra Azure CLI-kommandona och distribuera mallen. Först måste du dock läsa in mallen till filresursen för Cloud Shell. Om du inte har använt Cloud Shell tidigare läser du [Overview of Azure Cloud Shell](../cloud-shell/overview.md) (Översikt över Azure Cloud Shell), som innehåller information om hur du konfigurerar Cloud Shell.

1. Logga in på [Azure-portalen](https://portal.azure.com).   

2. Välj din Cloud Shell-resursgrupp. Namnet har formatet `cloud-shell-storage-<region>`.

   ![Välj resursgrupp](./media/resource-manager-create-first-template/select-cs-resource-group.png)

3. Välj lagringskontot för Cloud Shell.

   ![Välj lagringskonto](./media/resource-manager-create-first-template/select-storage.png)

4. Välj **Filer**.

   ![Välj filer](./media/resource-manager-create-first-template/select-files.png)

5. Välj filresursen för Cloud Shell. Namnet har formatet `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Välj filresurs](./media/resource-manager-create-first-template/select-file-share.png)

6. Välj **Lägg till katalog**.

   ![Lägg till katalog](./media/resource-manager-create-first-template/select-add-directory.png)

7. Ge den namnet **templates** och välj **OK**.

   ![Namnge katalogen](./media/resource-manager-create-first-template/name-templates.png)

8. Välj den nya katalogen.

   ![Välj katalog](./media/resource-manager-create-first-template/select-templates.png)

9. Välj **Överför**.

   ![Välj Överför](./media/resource-manager-create-first-template/select-upload.png)

10. Leta upp och överför mallen.

   ![Ladda upp filen](./media/resource-manager-create-first-template/upload-files.png)

11. Öppna kommandotolken.

   ![Öppna Cloud Shell](./media/resource-manager-create-first-template/start-cloud-shell.png)

12. Ange följande kommandon i Cloud Shell:

   ```azurecli
   az group create --name examplegroup --location "South Central US"
   az group deployment create --resource-group examplegroup --template-file clouddrive/templates/azuredeploy.json
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

När du har slutfört stegen i den här artikeln ser din mall ut så här:

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
* Mer information om strukturen i en mall finns i [Redigera Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Mer information om egenskaperna för ett lagringskonto finns i [mallreferensen för lagringskonton](/azure/templates/microsoft.storage/storageaccounts).
* Om du vill visa kompletta mallar för många olika typer av lösningar kan du se [Azure-snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).


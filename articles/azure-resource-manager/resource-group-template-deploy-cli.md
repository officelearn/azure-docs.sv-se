---
title: Distribuera resurser med Azure CLI och en mall | Microsoft Docs
description: Använda Azure Resource Manager och Azure CLI för att distribuera en resurser till Azure. Resurserna definieras i en Resource Manager-mall.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: 510cba0c2e27ab56ea26a476258fd7480b80e0d2
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39420411"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuera resurser med Resource Manager-mallar och Azure CLI

Den här artikeln förklarar hur du använder Azure CLI med Resource Manager-mallar för att distribuera dina resurser till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure-lösningar finns i [översikt över Azure Resource Manager](resource-group-overview.md).  

Resource Manager-mallen som du distribuerar kan antingen vara en lokal fil på din dator eller en extern fil som finns i en databas som GitHub. Mallen som du distribuerar i den här artikeln är tillgänglig i den [exempelmallen](#sample-template) avsnittet eller som en [storage-konto mallen i GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Om du inte har Azure CLI installerat kan du använda den [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Distribuera lokala mall

När du distribuerar resurser till Azure måste du:

1. Logga in på ditt Azure-konto
2. Skapa en resursgrupp som fungerar som behållare för distribuerade resurser. Namnet på resursgruppen får bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Det får inte avslutas med en punkt.
3. Distribuera till resursgrupp mallen som definierar resurser för att skapa

En mall kan innehålla parametrar som gör att du kan anpassa distributionen. Du kan till exempel ange värden som är skräddarsydda för en viss miljö (till exempel utveckling, testning och produktion). Exempelmallen definierar en parameter för SKU för lagringskontot. 

I följande exempel skapas en resursgrupp och distribuerar en mall från den lokala datorn:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När den är klar visas ett meddelande som innehåller resultatet:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Distribuera externa mall

Istället för att lagra Resource Manager-mallar på den lokala datorn, kanske du föredrar att lagra dem i en extern plats. Du kan lagra mallar i ett källkontrollscentrallager (till exempel GitHub). Eller du kan lagra dem i ett Azure storage-konto för delad åtkomst i din organisation.

Om du vill distribuera en mall för externa, använda den **mall-uri** parametern. Använd URI: N i det här exemplet för att distribuera exempelmallen från GitHub.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

I föregående exempel kräver en offentligt tillgänglig URI för den mall som passar de flesta fall eftersom mallen inte får innehålla känsliga data. Om du vill ange känsliga data (till exempel ett administratörslösenord) kan du skicka det värdet som en säker parameter. Om du inte vill att mallen för att vara allmänt tillgänglig, kan du dock skydda den genom att lagra det i en privat lagringsbehållare. Information om hur du distribuerar en mall som kräver en signatur (SAS) token för delad åtkomst finns i [distribuera privat mall med SAS-token](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

I Cloud Shell använder du följande kommandon:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Distribuera till fler än en resursgrupp eller prenumeration

Normalt kan distribuera du alla resurser i mallen till en enda resursgrupp. Det finns dock scenarier där du vill distribuera en uppsättning resurser tillsammans, men placera dem i olika resursgrupper eller prenumerationer. Du kan distribuera till endast fem resursgrupper i samma distribution. Mer information finns i [distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](resource-manager-cross-resource-group-deployment.md).

## <a name="parameter-files"></a>Parameterfiler

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara enklare att använda en JSON-fil som innehåller parametervärdena. Parameterfilen måste vara i följande format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Observera att parameters-avsnittet innehåller ett parameternamn som matchar den parameter som definierats i mallen (storageAccountType). Parameterfilen innehåller ett värde för parametern. Det här värdet skickas automatiskt till mallen under distributionen. Du kan skapa flera parameterfiler för olika distributionsscenarier och sedan skicka in lämpliga parameterfilen. 

Kopiera i föregående exempel och spara det som en fil med namnet `storage.parameters.json`.

Om du vill skicka en lokal parameterfil använda `@` att ange en lokal fil med namnet storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Testa en för malldistribution

Testa din mall- och parameterfilerna värden utan att faktiskt distribuera resurser med [az group deployment Validera](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Om inga fel identifieras måste returnerar kommandot information om test-distribution. Observera att särskilt den **fel** värdet är null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Om ett fel upptäcks kan returnerar kommandot ett felmeddelande. Exempel: försöker skicka ett felaktigt värde för lagringskontots SKU, får du följande felmeddelande:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Om mallen innehåller ett syntaxfel, returnerar kommandot ett felmeddelande om att det inte kunde parsa mallen. Meddelandet Anger radnumret och positionen för parsningsfel.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Om du vill använda fullständig läge, Använd den `mode` parameter:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>Exempelmall

Följande mall används i exemplen i den här artikeln. Kopiera och spara den som en fil med namnet storage.json. Information om hur du skapar den här mallen finns i [skapa din första Azure Resource Manager-mall](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Nästa steg
* Exemplen i den här artikeln distribuera resurser till en resursgrupp i din Standardprenumeration. Om du vill använda en annan prenumeration, se [hantera flera Azure-prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli).
* En fullständig exempelskript som distribuerar en mall finns i [distributionsskriptet för Resource Manager-mall](resource-manager-samples-cli-deploy.md).
* Information om hur du definierar parametrar i mallen finns i [förstå strukturen och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distributionsfel med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](resource-manager-cli-sas-token.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).

---
title: Distribuera resurser med Azure CLI och mall | Microsoft Docs
description: Använd Azure Resource Manager och Azure CLI för att distribuera en resurser till Azure. Resurserna definieras i en Resource Manager-mall.
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
ms.openlocfilehash: ee8ce8453c24d19b3912d0f6cc506b3d3e72548f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34603012"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuera resurser med Resource Manager-mallar och Azure CLI

Den här artikeln förklarar hur du använder Azure CLI 2.0 med Resource Manager-mallar för att distribuera resurserna till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure lösningar finns [översikt över Azure Resource Manager](resource-group-overview.md).  

Resource Manager-mallen som du distribuerar kan antingen vara en lokal fil på din dator eller en extern fil som finns i en databas som GitHub. Den mall som du distribuerar i den här artikeln är tillgänglig i den [exempelmall](#sample-template) avsnitt, eller som en [lagring mall i GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Om du inte har Azure CLI är installerad kan du använda den [moln Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Distribuera lokala mall

När du distribuerar resurser till Azure måste du:

1. Logga in på ditt Azure-konto
2. Skapa en resursgrupp som fungerar som behållare för distribuerade resurser. Namnet på resursgruppen får bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Den kan inte sluta med en punkt.
3. Distribuera till resursgrupp mallen som definierar resurser för att skapa

En mall kan innehålla parametrar som gör att du kan anpassa distributionen. Exempelvis kan du ange värden som är anpassade för en viss miljö (t.ex dev, test- och). Mallen exempel definierar en parameter för lagringskontot SKU. 

I följande exempel skapar en resursgrupp och distribuerar en mall från den lokala datorn:

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

Du kanske föredrar att lagra dem på en extern plats istället för att lagra Resource Manager-mallar på den lokala datorn. Du kan lagra mallar i en källkontroll (till exempel GitHub). Eller, du kan lagra dem i ett Azure storage-konto för delad åtkomst i din organisation.

Distribuera en extern mall att använda den **mall-uri** parameter. Använd URI: N i exemplet för att distribuera exempelmall från GitHub.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

I exemplet ovan kräver en offentligt tillgänglig URI för mallen, som fungerar i de flesta scenarier eftersom mallen inte innehålla känsliga data. Om du behöver ange känsliga data (till exempel en adminlösenord) skicka det värdet som en säker parameter. Om du inte vill att mallen ska vara offentligt tillgänglig, kan du dock skydda den genom att lagra det i en behållare för privat lagring. Information om hur du distribuerar en mall som kräver en signatur (SAS) token för delad åtkomst finns i [distribuera privata mallar med SAS-token](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Använd följande kommandon i molnet-gränssnittet:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Distribuera till mer än en resursgrupp eller prenumeration

Normalt distribuerar du alla resurser i mallen som en enskild resursgrupp. Det finns emellertid scenarier där du vill distribuera en uppsättning resurser tillsammans men placera dem i olika resursgrupper eller prenumerationer. Du kan distribuera till bara fem resursgrupper i en enda distribution. Mer information finns i [distribuera Azure-resurser till mer än en prenumeration eller resursgrupp](resource-manager-cross-resource-group-deployment.md).

## <a name="parameter-files"></a>Parametern-filer

I stället för att skicka parametrar som infogade värden i skriptet kan det vara lättare att använda en JSON-fil som innehåller parametrarnas värden. Parameterfilen måste vara i följande format:

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

Observera att avsnittet Parametrar innehåller ett parameternamn som matchar den parameter som definierats i mallen (storageAccountType). Parameterfilen innehåller ett värde för parametern. Det här värdet skickas automatiskt till mallen under distributionen. Du kan skapa flera parametern filer för olika distributionsscenarier och sedan ange lämpliga parameterfil. 

Kopiera föregående exempel och spara den som en fil med namnet `storage.parameters.json`.

Om du vill lägga till en lokal parameterfil, Använd `@` att ange en lokal fil med namnet storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Testa en för malldistribution

Testa din mall och parametern-värden utan att faktiskt distribuera resurser med [az distribution Validera](/cli/azure/group/deployment#az_group_deployment_validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Om inga fel identifieras returnerar kommandot information om testdistributionen. Observera att särskilt den **fel** värdet är null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Om ett fel upptäcks returnerar kommandot ett felmeddelande. Försök att skicka ett felaktigt värde för lagringskontot SKU, returnerar till exempel följande fel:

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

Om mallen har ett syntaxfel, returnerar kommandot ett felmeddelande om att det inte kunde tolka mallen. Meddelandet Anger radnumret och positionen för parsningsfel.

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

Om du vill använda fullständig läge i `mode` parameter:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>Exempelmall

Följande mall används i exemplen i den här artikeln. Kopiera och spara den som en fil med namnet storage.json. Information om hur du skapar den här mallen finns [skapa din första Azure Resource Manager-mallen](resource-manager-create-first-template.md).  

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
* Exemplen i den här artikeln distribuera resurser i en resursgrupp i din standard-prenumeration. Om du vill använda en annan prenumeration finns [hantera Azure-prenumerationer](/cli/azure/manage-azure-subscriptions-azure-cli).
* En fullständig exempelskript som distribuerar en mall finns i [distributionsskriptet för Resource Manager-mallen](resource-manager-samples-cli-deploy.md).
* Information om hur du definierar parametrar i mallen finns [förstå struktur och syntaxen för Azure Resource Manager-mallar](resource-group-authoring-templates.md).
* Tips om hur du löser vanliga distributionsfel finns [felsöka vanliga Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns [distribuera privata mallar med SAS-token](resource-manager-cli-sas-token.md).
* Vägledning för hur företag kan använda resurshanteraren för att effektivt hantera prenumerationer finns i [Azure enterprise scaffold - förebyggande prenumerationsåtgärder](/azure/architecture/cloud-adoption-guide/subscription-governance).

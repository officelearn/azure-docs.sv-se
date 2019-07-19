---
title: Distribuera resurser med Azure CLI och mall | Microsoft Docs
description: Använd Azure Resource Manager och Azure CLI för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 07/12/2019
ms.author: tomfitz
ms.openlocfilehash: 93b1b16776bac6cb24996d6fa08a547318802f32
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853838"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Distribuera resurser med Resource Manager-mallar och Azure CLI

Den här artikeln förklarar hur du använder Azure CLI med Resource Manager-mallar för att distribuera dina resurser till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure-lösningar kan du läsa [Azure Resource Manager översikt](resource-group-overview.md).  

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Om du inte har Azure CLI installerat kan du använda [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Distributions omfång

Du kan rikta din distribution till antingen en Azure-prenumeration eller en resurs grupp i en prenumeration. I de flesta fall riktar du distribution till en resurs grupp. Använd prenumerations distributioner för att tillämpa principer och roll tilldelningar i prenumerationen. Du kan också använda prenumerations distributioner för att skapa en resurs grupp och distribuera resurser till den. Beroende på distributionens omfattning använder du olika kommandon.

Om du vill distribuera till en **resurs grupp**använder du [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

Om du vill distribuera till en **prenumeration**använder du [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

För närvarande stöds endast distributioner av hanterings grupper via REST API. Se [distribuera resurser med Resource Manager-mallar och Resource Manager-REST API](resource-group-template-deploy-rest.md).

I exemplen i den här artikeln används resurs grupps distributioner. Mer information om distribution av prenumerationer finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

## <a name="deploy-local-template"></a>Distribuera lokal mall

När du distribuerar resurser till Azure:

1. Logga in på ditt Azure-konto
2. Skapa en resurs grupp som fungerar som behållare för de distribuerade resurserna. Namnet på resurs gruppen får bara innehålla alfanumeriska tecken, punkter, under streck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Det får inte sluta med en punkt.
3. Distribuera till resurs gruppen med mallen som definierar vilka resurser som ska skapas

En mall kan innehålla parametrar som gör att du kan anpassa distributionen. Du kan till exempel ange värden som är anpassade för en viss miljö (till exempel utveckling, testning och produktion). Exempel mal len definierar en parameter för lagrings kontots SKU. 

I följande exempel skapas en resurs grupp och en mall distribueras från den lokala datorn:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När det är klart visas ett meddelande som innehåller resultatet:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuera fjärran sluten mall

I stället för att lagra Resource Manager-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar i ett lager för käll kontroll (till exempel GitHub). Du kan också lagra dem i ett Azure Storage-konto för delad åtkomst i din organisation.

Om du vill distribuera en extern mall använder du parametern **mall-URI** . Använd URI i exemplet för att distribuera exempel mal len från GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

I föregående exempel krävs en offentligt tillgänglig URI för mallen som fungerar i de flesta fall eftersom din mall inte ska innehålla känsliga data. Om du behöver ange känsliga data (som ett administratörs lösen ord) skickar du det värdet som en säker parameter. Men om du inte vill att din mall ska vara offentligt tillgänglig kan du skydda den genom att lagra den i en privat lagrings behållare. Information om hur du distribuerar en mall som kräver en SAS-token (signatur för delad åtkomst) finns i [distribuera privat mall med SAS](resource-manager-cli-sas-token.md)-token.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Använd följande kommandon i Cloud Shell:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="redeploy-when-deployment-fails"></a>Distribuera om när distributionen Miss lyckas

Den här funktionen kallas även *återgång vid fel*. När en distribution Miss lyckas kan du automatiskt distribuera om en tidigare distribuerad distribution från din distributions historik. Om du vill ange omdistribution använder `--rollback-on-error` du parametern i distributions kommandot. Den här funktionen är användbar om du har ett känt bra tillstånd för distribution av infrastrukturen och vill återställa till det här läget. Det finns ett antal varningar och begränsningar:

- Omdistributionen körs exakt eftersom den kördes tidigare med samma parametrar. Du kan inte ändra parametrarna.
- Den tidigare distributionen körs med slutfört [läge](./deployment-modes.md#complete-mode). Alla resurser som inte ingår i den tidigare distributionen tas bort och alla resurspooler anges till sitt tidigare tillstånd. Se till att du förstår [distributions lägena](./deployment-modes.md)fullständigt.
- Omdistributionen påverkar endast resurserna, alla data ändringar påverkas inte.
- Den här funktionen stöds bara för distributioner av resurs grupper, inte på prenumerations nivå distributioner. Mer information om distribution av prenumerations nivåer finns i [skapa resurs grupper och resurser på prenumerations nivå](./deploy-to-subscription.md).

Om du vill använda det här alternativet måste distributionerna ha unika namn så att de kan identifieras i historiken. Om du inte har unika namn kan den aktuella misslyckade distributionen skriva över den tidigare lyckade distributionen i historiken. Du kan bara använda det här alternativet med distributioner på rotnivå. Distributioner från en kapslad mall är inte tillgängliga för omdistribution.

Om du vill distribuera om den senaste lyckade distributionen lägger `--rollback-on-error` du till parametern som en flagga.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Om du vill distribuera om en speciell distribution använder `--rollback-on-error` du parametern och anger namnet på distributionen.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

Den angivna distributionen måste ha slutförts.

## <a name="parameters"></a>Parametrar

Om du vill skicka parameter värden kan du använda antingen infogade parametrar eller en parameter fil. I föregående exempel i den här artikeln visas infogade parametrar.

### <a name="inline-parameters"></a>Infogade parametrar

Ange värdena i `parameters`om du vill skicka in infogade parametrar. Om du till exempel vill skicka en sträng och matris till en mall är ett bash-gränssnitt använder du:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Om du använder Azure CLI med kommando tolken i Windows (CMD) eller PowerShell skickar du matrisen i formatet: `exampleArray="['value1','value2']"`.

Du kan också hämta innehållet i filen och ange innehållet som en infogad parameter.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Att hämta ett parameter värde från en fil är användbart när du behöver ange konfigurations värden. Du kan till exempel ange [värden för Cloud-Init för en virtuell Linux-dator](../virtual-machines/linux/using-cloud-init.md).

Formatet arrayContent. JSON är:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Parameter-filer

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara lättare att använda en JSON-fil som innehåller parameter värden. Parameter filen måste vara en lokal fil. Externa parameter-filer stöds inte med Azure CLI.

Parameter filen måste ha följande format:

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

Observera att avsnittet Parameters innehåller ett parameter namn som matchar den parameter som definierats i din mall (storageAccountType). Parameter filen innehåller ett värde för parametern. Det här värdet skickas automatiskt till mallen under distributionen. Du kan skapa mer än en parameter fil och sedan skicka in rätt parameter fil för scenariot. 

Kopiera föregående exempel och spara den som en fil med namnet `storage.parameters.json`.

Om du vill skicka en lokal parameter fil `@` använder du för att ange en lokal fil med namnet Storage. Parameters. JSON.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>Parameter prioritet

Du kan använda infogade parametrar och en lokal parameter fil i samma distributions åtgärd. Du kan till exempel ange vissa värden i den lokala parameter filen och lägga till andra värden i den under distributionen. Om du anger värden för en parameter i både den lokala parameter filen och den infogade värdet, prioriteras det infogade värdet.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```

## <a name="test-a-template-deployment"></a>Testa en mall distribution

Om du vill testa mallen och parameter värden utan att behöva distribuera några resurser använder du [AZ Group Deployment validate](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Om inga fel upptäcks returnerar kommandot information om test distributionen. Observera särskilt att **felvärdet** är null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Om ett fel upptäcks returnerar kommandot ett fel meddelande. Om du till exempel skickar ett felaktigt värde för lagrings kontots SKU returneras följande fel:

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

Om din mall har ett syntaxfel returnerar kommandot ett fel som anger att mallen inte kunde parsas. Meddelandet anger rad numret och positionen för tolknings felet.

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

## <a name="next-steps"></a>Nästa steg

- I exemplen i den här artikeln distribueras resurser till en resurs grupp i din standard prenumeration. Om du vill använda en annan prenumeration läser du [Hantera flera Azure](/cli/azure/manage-azure-subscriptions-azure-cli)-prenumerationer.
- Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](resource-group-authoring-templates.md).
- Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](resource-manager-common-deployment-errors.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS](resource-manager-cli-sas-token.md)-token.
- För att på ett säkert sätt distribuera tjänsten till mer än en region, se [Azure Deployment Manager](deployment-manager-overview.md).

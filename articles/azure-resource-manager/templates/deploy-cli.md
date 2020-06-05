---
title: Distribuera resurser med Azure CLI och mall
description: Använd Azure Resource Manager och Azure CLI för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 06/04/2020
ms.openlocfilehash: a2a1c1fe63d0a841f57407ed5402d7ddca3fcea4
ms.sourcegitcommit: b55d1d1e336c1bcd1c1a71695b2fd0ca62f9d625
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2020
ms.locfileid: "84432078"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Distribuera resurser med ARM-mallar och Azure CLI

Den här artikeln förklarar hur du använder Azure CLI med Azure Resource Manager ARM-mallar för att distribuera dina resurser till Azure. Om du inte är bekant med principerna för att distribuera och hantera dina Azure-lösningar kan du läsa [Översikt över mall-distribution](overview.md).

Distributions kommandona ändrades i Azure CLI-version 2.2.0. Exemplen i den här artikeln kräver Azure CLI version 2.2.0 eller senare.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Om du inte har Azure CLI installerat kan du använda [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Distributions omfång

Du kan rikta distributionen till en resurs grupp, prenumeration, hanterings grupp eller klient organisation. I de flesta fall riktar du distribution till en resurs grupp. Använd prenumerationer, hanterings grupper eller klient distributioner för att tillämpa principer och roll tilldelningar i ett större omfång. När du distribuerar till en prenumeration kan du skapa en resurs grupp och distribuera resurser till den.

Beroende på distributionens omfattning använder du olika kommandon.

* Om du vill distribuera till en **resurs grupp**använder du [AZ distributions grupp skapa](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* Om du vill distribuera till en **prenumeration**använder du [AZ Deployment sub Create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Mer information om distributioner på prenumerations nivå finns i [skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md).

* Om du vill distribuera till en **hanterings grupp**använder du [AZ Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Mer information om distributioner på hanterings grupp nivå finns i [Skapa resurser på hanterings grupps nivå](deploy-to-management-group.md).

* Använd [AZ Deployment Tenant Create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)för att distribuera till en **klient organisation**:

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Mer information om distributioner på klient nivå finns i [Skapa resurser på klient nivå](deploy-to-tenant.md).

I exemplen i den här artikeln används resurs grupps distributioner.

## <a name="deploy-local-template"></a>Distribuera lokal mall

När du distribuerar resurser till Azure:

1. Logga in på ditt Azure-konto
2. Skapa en resurs grupp som fungerar som behållare för de distribuerade resurserna. Namnet på resurs gruppen får bara innehålla alfanumeriska tecken, punkter, under streck, bindestreck och parenteser. Det kan vara upp till 90 tecken. Det får inte sluta med en punkt.
3. Distribuera till resurs gruppen med mallen som definierar vilka resurser som ska skapas

En mall kan innehålla parametrar som gör att du kan anpassa distributionen. Du kan till exempel ange värden som är anpassade för en viss miljö (till exempel utveckling, testning och produktion). Exempel mal len definierar en parameter för lagrings kontots SKU.

I följande exempel skapas en resurs grupp och en mall distribueras från den lokala datorn:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När det är klart visas ett meddelande som innehåller resultatet:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuera fjärran sluten mall

I stället för att lagra ARM-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar i ett lager för käll kontroll (till exempel GitHub). Du kan också lagra dem i ett Azure Storage-konto för delad åtkomst i din organisation.

Om du vill distribuera en extern mall använder du parametern **mall-URI** . Använd URI i exemplet för att distribuera exempel mal len från GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

I föregående exempel krävs en offentligt tillgänglig URI för mallen som fungerar i de flesta fall eftersom din mall inte ska innehålla känsliga data. Om du behöver ange känsliga data (som ett administratörs lösen ord) skickar du det värdet som en säker parameter. Men om du inte vill att din mall ska vara offentligt tillgänglig kan du skydda den genom att lagra den i en privat lagrings behållare. Information om hur du distribuerar en mall som kräver en SAS-token (signatur för delad åtkomst) finns i [distribuera privat mall med SAS-token](secure-template-with-sas-token.md).

## <a name="preview-changes"></a>Förhandsgranska ändringar

Innan du distribuerar din mall kan du förhandsgranska de ändringar som mallen kommer att göra i din miljö. Använd [åtgärden konsekvens](template-deploy-what-if.md) för att kontrol lera att mallen gör de ändringar som du förväntar dig. Vad händer om även validerar mallen för fel.

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Använd följande kommandon i Cloud Shell:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametrar

Om du vill skicka parameter värden kan du använda antingen infogade parametrar eller en parameter fil.

### <a name="inline-parameters"></a>Infogade parametrar

Ange värdena i om du vill skicka in infogade parametrar `parameters` . Om du till exempel vill skicka en sträng och matris till en mall är ett bash-gränssnitt använder du:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Om du använder Azure CLI med kommando tolken i Windows (CMD) eller PowerShell skickar du matrisen i formatet: `exampleArray="['value1','value2']"` .

Du kan också hämta innehållet i filen och ange innehållet som en infogad parameter.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Att hämta ett parameter värde från en fil är användbart när du behöver ange konfigurations värden. Du kan till exempel ange [värden för Cloud-Init för en virtuell Linux-dator](../../virtual-machines/linux/using-cloud-init.md).

Formatet arrayContent. JSON är:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Parameter-filer

I stället för att skicka parametrar som infogade värden i skriptet, kan det vara lättare att använda en JSON-fil som innehåller parameter värden. Parameter filen måste vara en lokal fil. Externa parameter-filer stöds inte med Azure CLI.

Mer information om parameter filen finns i [create Resource Manager parameter File](parameter-files.md).

Om du vill skicka en lokal parameter fil använder `@` du för att ange en lokal fil med namnet Storage. Parameters. JSON.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Hantera utökat JSON-format

Om du vill distribuera en mall med strängar med flera rader eller kommentarer med hjälp av Azure CLI med version 2.3.0 eller äldre måste du använda `--handle-extended-json-format` växeln.  Exempel:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="next-steps"></a>Nästa steg

- Om du vill återställa till en lyckad distribution när du får ett fel, se [återställa vid fel till lyckad distribution](rollback-on-error.md).
- Information om hur du hanterar resurser som finns i resurs gruppen men som inte har definierats i mallen finns i [Azure Resource Manager distributions lägen](deployment-modes.md).
- Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
- Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](secure-template-with-sas-token.md).

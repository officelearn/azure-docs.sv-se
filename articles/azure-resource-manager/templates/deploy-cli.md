---
title: Distribuera resurser med Azure CLI och mall
description: Använd Azure Resource Manager och Azure CLI för att distribuera resurser till Azure. Resurserna definieras i en Resource Manager-mall.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685691"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Distribuera resurser med ARM-mallar och Azure CLI

I den här artikeln beskrivs hur du använder Azure CLI med ARM-mallar (Azure Resource Manager) för att distribuera dina resurser till Azure. Om du inte är bekant med begreppen att distribuera och hantera dina Azure-lösningar läser du [översikt över malldistribution](overview.md).

Distributionskommandona har ändrats i Azure CLI version 2.2.0. Exemplen i den här artikeln kräver Azure CLI version 2.2.0 eller senare.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Om du inte har Azure CLI installerat kan du använda [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Distributionsomfattning

Du kan rikta distributionen till en resursgrupp, prenumeration, hanteringsgrupp eller klient. I de flesta fall riktar du distributionen till en resursgrupp. Om du vill använda principer och rolltilldelningar i ett större omfång använder du prenumerations-, hanteringsgrupp- eller klientdistributioner. När du distribuerar till en prenumeration kan du skapa en resursgrupp och distribuera resurser till den.

Beroende på distributionens omfattning använder du olika kommandon.

Om du vill distribuera till en **resursgrupp**använder du [az deployment group create:](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create)

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Om du vill distribuera till en **prenumeration**använder du [az deployment sub create:](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create)

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Mer information om distributioner på prenumerationsnivå finns i [Skapa resursgrupper och resurser på prenumerationsnivå](deploy-to-subscription.md).

Om du vill distribuera till en **hanteringsgrupp**använder du [az deployment mg create:](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create)

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Mer information om distributioner på hanteringsgruppsnivå finns i [Skapa resurser på hanteringsgruppsnivå](deploy-to-management-group.md).

Om du vill distribuera till en **klient**använder du [az deployment-klienten skapa:](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create)

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Mer information om distributioner på klientnivå finns i [Skapa resurser på klientnivå](deploy-to-tenant.md).

Exemplen i den här artikeln använder resursgruppsdistributioner.

## <a name="deploy-local-template"></a>Distribuera lokal mall

När du distribuerar resurser till Azure kan du:

1. Logga in på ditt Azure-konto
2. Skapa en resursgrupp som fungerar som behållare för de distribuerade resurserna. Namnet på resursgruppen kan bara innehålla alfanumeriska tecken, punkter, understreck, bindestreck och parentes. Det kan vara upp till 90 tecken. Det kan inte sluta i en period.
3. Distribuera den mall som definierar de resurser som ska skapas till resursgruppen till resursgruppen

En mall kan innehålla parametrar som gör att du kan anpassa distributionen. Du kan till exempel ange värden som är anpassade för en viss miljö (till exempel utveckling, test och produktion). Exempelmallen definierar en parameter för lagringskontot SKU.

I följande exempel skapas en resursgrupp och en mall distribueras från den lokala datorn:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Det kan ta några minuter att slutföra distributionen. När den är klar visas ett meddelande som innehåller resultatet:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Distribuera fjärrmall

I stället för att lagra ARM-mallar på den lokala datorn kanske du föredrar att lagra dem på en extern plats. Du kan lagra mallar i en källkontrolldatabas (till exempel GitHub). Du kan också lagra dem i ett Azure-lagringskonto för delad åtkomst i din organisation.

Om du vill distribuera en extern mall använder du **parametern mall-uri.** Använd URI i exemplet för att distribuera exempelmallen från GitHub.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

I föregående exempel krävs en allmänt tillgänglig URI för mallen, som fungerar för de flesta scenarier eftersom mallen inte ska innehålla känsliga data. Om du behöver ange känsliga data (till exempel ett administratörslösenord) skickar du det värdet som en säker parameter. Men om du inte vill att mallen ska vara allmänt tillgänglig kan du skydda den genom att lagra den i en privat lagringsbehållare. Information om hur du distribuerar en mall som kräver en SAS-token (Shared Access Signature) finns [i Distribuera en privat mall med SAS-token](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

Använd följande kommandon i Cloud Shell:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametrar

Om du vill skicka parametervärden kan du använda antingen infogade parametrar eller en parameterfil.

### <a name="inline-parameters"></a>Infogade parametrar

Om du vill skicka infogade `parameters`parametrar anger du värdena i . Om du till exempel vill skicka en sträng och en matris till en mall är ett Bash-skal använder du:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Om du använder Azure CLI med Windows Command Prompt (CMD) eller PowerShell skickar du matrisen i formatet: `exampleArray="['value1','value2']"`.

Du kan också hämta innehållet i filen och ange innehållet som en infogad parameter.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Det är praktiskt att hämta ett parametervärde från en fil när du behöver ange konfigurationsvärden. Du kan till exempel ange [molninitvärden för en virtuell Linux-dator](../../virtual-machines/linux/using-cloud-init.md).

ArrayContent.json-formatet är:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Parameterfiler

I stället för att skicka parametrar som infogade värden i skriptet kan det vara enklare att använda en JSON-fil som innehåller parametervärdena. Parameterfilen måste vara en lokal fil. Externa parameterfiler stöds inte med Azure CLI.

Mer information om parameterfilen finns i [Skapa Resource Manager-parameterfil](parameter-files.md).

Om du vill skicka `@` en lokal parameterfil använder du för att ange en lokal fil med namnet storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Hantera utökat JSON-format

Om du vill distribuera en mall med strängar eller kommentarer med flera rader med Azure CLI `--handle-extended-json-format` med version 2.3.0 eller äldre måste du använda växeln.  Ett exempel:

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

## <a name="test-a-template-deployment"></a>Testa en malldistribution

Om du vill testa mall- och parametervärdena utan att distribuera några resurser använder du [validera den az-distributionsgruppen](/cli/azure/group/deployment).

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Om inga fel upptäcks returnerar kommandot information om testdistributionen. Observera särskilt att **felvärdet** är null.

```output
{
  "error": null,
  "properties": {
      ...
```

Om ett fel upptäcks returneras ett felmeddelande i kommandot. Om du till exempel skickar ett felaktigt värde för lagringskontot SKU returneras följande fel:

```output
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

Om mallen har ett syntaxfel returnerar kommandot ett fel som anger att det inte kunde tolka mallen. Meddelandet anger radnumret och placeringen av tolkningsfelet.

```output
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

- Om du vill återställa till en lyckad distribution när du får ett fel finns [i Återställning vid fel till en lyckad distribution](rollback-on-error.md).
- Information om hur resurser som finns i resursgruppen men som inte har definierats i mallen ska [hanteras](deployment-modes.md).
- Information om hur du definierar parametrar i mallen finns i [Förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
- Tips om hur du löser vanliga distributionsfel finns i [Felsöka vanliga Azure-distributionsfel med Azure Resource Manager](common-deployment-errors.md).
- Information om hur du distribuerar en mall som kräver en SAS-token finns i [Distribuera privat mall med SAS-token](secure-template-with-sas-token.md).
- Information om hur du distribuerar tjänsten till mer än en region på ett säkert sätt finns i [Azure Deployment Manager](deployment-manager-overview.md).

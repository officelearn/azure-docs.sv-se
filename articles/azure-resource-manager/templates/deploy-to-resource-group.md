---
title: Distribuera resurser till resurs grupper
description: Beskriver hur du distribuerar resurser i en Azure Resource Manager mall. Det visar hur du riktar in mer än en resurs grupp.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681671"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Resurs grupps distributioner med ARM-mallar

Den här artikeln beskriver hur du omfångerar distributionen till en resurs grupp. Du använder en Azure Resource Manager-mall (ARM-mall) för distributionen. Artikeln visar också hur du kan utöka omfattningen bortom resurs gruppen i distributions åtgärden.

## <a name="supported-resources"></a>Resurser som stöds

De flesta resurser kan distribueras till en resurs grupp. En lista över tillgängliga resurser finns i [referens för ARM-mallar](/azure/templates).

## <a name="schema"></a>Schema

För mallar använder du följande schema:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

För parameter-filer använder du:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Distributions kommandon

Använd resurs grupps distributions kommandon för att distribuera till en resurs grupp.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Använd [AZ distributions grupp skapa](/cli/azure/deployment/group#az_deployment_group_create)för Azure CLI. I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

För kommandot PowerShell-distribution använder du [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). I följande exempel distribueras en mall för att skapa en resurs grupp:

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Mer detaljerad information om distributions kommandon och alternativ för att distribuera ARM-mallar finns i:

* [Distribuera resurser med ARM-mallar och Azure Portal](deploy-portal.md)
* [Distribuera resurser med ARM-mallar och Azure CLI](deploy-cli.md)
* [Distribuera resurser med ARM-mallar och Azure PowerShell](deploy-powershell.md)
* [Distribuera resurser med ARM-mallar och Azure Resource Manager REST API](deploy-rest.md)
* [Använd en distributions knapp för att distribuera mallar från GitHub-lagringsplatsen](deploy-to-azure-button.md)
* [Distribuera ARM-mallar från Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Distributions omfång

När du distribuerar till en resurs grupp kan du distribuera resurser för att:

* mål resurs gruppen från åtgärden
* andra resurs grupper i samma prenumeration eller andra prenumerationer
* [tilläggs resurser](scope-extension-resources.md) kan tillämpas på resurser

Användaren som distribuerar mallen måste ha åtkomst till det angivna omfånget.

I det här avsnittet visas hur du anger olika omfång. Du kan kombinera dessa olika omfång i en enda mall.

### <a name="scope-to-target-resource-group"></a>Omfång till mål resurs grupp

Om du vill distribuera resurser till mål resursen lägger du till resurserna i avsnittet resurser i mallen.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>Omfånget till resurs gruppen i samma prenumeration

Om du vill distribuera resurser till en annan resurs grupp i samma prenumeration lägger du till en kapslad distribution och inkluderar `resourceGroup` egenskapen. Om du inte anger prenumerations-ID eller resurs grupp används prenumerationen och resurs gruppen från den överordnade mallen. Alla resurs grupper måste finnas innan du kör distributionen.

I följande exempel riktar den kapslade distributionen till en resurs grupp med namnet `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>Omfånget till resurs gruppen i en annan prenumeration

Om du vill distribuera resurser till en resurs grupp i en annan prenumeration lägger du till en kapslad distribution och inkluderar- `subscriptionId` och- `resourceGroup` egenskaperna. I följande exempel riktar den kapslade distributionen till en resurs grupp med namnet `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>Kors resurs grupper

Du kan distribuera till fler än en resurs grupp i en enda ARM-mall. Använd en [kapslad eller länkad mall](linked-templates.md)om du vill rikta en resurs grupp från en annan resurs grupp än den för den överordnade mallen. I resurs typen distribution anger du värden för det prenumerations-ID och den resurs grupp som du vill att den kapslade mallen ska distribuera till. Resurs grupperna kan finnas i olika prenumerationer.

> [!NOTE]
> Du kan distribuera till **800 resurs grupper** i en enda distribution. Den här begränsningen innebär vanligt vis att du kan distribuera till en resurs grupp som angetts för den överordnade mallen och upp till 799 resurs grupper i kapslade eller länkade distributioner. Men om den överordnade mallen bara innehåller kapslade eller länkade mallar och inte själv distribuerar några resurser, kan du inkludera upp till 800 resurs grupper i kapslade eller länkade distributioner.

I följande exempel distribueras två lagrings konton. Det första lagrings kontot distribueras till den resurs grupp som anges i distributions åtgärden. Det andra lagrings kontot distribueras till resurs gruppen som anges i `secondResourceGroup` parametrarna och `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Om du anger `resourceGroup` namnet på en resurs grupp som inte finns, Miss lyckas distributionen.

Testa föregående mall och se resultatet med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration** använder du:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

Om du vill distribuera två lagrings konton till **två prenumerationer** använder du:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration** använder du:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

Om du vill distribuera två lagrings konton till **två prenumerationer** använder du:

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="next-steps"></a>Nästa steg

* Ett exempel på hur du distribuerar inställningar för arbets ytan för Azure Security Center finns [deployASCwithWorkspaceSettings.jspå](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).

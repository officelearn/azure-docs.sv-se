---
title: Distribuera resurser mellan prenumerationer & resurs grupp
description: Visar hur du riktar in mer än en Azure-prenumeration och resurs grupp under distributionen.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 34de1d9df53d61d849ffbb81a57b468020bc3b65
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057389"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Distribuera Azure-resurser över prenumerationer eller resurs grupper

Med Resource Manager kan du distribuera till fler än en resurs grupp i en enda distribution. Du använder kapslade mallar för att ange resurs grupper som skiljer sig från resurs gruppen i distributions åtgärden. Resurs grupperna kan finnas i olika prenumerationer.

> [!NOTE]
> Du kan distribuera till **800 resurs grupper** i en enda distribution. Den här begränsningen innebär vanligt vis att du kan distribuera till en resurs grupp som angetts för den överordnade mallen och upp till 799 resurs grupper i kapslade eller länkade distributioner. Men om den överordnade mallen bara innehåller kapslade eller länkade mallar och inte själv distribuerar några resurser, kan du inkludera upp till 800 resurs grupper i kapslade eller länkade distributioner.

## <a name="specify-subscription-and-resource-group"></a>Ange prenumeration och resurs grupp

Använd en [kapslad eller länkad mall](linked-templates.md)om du vill rikta en resurs grupp från en annan resurs grupp än den för den överordnade mallen. I resurs typen distribution anger du värden för det prenumerations-ID och den resurs grupp som du vill att den kapslade mallen ska distribuera till.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Om du inte anger prenumerations-ID eller resurs grupp används prenumerationen och resurs gruppen från den överordnade mallen. Alla resurs grupper måste finnas innan du kör distributionen.

Det konto som distribuerar mallen måste ha behörighet att distribuera till det angivna prenumerations-ID: t. Om den angivna prenumerationen finns i en annan Azure Active Directory klient organisation måste du [lägga till gäst användare från en annan katalog](../../active-directory/b2b/what-is-b2b.md).

I följande exempel distribueras två lagrings konton. Det första lagrings kontot distribueras till den resurs grupp som anges i distributions åtgärden. Det andra lagrings kontot distribueras till resurs gruppen som anges i `secondResourceGroup` parametrarna och `secondSubscriptionID` :

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Om du anger `resourceGroup` namnet på en resurs grupp som inte finns, Miss lyckas distributionen.

Testa föregående mall och se resultatet med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration**använder du:

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

Om du vill distribuera två lagrings konton till **två prenumerationer**använder du:

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

Om du vill distribuera två lagrings konton till två resurs grupper i **samma prenumeration**använder du:

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

Om du vill distribuera två lagrings konton till **två prenumerationer**använder du:

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

## <a name="use-functions"></a>Använda funktioner

Funktionerna [resourceGroup ()](template-functions-resource.md#resourcegroup) och [Subscription ()](template-functions-resource.md#subscription) löses på olika sätt beroende på hur du anger mallen. När du länkar till en extern mall, matchas alltid funktionerna med omfånget för mallen. När du kapslar en mall i en överordnad mall använder du `expressionEvaluationOptions` egenskapen för att ange om funktionerna ska matcha till resurs gruppen och prenumerationen för den överordnade mallen eller den kapslade mallen. Ställ in egenskapen på för `inner` att matcha omfånget för den kapslade mallen. Ställ in egenskapen på för `outer` att matcha omfånget för den överordnade mallen.

Följande tabell visar om funktionerna matchas med den överordnade eller inbäddade resurs gruppen och prenumerationen.

| Malltyp | Omfång | Lösning |
| ------------- | ----- | ---------- |
| Nest        | yttre (standard) | Överordnad resurs grupp |
| Nest        | innersta | Under resurs grupp |
| länkade        | E.t.   | Under resurs grupp |

Följande [exempel-mall](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) visar:

* kapslad mall med standard omfång (yttre)
* kapslad mall med inre omfång
* länkad mall

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Testa föregående mall och se resultatet med PowerShell eller Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Utdata från föregående exempel är:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Utdata från föregående exempel är:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---

## <a name="next-steps"></a>Nästa steg

* Information om hur du definierar parametrar i din mall finns i [förstå strukturen och syntaxen för Azure Resource Manager mallar](template-syntax.md).
* Tips om hur du löser vanliga distributions fel finns i [Felsöka vanliga problem med Azure-distribution med Azure Resource Manager](common-deployment-errors.md).
* Information om hur du distribuerar en mall som kräver en SAS-token finns i [distribuera privat mall med SAS-token](secure-template-with-sas-token.md).

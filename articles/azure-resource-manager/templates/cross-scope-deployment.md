---
title: Distribuera resurser över omfång
description: Visar hur du riktar in mer än ett omfång under en distribution. Omfånget kan vara en klient, hanterings grupper, prenumerationer och resurs grupper.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: d3c3f2ded72bfcf2f552e635dc4eb4010491eb75
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92559000"
---
# <a name="deploy-azure-resources-across-scopes"></a>Distribuera Azure-resurser över omfång

Med Azure Resource Manager mallar (ARM-mallar) kan du distribuera till fler än ett omfång i en enda distribution. Tillgängliga omfattningar är en klient, hanterings grupper, prenumerationer och resurs grupper. Du kan till exempel distribuera resurser till en resurs grupp och i samma mall distribuera resurser till en annan resurs grupp. Du kan också distribuera resurser till en hanterings grupp och även distribuera resurser till en resurs grupp i den hanterings gruppen.

Du kan använda [kapslade eller länkade mallar](linked-templates.md) för att ange omfång som skiljer sig från det primära omfånget för distributions åtgärden.

## <a name="available-scopes"></a>Tillgängliga omfattningar

Omfattningen som används för distributions åtgärden avgör vilka andra omfattningar som är tillgängliga. Du kan distribuera till [klienten](deploy-to-tenant.md), [hanterings gruppen](deploy-to-management-group.md), [prenumerationen](deploy-to-subscription.md)eller [resurs gruppen](deploy-powershell.md). Du kan inte gå upp nivåer i hierarkin från den primära distributions nivån. Om du till exempel distribuerar till en prenumeration kan du inte stega upp en nivå för att distribuera resurser till en hanterings grupp. Men du kan distribuera till hanterings gruppen och stegvisa nivåer för distribution till en prenumeration eller resurs grupp.

För varje omfång måste användaren som distribuerar mallen ha behörighet att skapa resurser.

## <a name="cross-resource-groups"></a>Kors resurs grupper

Använd en [kapslad eller länkad mall](linked-templates.md)om du vill rikta en resurs grupp från en annan resurs grupp än den för den överordnade mallen. I resurs typen distribution anger du värden för det prenumerations-ID och den resurs grupp som du vill att den kapslade mallen ska distribuera till. Resurs grupperna kan finnas i olika prenumerationer.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Om du inte anger prenumerations-ID eller resurs grupp används prenumerationen och resurs gruppen från den överordnade mallen. Alla resurs grupper måste finnas innan du kör distributionen.

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

## <a name="cross-subscription-management-group-and-tenant"></a>Mellan prenumerationer, hanterings grupp och klient organisation

När du anger olika omfång för prenumeration, hanterings grupp och distributioner av klient nivåer använder du kapslade distributioner som exempel på resurs grupper. De egenskaper som du använder för att ange omfång kan skilja sig åt. Dessa scenarier beskrivs i artiklarna om distributions nivåer. Mer information finns i:

* [Skapa resurs grupper och resurser på prenumerations nivå](deploy-to-subscription.md)
* [Skapa resurser på hanterings grupps nivå](deploy-to-management-group.md)
* [Skapa resurser på klient nivå](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>Så här löser du funktioner i omfattningar

När du distribuerar till fler än ett omfång matchas [resourceGroup ()](template-functions-resource.md#resourcegroup) och [prenumerationen ()](template-functions-resource.md#subscription) på olika sätt utifrån hur du anger mallen. När du länkar till en extern mall, matchas alltid funktionerna med omfånget för mallen. När du kapslar en mall i en överordnad mall använder du `expressionEvaluationOptions` egenskapen för att ange om funktionerna ska matcha till resurs gruppen och prenumerationen för den överordnade mallen eller den kapslade mallen. Ställ in egenskapen på för `inner` att matcha omfånget för den kapslade mallen. Ställ in egenskapen på för `outer` att matcha omfånget för den överordnade mallen.

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

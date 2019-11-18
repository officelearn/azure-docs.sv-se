---
title: Ta bort resurs grupp och resurser
description: Beskriver hur du tar bort resurs grupper och resurser. Det beskriver hur Azure Resource Manager beställer borttagningen av resurser när en resurs grupp tas bort. Beskriver svarskoder samt hur Resource Manager hanterar dem för att fastställa om borttagningen lyckades.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: c84dcf64555f40f45dbdaeedb05d4cc461f2cbb7
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74150807"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Azure Resource Manager resurs grupp och borttagning av resurs

Den här artikeln visar hur du tar bort resurs grupper och resurser. Det beskriver hur Azure Resource Manager beställer borttagningen av resurser när du tar bort en resurs grupp.

## <a name="how-order-of-deletion-is-determined"></a>Hur borttagnings ordningen bestäms

När du tar bort en resursgrupp, anger Resource Manager den för att ta bort resurser. Den använder följande ordning:

1. Alla underordnade (kapslade) resurser tas bort.

2. Resurser som hanterar andra resurser tas bort nästa. En resurs kan ha den `managedBy` egenskapen in för att ange att en annan resurs hanterar den. När den här egenskapen anges tas den resurs som hanterar andra resursen bort innan de övriga resurserna.

3. De återstående resurserna tas bort efter de föregående två kategorierna.

När ordningen bestäms utfärdar en borttagningsåtgärd för varje resurs i Resource Manager. Det väntar några beroenden som ska slutföras innan du fortsätter.

För synkrona åtgärder är förväntade lyckat svar koderna:

* 200
* 204
* 404

För asynkrona åtgärder är det förväntade lyckat svaret 202. Resource Manager spårar location-huvudet eller azure-asynkrona åtgärden huvudet att bestämma status för asynkron borttagningen.
  
### <a name="deletion-errors"></a>Fel vid borttagning

När en borttagningsåtgärd returnerar ett fel, försöker Resource Manager ta bort anropet. Återförsök sker för 5xx, 429 och 408 statuskoder. Som standard är tidsperioden för återförsök 15 minuter.

## <a name="after-deletion"></a>Efter borttagningen

Resource Manager skickar en GET-anrop för varje resurs som den försökte ta bort. Svaret på GET-anrop anses vara 404. När Resource Manager hämtar 404, som de anser att borttagningen har slutförts. Resource Manager tar bort resursen från sin cache.

Om GET-anrop på resursen returnerar 200 eller 201, återskapas Resource Manager resursen.

Om åtgärden GET returnerar ett fel, försöker Resource Manager GET för följande felkod:

* Mindre än 100
* 408
* 429
* Större än 500

För andra felkoder inte Resource Manager borttagningen av resursen.

## <a name="delete-resource-group"></a>Ta bort resursgrupp

Använd någon av följande metoder för att ta bort resurs gruppen.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. I [portalen](https://portal.azure.com)väljer du den resurs grupp som du vill ta bort.

1. Välj **Ta bort resursgrupp**.

   ![Ta bort resursgrupp](./media/resource-group-delete/delete-group.png)

1. Bekräfta borttagningen genom att skriva namnet på resurs gruppen

---

## <a name="delete-resource"></a>Ta bort resurs

Använd någon av följande metoder för att ta bort en resurs.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

1. I [portalen](https://portal.azure.com)väljer du den resurs som du vill ta bort.

1. Välj **Ta bort**. Följande skärm bild visar hanterings alternativen för en virtuell dator.

   ![Ta bort resurs](./media/resource-group-delete/delete-resource.png)

1. Bekräfta borttagningen när du uppmanas att göra det.

---


## <a name="next-steps"></a>Nästa steg

* Information om Resource Manager-begrepp finns i [översikt över Azure Resource Manager](resource-group-overview.md).
* Ta bort kommandon finns i [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete), och [REST API](/rest/api/resources/resourcegroups/delete).

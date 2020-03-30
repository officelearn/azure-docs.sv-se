---
title: Ta bort resursgrupp och resurser
description: Beskriver hur du tar bort resursgrupper och resurser. Den beskriver hur Azure Resource Manager beställer borttagning av resurser när en borttagning av en resursgrupp. Den beskriver svarskoderna och hur Resource Manager hanterar dem för att avgöra om borttagningen lyckades.
ms.topic: conceptual
ms.date: 09/03/2019
ms.custom: seodec18
ms.openlocfilehash: db56cf0897cd90f1e6e51199032d0d9712530f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274026"
---
# <a name="azure-resource-manager-resource-group-and-resource-deletion"></a>Resursgrupp och borttagning av resurser i Azure Resource Manager

Den här artikeln visar hur du tar bort resursgrupper och resurser. Den beskriver hur Azure Resource Manager beställer borttagning av resurser när du tar bort en resursgrupp.

## <a name="how-order-of-deletion-is-determined"></a>Hur raderingsordningen bestäms

När du tar bort en resursgrupp bestämmer Resource Manager ordningen för att ta bort resurser. Den använder följande ordning:

1. Alla underordnade (kapslade) resurser tas bort.

2. Resurser som hanterar andra resurser tas bort härnäst. En resurs kan `managedBy` ha egenskapen inställd på att ange att en annan resurs hanterar den. När den här egenskapen har angetts tas resursen som hanterar den andra resursen bort före de andra resurserna.

3. De återstående resurserna tas bort efter de två föregående kategorierna.

När ordern har bestämts utfärdar Resource Manager en DELETE-åtgärd för varje resurs. Den väntar på att eventuella beroenden ska slutföras innan du fortsätter.

För synkrona åtgärder är de förväntade lyckade svarskoderna:

* 200
* 204
* 404

För asynkrona åtgärder är det förväntade lyckade svaret 202. Resource Manager spårar platshuvudet eller azure-async-åtgärdshuvudet för att fastställa status för den asynkrona borttagningsåtgärden.
  
### <a name="deletion-errors"></a>Fel vid borttagning

När en borttagningsåtgärd returnerar ett fel försöker Resource Manager ta bort anropet. Försök sker för statuskoderna 5xx, 429 och 408. Som standard är tidsperioden för återförsök 15 minuter.

## <a name="after-deletion"></a>Efter radering

Resource Manager utfärdar ett GET-anrop på varje resurs som den försökte ta bort. Svaret från detta GET-samtal förväntas vara 404. När Resource Manager får en 404, anser den att borttagningen har slutförts. Resource Manager tar bort resursen från cacheminnet.

Men om GET-anropet på resursen returnerar en 200 eller 201 återskapas resursen.

Om GET-åtgärden returnerar ett fel försöker Resource Manager hämta för följande felkod:

* Färre än 100
* 408
* 429
* Större än 500

För andra felkoder misslyckas resurshanteraren att resursen tas bort.

## <a name="delete-resource-group"></a>Ta bort resursgrupp

Använd någon av följande metoder för att ta bort resursgruppen.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResourceGroup -Name ExampleResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group delete --name ExampleResourceGroup
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Markera den resursgrupp som du vill ta bort i [portalen.](https://portal.azure.com)

1. Välj **Ta bort resursgrupp**.

   ![Ta bort resursgrupp](./media/delete-resource-group/delete-group.png)

1. Om du vill bekräfta borttagningen skriver du namnet på resursgruppen

---

## <a name="delete-resource"></a>Ta bort resurs

Använd någon av följande metoder för att ta bort en resurs.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```azurepowershell-interactive
Remove-AzResource `
  -ResourceGroupName ExampleResourceGroup `
  -ResourceName ExampleVM `
  -ResourceType Microsoft.Compute/virtualMachines
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az resource delete \
  --resource-group ExampleResourceGroup \
  --name ExampleVM \
  --resource-type "Microsoft.Compute/virtualMachines"
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Markera den resurs som du vill ta bort i [portalen.](https://portal.azure.com)

1. Välj **Ta bort**. Följande skärmbild visar hanteringsalternativen för en virtuell dator.

   ![Ta bort resurs](./media/delete-resource-group/delete-resource.png)

1. Bekräfta borttagningen när du uppmanas att göra det.

---


## <a name="next-steps"></a>Nästa steg

* Information om hur du förstår Resource Manager-begrepp finns i [Översikt över Azure Resource Manager](overview.md).
* Borttagningskommandon finns i [PowerShell,](/powershell/module/az.resources/Remove-AzResourceGroup) [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete)och [REST API](/rest/api/resources/resourcegroups/delete).

---
title: Lägg till regioner, ändra prioritet för redundans, Utlös redundans för ett Azure Cosmos-konto
description: Lägg till regioner, ändra prioritet för redundans, Utlös redundans för ett Azure Cosmos-konto
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: c8c8cbd81be07070ec5434ee49e42d81641bc969
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083944"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Lägg till regioner, ändra prioritet för redundans, Utlös redundans för ett Azure Cosmos-konto med Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt, kräver det här avsnittet att du kör Azure CLI-version 2.9.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="sample-script"></a>Exempelskript

Det här skriptet visar tre åtgärder.

- Lägg till en region i ett befintligt Azure Cosmos-konto.
- Ändra prioritet för regional redundans (gäller konton med automatisk redundans)
- Utlös en manuell redundansväxling från primär till sekundär region (gäller konton med manuell redundans)

> [!NOTE]
> Det går inte att lägga till och ta bort regions åtgärder på ett Cosmos-konto medan andra egenskaper ändras.

> [!NOTE]
> Det här exemplet demonstrerar användningen av ett SQL-API-konto men dessa åtgärder är identiska för alla databas-API: er i Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Uppdaterar ett Azure Cosmos DB konto (Lägg till eller ta bort region). |
| [AZ cosmosdb redundans-prioritet-ändra](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Uppdatera växlings prioritet eller utlös redundans för ett Azure Cosmos DB-konto. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

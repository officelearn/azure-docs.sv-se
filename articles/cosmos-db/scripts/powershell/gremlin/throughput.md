---
title: PowerShell-skript för data flöde (RU/s) åtgärder för Azure Cosmos DB Gremlin-API
description: PowerShell-skript för data flöde (RU/s) åtgärder för Gremlin-API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 53e427b77066d1f1833ce12f856e4157a51ec530
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075542"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Data flöde (RU/s)-åtgärder med PowerShell för en databas eller graf för Azure Cosmos DB-Gremlin-API
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Beräkna dataflöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-get.ps1 "Get throughput on a database or graph for Gremlin API")]

## <a name="update-throughput"></a>Uppdatera data flöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-update.ps1 "Update throughput on a database or graph for Gremlin API")]

## <a name="migrate-throughput"></a>Migrera data flöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or graph for Gremlin API")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlindatabasethroughput) | Hämtar data flöde svärdet för Gremlin-API-databasen. |
| [Get-AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraphthroughput) | Hämtar data flöde svärdet för Gremlin-API-diagrammet. |
| [Uppdatera – AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlindatabasethroughput) | Uppdaterar data flöde svärdet för Gremlin-API-databasen. |
| [Uppdatera – AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlingraphthroughput) | Uppdaterar data flöde svärdet för Gremlin-API-diagrammet. |
| [Invoke-AzCosmosDBGremlinDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlindatabasethroughputmigration) | Migrera data flödet för en Gremlin-API-databas. |
| [Invoke-AzCosmosDBGremlinGraphThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlingraphthroughputmigration) | Migrera genomflöde av ett Gremlin API-diagram. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).
---
title: PowerShell-skript för data flöde (RU/s) åtgärder för Azure Cosmos databaser-API för MongoDB
description: PowerShell-skript för data flöde (RU/s) åtgärder för Azure Cosmos databaser-API för MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 54e990b27461bd47466e12e5f51f3ed2a8a585ec
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92489106"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-collection-for-azure-cosmos-db-api-for-mongodb"></a>Data flöde (RU/s)-åtgärder med PowerShell för en databas eller samling för Azure Cosmos DB API för MongoDB

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Beräkna dataflöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-get.ps1 "Get throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="update-throughput"></a>Uppdatera data flöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-update.ps1 "Update throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="migrate-throughput"></a>Migrera data flöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBMongoDBDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabasethroughput) | Hämtar data flöde svärdet för angivet Azure Cosmos DB-API för MongoDB-databasen. |
| [Get-AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollectionthroughput) | Hämtar data flöde svärdet för angivet Azure Cosmos DB-API för MongoDB-samlingen. |
| [Uppdatera – AzCosmosDBMongoDBDatabaseThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbdatabasethroughput) | Uppdaterar data flöde svärdet för Azure Cosmos DB-API: et för MongoDB-databasen. |
| [Uppdatera – AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollectionthroughput) | Uppdaterar data flöde svärdet för Azure Cosmos DB API för MongoDB-samlingen. |
| [Invoke-AzCosmosDBMongoDBDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbmongodbdatabasethroughputmigration) | Migrera genomflöde av ett Azure Cosmos DB-API för MongoDB-samling. |
| [Invoke-AzCosmosDBMongoDBCollectionThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbmongodbcollectionthroughputmigration) | Migrera genomflöde av ett Azure Cosmos DB-API för MongoDB-samling. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).
---
title: PowerShell-skript för data flöde (RU/s) åtgärder för Azure Cosmos DB API för Cassandra resurser
description: PowerShell-skript för data flöde (RU/s) åtgärder för Azure Cosmos DB API för Cassandra resurser
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 8bf49445990596471a6526274c2aa86843d4868a
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488273"
---
# <a name="throughput-rus-operations-with-powershell-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Data flöde (RU/s)-åtgärder med PowerShell för ett tecken utrymme eller en tabell för Azure Cosmos DB-API för Cassandra

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Beräkna dataflöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-get.ps1 "Get throughput on a keyspace or table for Cassandra API")]

## <a name="update-throughput"></a>Uppdatera data flöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-update.ps1 "Update throughput on a keyspace or table for Cassandra API")]

## <a name="migrate-throughput"></a>Migrera data flöde

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a keyspace or table for Cassandra API")]

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
| [Get-AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandrakeyspacethroughput) | Hämtar data flöde svärdet för API för Cassandra-utrymmet. |
| [Get-AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbcassandratablethroughput) | Hämtar data flöde svärdet för API för Cassandras tabellen. |
| [Uppdatera – AzCosmosDBCassandraKeyspaceThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandrakeyspacethroughput) | Uppdaterar data flöde svärdet för API för Cassandra-utrymmet. |
| [Uppdatera – AzCosmosDBCassandraTableThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbcassandratablethroughput) | Uppdaterar data flöde svärdet för API för Cassandras tabellen. |
| [Invoke-AzCosmosDBCassandraKeyspaceThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandrakeyspacethroughputmigration) | Migrera data flöde för ett API för Cassandra-adressutrymme. |
| [Invoke-AzCosmosDBCassandraTableThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbcassandratablethroughputmigration) | Migrera data flöde för en API för Cassandra tabell. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).
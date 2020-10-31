---
title: PowerShell-skript för att skapa Azure Cosmos DB API för Cassandra nyckel utrymme och tabell med autoskalning
description: Azure PowerShell skript – Azure Cosmos DB skapa API för Cassandra nyckel utrymme och tabell med autoskalning
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 631f689348f4fa99bc15209f81436a718df163b4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074652"
---
# <a name="create-a-keyspace-and-table-with-autoscale-for-azure-cosmos-db---cassandra-api"></a>Skapa ett nyckel utrymme och en tabell med autoskalning för Azure Cosmos DB-API för Cassandra
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-autoscale.ps1 "Create a keyspace and table with autoscale for Cassandra API")]

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
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Skapar ett Cosmos DB-konto. |
| [New-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/new-azcosmosdbcassandrakeyspace) | Skapar ett Cosmos DB API för Cassandrat tecken utrymme. |
| [New-AzCosmosDBCassandraClusterKey](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Skapar en Cosmos DB API för Cassandra kluster nyckel. |
| [New-AzCosmosDBCassandraColumn](/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Skapar en Cosmos DB API för Cassandra-kolumn. |
| [New-AzCosmosDBCassandraSchema](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Skapar ett Cosmos DB API för Cassandras schema. |
| [New-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/new-azcosmosdbcassandratable) | Skapar en Cosmos DB API för Cassandra tabell. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell dokumentation](/powershell/)

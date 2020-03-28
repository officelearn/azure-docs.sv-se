---
title: PowerShell-skript för att skapa Azure Cosmos DB SQL API-databas och behållare
description: Azure PowerShell-skript - Azure Cosmos DB skapar SQL API-databas och behållare
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 719e2cd831a982c62ab965cd7dc8a37c4cb41265
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365613"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Skapa en databas och behållare för Azure Cosmos DB - SQL API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar ett Cosmos-konto för SQL-API (Core) i två regioner med konsekvens på sessionsnivå, en databas och en behållare med en partitionsnyckel, anpassad indexeringsprincip, `multipleWriteLocations=true`unik nyckelprincip, TTL, dedikerat dataflöde och sista brännaren vinner konfliktlösningsprincip med en anpassad konfliktlösningssökväg som ska användas när .

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

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
| [Ny-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Skapar ett nytt Cosmos DB-konto. |
| [Set-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Skapar en ny eller uppdaterar en befintlig Cosmos DB SQL Database. |
| [Ny-AzCosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Skapar ett nytt Cosmos DB SQL UniqueKey-objekt. |
| [Ny-AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Skapar ett nytt Cosmos DB SQL UniqueKeyPolicy-objekt. |
| [Nya-AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Skapar ett nytt objekt av typen PSIndexes som används som parameter för Set-AzCosmosDBSqlIncludedPath. |
| [Nya-AzCosmosDBSqlInkluderadPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Skapar ett nytt objekt av typen PSIncludedPath som används som parameter för New-AzCosmosDBSqlIndexingPolicy. |
| [Ny-AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Skapar ett nytt objekt av typen PSSqlIndexingPolicy som används som parameter för Set-AzCosmosDBSqlContainer. |
| [Ny-AzCosmosDBSqlConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Skapar ett nytt objekt av typen PSSqlConflictResolutionPolicy som används som parameter för Set-AzCosmosDBSqlContainer. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Skapar en ny eller uppdaterar en befintlig Cosmos DB SQL Container. |
|**Azure-resursgrupper**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../../../powershell-samples.md).

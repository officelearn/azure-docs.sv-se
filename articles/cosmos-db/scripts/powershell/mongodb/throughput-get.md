---
title: PowerShell-skript för att hämta data flöde (RU/s) för Azure Cosmos DBs API för MongoDB
description: Azure PowerShell script-Azure Cosmos DB hämta data flöde (RU/s) för MongoDB-API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 6421636b806f2c32f5beba8c3cfafc6cd146e945
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87506672"
---
# <a name="get-throughput-rus-for-a-database-or-collection-for-azure-cosmos-db---mongodb-api"></a>Hämta data flöde (RU/s) för en databas eller samling för Azure Cosmos DB-MongoDB-API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-get.ps1 "Get throughput on a database or collection for MongoDB API")]

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
| [Get-AzCosmosDBMongoDBDatabaseThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabasethroughput) | Hämtar data flöde svärdet för den angivna MongoDB API-databasen. |
| [Get-AzCosmosDBMongoDBCollectionThroughput](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollectionthroughput) | Hämtar data flöde svärdet för den angivna MongoDB API-samlingen. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Fler skriptexempel för PowerShell i Azure Cosmos DB finns i [PowerShell-skript för Azure Cosmos DB](../../../powershell-samples.md).
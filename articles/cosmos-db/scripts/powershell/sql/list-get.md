---
title: PowerShell-skript för att lista och hämta Azure Cosmos DB SQL API-resurser
description: Azure PowerShell skript-Azure Cosmos DB lista och hämta åtgärder för SQL API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: b7a316a13ecc5683474411e3ac185f2136914186
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92481983"
---
# <a name="list-and-get-databases-and-containers-for-azure-cosmos-db---sql-core-api"></a>Lista och hämta databaser och behållare för API: et för Azure Cosmos DB-SQL (Core)

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-list-get.ps1 "List and get databases and containers for SQL API")]

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
| [Get-AzCosmosDBAccount](/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Listar Cosmos DB konton eller hämtar ett angivet Cosmos DB-konto. |
| [Get-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/get-azcosmosdbsqldatabase) | Visar en lista Cosmos DB databaser i ett konto eller hämtar en angiven Cosmos DB databas i ett konto. |
| [Get-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/get-azcosmosdbsqlcontainer) | Listar Cosmos DB behållare i en databas eller hämtar en angiven Cosmos DB behållare i en databas. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).
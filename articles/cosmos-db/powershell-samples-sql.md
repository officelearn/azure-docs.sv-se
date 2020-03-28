---
title: Azure PowerShell-exempel för Azure Cosmos DB - SQL (Core) API
description: Hämta Azure PowerShell-exempel för att utföra olika vanliga uppgifter i Azure Cosmos DB SQL API-konton
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: efc0ff8e6c198071d3906a0e7e999510198f73bf
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366181"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell-exempel för Azure Cosmos DB - SQL (Core) API

Följande tabell innehåller länkar till vanliga Azure PowerShell-skript för Azure Cosmos DB för SQL (Core) API. Om du vill tämpla dessa PowerShell-exempel för Cosmos DB från vår GitHub-lagringsplats besöker du [Cosmos DB PowerShell-samplingar på GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Ytterligare Cosmos DB PowerShell-exempel för SQL-API (Core) och dokumentation finns i [Hantera Azure Cosmos DB SQL API-resurser med PowerShell](manage-with-powershell.md). För Cosmos DB PowerShell-exempel för andra API:er finns i [Cassandra API](powershell-samples-cassandra.md), [MongoDB API](powershell-samples-mongodb.md), [Gremlin API](powershell-samples-gremlin.md)och [Tabell-API](powershell-samples-table.md).

> [!NOTE]
> Exemplen använder [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) management cmdlets. Observera att `Az.CosmosDB` cmdlets fortfarande är i förhandsgranskning och kan ändras innan de släpps. Kontrollera om det `Az.CosmosDB` finns uppdateringar regelbundet.

| | |
|---|---|
|[Skapa ett konto, en databas och en behållare](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB-konto, databas och behållare. |
|[Skapa en behållare med en stor partitionsnyckel](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa en behållare med en stor partitionsnyckel. |
|[Lista eller hämta databaser eller behållare](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta databas eller behållare. |
|[Skaffa RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skaffa RU/s för en databas eller behållare. |
|[Uppdatera RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera RU/s för en databas eller behållare. |
|[Skapa en behållare utan indexprincip](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa en Azure Cosmos-behållare med indexprincipen inaktiverad.|
|[Uppdatera ett konto](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera standardkonsekvensnivån för ett Cosmos DB-konto. |
|[Uppdatera ett kontos regioner](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera ett Cosmos DB-kontos regioner. |
|[Ändra redundansprioritet eller utlösa redundans](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra den regionala redundansprioriteten för ett Azure Cosmos-konto eller utlösa en manuell redundans. |
|[Kontonycklar eller anslutningssträngar](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta primära och sekundära nycklar, anslutningssträngar eller återskapa en kontonyckel för ett Azure Cosmos DB-konto. |
|[Skapa ett Cosmos-konto med IP-brandvägg](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB-konto med IP-brandväggen aktiverad. |
|||

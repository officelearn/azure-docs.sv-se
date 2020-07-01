---
title: 'Azure PowerShell exempel för API: et för Azure Cosmos DB-SQL (Core)'
description: Hämta Azure PowerShell exempel för att utföra olika vanliga uppgifter i Azure Cosmos DB SQL API-konton
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/12/2020
ms.author: mjbrown
ms.openlocfilehash: fc4ec916e71f2fcfd3b411420879d42b2fa90f18
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/30/2020
ms.locfileid: "85563852"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db---sql-core-api"></a>Azure PowerShell exempel för API: et för Azure Cosmos DB-SQL (Core)

Följande tabell innehåller länkar till vanliga Azure PowerShell skript för Azure Cosmos DB för SQL-API (Core). Om du vill använda dessa PowerShell-exempel för Cosmos DB från vår GitHub-lagringsplats går du [till Cosmos DB PowerShell-exempel på GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

Ytterligare Cosmos DB PowerShell-exempel för SQL (Core) API och dokumentation finns i [hantera Azure Cosmos DB SQL API-resurser med hjälp av PowerShell](manage-with-powershell.md). Cosmos DB PowerShell-exempel för andra API: er finns i [API för Cassandra](powershell-samples-cassandra.md), [MongoDB-API](powershell-samples-mongodb.md), [Gremlin-API](powershell-samples-gremlin.md)och [tabell-API](powershell-samples-table.md).

> [!NOTE]
> I exemplen används [AZ. CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) -hanterings-cmdletar. Sök efter uppdateringar `Az.CosmosDB` regelbundet.

|Uppgift | Description |
|---|---|
|[Skapa ett konto, en databas och en behållare](scripts/powershell/sql/ps-sql-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB konto, en databas och en behållare. |
|[Skapa en behållare med en stor partitionsnyckel](scripts/powershell/sql/ps-sql-container-create-large-partition-key.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa en behållare med en stor partitionsnyckel. |
|[Lista eller hämta databaser eller behållare](scripts/powershell/sql/ps-sql-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta databas eller behållare. |
|[Hämta RU/s](scripts/powershell/sql/ps-sql-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta RU/s för en databas eller behållare. |
|[Uppdatera RU/s](scripts/powershell/sql/ps-sql-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera RU/s för en databas eller behållare. |
|[Skapa en behållare utan index princip](scripts/powershell/sql/ps-sql-container-create-index-none.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Skapa en Azure Cosmos-behållare med index princip inaktive rad.|
|[Uppdatera ett konto](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera standard konsekvens nivån för ett Cosmos DB-konto. |
|[Uppdatera ett kontos regioner](scripts/powershell/common/ps-account-update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera ett Cosmos DB kontos regioner. |
|[Ändra växlings prioritet eller utlös redundans](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra den regionala växlings prioriteten för ett Azure Cosmos-konto eller utlösa en manuell redundansväxling. |
|[Konto nycklar eller anslutnings strängar](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta primära och sekundära nycklar, anslutnings strängar eller återskapa en konto nyckel för ett Azure Cosmos DB konto. |
|[Skapa ett Cosmos-konto med IP-brandvägg](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB-konto med IP-brandvägg aktive rad. |
|[Lås resurser från borttagning](scripts/powershell/sql/powershell-sql-lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||

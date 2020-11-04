---
title: Azure PowerShell exempel för Azure Cosmos DB API för Cassandra
description: Hämta Azure PowerShell exempel för att utföra vanliga uppgifter i Azure Cosmos DB API för Cassandra
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 4f3166693de0365af387f4b6005a4a309f17bb5b
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93342291"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-cassandra-api"></a>Azure PowerShell exempel för Azure Cosmos DB API för Cassandra
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Följande tabell innehåller länkar till vanliga Azure PowerShell skript för Azure Cosmos DB. Använd länkarna till höger för att navigera till API-/regionsspecifika exempel. Vanliga exempel är samma för alla API: er. Referens sidor för alla Azure Cosmos DB PowerShell-cmdletar finns i [referensen Azure PowerShell](/powershell/module/az.cosmosdb). Sök efter uppdateringar `Az.CosmosDB` regelbundet. Du kan också förgrena dessa PowerShell-exempel för Cosmos DB från vår GitHub-lagringsplats [Cosmos DB PowerShell-exempel på GitHub](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb).

## <a name="common-samples"></a>Vanliga exempel

|Uppgift | Beskrivning |
|---|---|
|[Uppdatera ett konto](scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera standard konsekvens nivån för ett Cosmos DB-konto. |
|[Uppdatera ett kontos regioner](scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera ett Cosmos DB kontos regioner. |
|[Ändra växlings prioritet eller utlös redundans](scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra den regionala växlings prioriteten för ett Azure Cosmos-konto eller utlösa en manuell redundansväxling. |
|[Konto nycklar eller anslutnings strängar](scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta primära och sekundära nycklar, anslutnings strängar eller återskapa en konto nyckel för ett Azure Cosmos DB konto. |
|[Skapa ett Cosmos-konto med IP-brandvägg](scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos DB-konto med IP-brandvägg aktive rad. |
|||

## <a name="cassandra-api-samples"></a>API för Cassandra exempel

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto, ett tomt utrymme och en tabell](scripts/powershell/cassandra/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, ett blank steg och en tabell. |
|[Skapa ett konto, ett nyckel utrymme och en tabell med autoskalning](scripts/powershell/cassandra/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, nyckel utrymme och tabell med autoskalning. |
|[Lista eller hämta blank steg eller tabeller](scripts/powershell/cassandra/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta blank steg eller tabeller. |
|[Data flödes åtgärder](scripts/powershell/cassandra/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Data flödes åtgärder för ett nyckel utrymme eller en tabell, inklusive Hämta, uppdatera och migrera mellan autoskalning och standard data flöde. |
|[Lås resurser från borttagning](scripts/powershell/cassandra/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||

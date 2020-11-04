---
title: Azure PowerShell exempel för Azure Cosmos DB API för MongoDB
description: Hämta Azure PowerShell exempel för att utföra vanliga uppgifter i Azure Cosmos DB API för MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3b401aec29da8702e262e0e8e9467454e77bc16d
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93332941"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>Azure PowerShell exempel för Azure Cosmos DB API för MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

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

## <a name="mongo-db-api-samples"></a>Mongo DB API-exempel

|Uppgift | Beskrivning |
|---|---|
|[Skapa ett konto, en databas och en samling](scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, en databas och en samling. |
|[Skapa ett konto, en databas och en samling med autoskalning](scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, en databas och en samling med autoskalning. |
|[Lista eller hämta databaser eller samlingar](scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta databas eller samling. |
|[Data flödes åtgärder](scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Data flödes åtgärder för en databas eller samling, inklusive Hämta, uppdatera och migrera mellan autoskalning och standard data flöde. |
|[Lås resurser från borttagning](scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Förhindra att resurser tas bort med resurs lås. |
|||

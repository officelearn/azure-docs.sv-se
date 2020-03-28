---
title: Azure PowerShell-exempel för Azure Cosmos DB - MongoDB API
description: Hämta Azure PowerShell-exemplen för att utföra olika vanliga uppgifter i Azure Cosmos DB:s API för MongoDB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 810161268df405d84f6c190d4d7f3b67f2a1def8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80366203"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-mongodb-api"></a>Azure PowerShell-exempel för Azure Cosmos DB MongoDB API

Följande tabell innehåller länkar till exempel på Azure PowerShell-skript för Azure Cosmos DB för MongoDB API.

> [!NOTE]
> För närvarande kan du bara skapa 3.2-version (det `*.documents.azure.com`vill säga konton som använder slutpunkten i formatet) för Azure Cosmos DB:s API för MongoDB-konton med hjälp av PowerShell-, CLI- och Resource Manager-mallar. Om du vill skapa 3.6-versionen av konton använder du Azure-portalen i stället.

> [!NOTE]
> Exemplen använder [Az.CosmosDB](https://docs.microsoft.com/powershell/module/az.cosmosdb) management cmdlets. Observera att `Az.CosmosDB` cmdlets fortfarande är i förhandsgranskning och kan ändras innan de släpps. Kontrollera om det `Az.CosmosDB` finns uppdateringar regelbundet.

| | |
|---|---|
|[Skapa ett konto, en databas och en samling](scripts/powershell/mongodb/ps-mongodb-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Azure Cosmos-konto, databas och samling. |
|[Lista eller hämta databaser eller samlingar](scripts/powershell/mongodb/ps-mongodb-list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lista eller hämta databas eller samling. |
|[Skaffa RU/s](scripts/powershell/mongodb/ps-mongodb-ru-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skaffa RU/s för en databas eller samling. |
|[Uppdatera RU/s](scripts/powershell/mongodb/ps-mongodb-ru-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Uppdatera RU/s för en databas eller samling. |
|[Uppdatera ett konto eller lägga till en region](scripts/powershell/common/ps-account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Lägg till en region i ett Cosmos-konto. Kan också användas för att ändra andra kontoegenskaper, men dessa måste vara åtskilda från ändringar i regioner. |
|[Ändra redundansprioritet eller utlösa redundans](scripts/powershell/common/ps-account-failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Ändra den regionala redundansprioriteten för ett Azure Cosmos-konto eller utlösa en manuell redundans. |
|[Kontonycklar eller anslutningssträngar](scripts/powershell/common/ps-account-keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Hämta primära och sekundära nycklar, anslutningssträngar eller återskapa en kontonyckel för ett Azure Cosmos-konto. |
|[Skapa ett Cosmos-konto med IP-brandvägg](scripts/powershell/common/ps-account-firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapa ett Azure Cosmos-konto med IP-brandväggen aktiverad. |
|||

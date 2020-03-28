---
title: Azure CLI-exempel för Azure Cosmos DB MongoDB API
description: Azure CLI-exempel för Azure Cosmos DB MongoDB API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 9/25/2019
ms.author: mjbrown
ms.openlocfilehash: f7807a4c2024e16f563adbcb46a5c60e5c542dda
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "77524571"
---
# <a name="azure-cli-samples-for-azure-cosmos-db-mongodb-api"></a>Azure CLI-exempel för Azure Cosmos DB MongoDB API

Följande tabell innehåller länkar till exempel på Azure CLI-skript för Azure Cosmos DB MongoDB API. Referenssidor för alla Azure Cosmos DB CLI-kommandon finns i [Azure CLI Reference](/cli/azure/cosmosdb). Alla Azure Cosmos DB CLI-skriptexempel finns i [Azure Cosmos DB CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).

> [!NOTE]
> För närvarande kan du bara skapa 3.2-version (det `*.documents.azure.com`vill säga konton som använder slutpunkten i formatet) för Azure Cosmos DB:s API för MongoDB-konton med hjälp av PowerShell-, CLI- och Resource Manager-mallar. Om du vill skapa 3.6-versionen av konton använder du Azure-portalen i stället.

| |  |
|---|---|
| [Skapa ett Azure Cosmos-konto, databas och samling](scripts/cli/mongodb/create.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB-konto, databas och samling för MongoDB API. |
| [Ändra dataflöde](scripts/cli/mongodb/throughput.md?toc=%2fcli%2fazure%2ftoc.json) | Uppdatera RU/s i en databas och samling.|
| [Lägga till eller redundansregioner](scripts/cli/common/regions.md?toc=%2fcli%2fazure%2ftoc.json) | Lägg till en region, ändra redundansprioritet, utlösa en manuell redundans.|
| [Kontonycklar och anslutningssträngar](scripts/cli/common/keys.md?toc=%2fcli%2fazure%2ftoc.json) | Lista kontonycklar, skrivskyddade nycklar, återskapa nycklar och listanslutningssträngar.|
| [Säker med IP-brandvägg](scripts/cli/common/ipfirewall.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto med IP-brandväggen konfigurerad.|
| [Säkra nytt konto med tjänstslutpunkter](scripts/cli/common/service-endpoints.md?toc=%2fcli%2fazure%2ftoc.json)| Skapa ett Cosmos-konto och skydda med tjänstslutpunkter.|
| [Skydda befintligt konto med tjänstslutpunkter](scripts/cli/common/service-endpoints-ignore-missing-vnet.md?toc=%2fcli%2fazure%2ftoc.json)| Uppdatera ett Cosmos-konto för att skydda med tjänstslutpunkter när undernätet så småningom konfigureras.|
|||

---
title: Azure CLI-exempel för Azure Cosmos DB | Microsoft Docs
description: Azure CLI-exempel – Skapa och hantera Azure Cosmos DB-konton, databaser, containrar, regioner och brandväggar.
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/26/2018
ms.author: mjbrown
ms.openlocfilehash: 461207d0c9d27ed645dcac98e6256431bb23f8ad
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51005996"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure Cosmos DB. Det finns referenssidor för alla Azure Cosmos DB CLI-kommandon i [referensguiden för Azure CLI](/cli/azure/cosmosdb).

| |  |
|---|---|
|**Skapa Azure Cosmos DB-konton, databaser och containrar**||
| [Skapa ett SQL API-konto](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett konto, en databas och en container för SQL API i Azure Cosmos DB. |
| [Skapa ett MongoDB API-konto](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett Azure Cosmos DB MongoDB API-konto, databas och samling. |
| [Skapa ett Gremlin API-konto](scripts/create-gremlin-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett konto, en databas och en graf för Gremlin API i Azure Cosmos DB. |
| [Skapa ett Cassandra API-konto](scripts/create-cassandra-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett enskilt Azure Cosmos DB-konto och -databas med API för Cassandra. |
| [Skapa ett Table API-konto](scripts/create-table-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett konto för tabell-API för Azure Cosmos DB, en databas och en tabell. |
|**Skala Azure Cosmos DB**||
| [Skala containerns dataflöde](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ändrar dataflöde i en container.|
| [Replikera ett Azure Cosmos DB-databaskonto i flera regioner och konfigurera redundansprioriteringar](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replikerar kontodata globalt till flera regioner med en angiven redundansprioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta kontonycklar](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar de primära och sekundära huvudskrivnycklarna och de primära och sekundära skrivskyddade nycklarna för kontot.|
| [Hämta anslutningssträngen för MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar anslutningssträngen för att ansluta MongoDB-appen till Azure Cosmos DB-kontot.|
| [Återskapa kontonycklar](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Återskapa nycklar för kontot.|
| [Skapa en brandvägg](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en princip för inkommande IP-åtkomstkontroll för att begränsa åtkomsten till kontot från en godkänd uppsättning datorer och/eller molntjänster.|
|**Hög tillgänglighet, haveriberedskap, säkerhetskopiering och återställning**||
| [Konfigurera redundansprincip](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Ställer in redundansprioritet för varje region i vilken kontot är replikerat.|
|**Ansluta Azure Cosmos DB till resurser**||
| [Ansluta en webbapp till Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Skapa och anslut en Azure Cosmos DB-databas och en Azure-webbapp.|
|||

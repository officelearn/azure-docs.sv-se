---
title: Azure CLI-exempel för Azure Cosmos DB | Microsoft Docs
description: Azure CLI-exempel – Skapa och hantera Azure Cosmos DB-konton, databaser, containrar, regioner och brandväggar.
services: cosmos-db
author: SnehaGunda
manager: kfile
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.date: 11/29/2017
ms.author: sngun
ms.openlocfilehash: a6c6f61f9aa3e821ec048c1516c0e433c3c64e30
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963976"
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till Azure CLI-exempelskript för Azure Cosmos DB. Det finns referenssidor för alla Azure Cosmos DB CLI-kommandon i [referensguiden för Azure CLI](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Skapa Azure Cosmos DB-konton, databaser och containrar**||
|[Skapa ett SQL API-konto](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar ett Azure Cosmos DB API-konto, databas och container som ska användas med SQL API. |
| [Skapa ett MongoDB API-konto](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett Azure Cosmos DB MongoDB API-konto, databas och samling. |
| [Skapa ett Gremlin API-konto](scripts/create-graph-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar ett konto, en databas och en container för Gremlin API i Azure Cosmos DB. |
|**Skala Azure Cosmos DB**||
| [Skala containerns dataflöde](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ändrar dataflöde i en container.|
|[Replikera ett Azure Cosmos DB-databaskonto i flera regioner och konfigurera redundansprioriteringar](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replikerar kontodata globalt till flera regioner med en angiven redundansprioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta kontonycklar](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar de primära och sekundära huvudskrivnycklarna och de primära och sekundära skrivskyddade nycklarna för kontot.|
| [Hämta anslutningssträngen för MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar anslutningssträngen för att ansluta MongoDB-appen till Azure Cosmos DB-kontot.|
|[Återskapa kontonycklar](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Återskapar huvudnyckeln eller den skrivskyddade nyckeln för kontot.|
|[Skapa en brandvägg](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en princip för inkommande IP-åtkomstkontroll för att begränsa åtkomsten till kontot från en godkänd uppsättning datorer och/eller molntjänster.|
|**Hög tillgänglighet, haveriberedskap, säkerhetskopiering och återställning**||
|[Konfigurera redundansprincip](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Ställer in redundansprioritet för varje region i vilken kontot är replikerat.|
|**Ansluta Azure Cosmos DB till resurser**||
|[Ansluta en webbapp till Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Skapa och anslut en Azure Cosmos DB-databas och en Azure-webbapp.|
|||

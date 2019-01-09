---
title: Azure PowerShell-exempel för Azure Cosmos DB
description: Azure PowerShell-exempel – Skript som hjälper dig att skapa och hantera Azure Cosmos DB-konton.
services: cosmos-db
author: SnehaGunda
tags: azure-service-management
ms.service: cosmos-db
ms.custom: mvc
ms.topic: sample
ms.date: 10/16/2017
ms.author: sngun
ms.openlocfilehash: 480bccd73a049d2f181d5acf906882b2f9b6ccdb
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/28/2018
ms.locfileid: "53808408"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db"></a>Azure PowerShell-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till Azure PowerShell-exempelskript för Azure Cosmos DB. Just nu kan du bara hantera Azure Cosmos DB-kontot via PowerShell. Andra resurser, som databaser och containrar, kan inte hanteras via PowerShell.

| |  |
|---|---|
|**Skapa ett Azure Cosmos DB-konto**||
|[Skapa och konfigurera ett Cosmos-konto med SQL-API](scripts/create-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med SQL-API:et. |
|[Skapa och konfigurera ett Cosmos-konto med Azure Cosmos DB:s API för MongoDB](scripts/create-mongodb-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett Cosmos-konto med Azure Cosmos DB:s API för MongoDB. |
|[Skapa och konfigurera ett Cosmos-konto med Gremlin-API ](scripts/create-graph-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med Gremlin API:et. |
|[Skapa och konfigurera ett Cosmos-konto med Cassandra-API](scripts/create-and-configure-cassandra-database.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med Cassandra-API:et. |
|[Skapa och konfigurera ett Cosmos-konto med Tabell-API](scripts/create-table-database-account-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar ett enskilt Azure Cosmos DB-konto som ska användas med Table-API:et. |
|**Skala Azure Cosmos DB**||
|[Replikera ett Azure Cosmos DB-konto i flera regioner och konfigurera redundansprioriteringar](scripts/scale-multiregion-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Replikerar kontodata globalt till flera regioner med en angiven redundansprioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta kontonycklar](scripts/secure-get-account-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar de primära och sekundära huvudskrivnycklarna och de primära och sekundära skrivskyddade nycklarna för kontot.|
| [Hämta anslutningssträngen för MongoDB](scripts/secure-mongo-connection-string-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Hämtar anslutningssträngen för att ansluta MongoDB-appen till Azure Cosmos DB-kontot.|
|[Återskapa kontonycklar](scripts/secure-regenerate-key-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Återskapar huvudnyckeln eller den skrivskyddade nyckeln för kontot.|
|[Skapa en brandvägg](scripts/create-firewall-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Skapar en princip för inkommande IP-åtkomstkontroll för att begränsa åtkomsten till kontot från en godkänd uppsättning datorer och/eller molntjänster.|
|**Hög tillgänglighet, haveriberedskap, säkerhetskopiering och återställning**||
|[Konfigurera redundansprincip](scripts/ha-failover-policy-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)|Ställer in redundansprioritet för varje region i vilken kontot är replikerat.|
|||

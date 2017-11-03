---
title: "Azure CLI prover för Azure Cosmos DB | Microsoft Docs"
description: "Azure CLI-exempel - skapa och hantera Azure Cosmos DB konton, databaser, behållare, regioner och brandväggar."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 
ms.service: cosmos-db
ms.custom: mvc
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: database
ms.date: 06/07/2017
ms.author: mimig
ms.openlocfilehash: 189034b049e776a3b929930be937ec60bc1db241
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cli-samples-for-azure-cosmos-db"></a>Azure CLI-exempel för Azure Cosmos DB

Följande tabell innehåller länkar till exempel Azure CLI-skript för Azure Cosmos DB. För referenssidor för alla Azure Cosmos DB CLI-kommandon finns i den [referens för Azure CLI 2.0](https://docs.microsoft.com/cli/azure/cosmosdb).

| |  |
|---|---|
|**Skapa Azure DB som Cosmos-kontot, databas och behållare**||
|[Skapa ett DocumentDB-API, diagram och tabellen API-konto](scripts/create-database-account-collections-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en enda Azure Cosmos DB API-kontot, databas och behållare för användning med DocumentDB, diagram eller API: er för tabellen. |
| [Skapa ett konto för MongoDB-API](scripts/create-mongodb-database-account-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Skapar en enda Azure Cosmos DB MongoDB API kontot, databas och samling. |
|**Skala Azure Cosmos DB**||
| [Skala behållaren genomflöde](scripts/scale-collection-throughput-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Ändrar den etablerade througput i en behållare.|
|[Replikera Azure Cosmos DB databaskonto i flera områden och konfigurera redundans prioriteter](scripts/scale-multiregion-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Replikerar globalt kontodata i flera regioner med angivna redundans prioritet.|
|**Skydda Azure Cosmos DB**||
| [Hämta nycklar](scripts/secure-get-account-key-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar den primära och sekundära master skrivning och primära och sekundära skrivskyddade nycklar för kontot.|
| [Hämta anslutningssträngen för MongoDB](scripts/secure-mongo-connection-string-cli.md?toc=%2fcli%2fazure%2ftoc.json) | Hämtar anslutningssträngen till Anslut MongoDB-appen till ditt konto i Azure Cosmos DB.|
|[Återskapa nycklar](scripts/secure-regenerate-key-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Återskapar nyckeln master eller skrivskyddat för kontot.|
|[Skapa en brandvägg](scripts/create-firewall-cli.md?toc=%2fcli%2fazure%2ftoc.json)| Skapar en inkommande IP-principer för åtkomstkontroll för att begränsa åtkomst till kontot från en godkänd uppsättning datorer eller molntjänster.|
|**Hög tillgänglighet, katastrofåterställning, säkerhetskopiering och återställning**||
|[Konfigurera principen för växling vid fel](scripts/ha-failover-policy-cli.md?toc=%2fcli%2fazure%2ftoc.json)|Anger redundans prioriteten för varje region där kontot replikeras.|
|**Ansluta Azure Cosmos DB till resurser**||
|[Ansluta en webbapp till Azure Cosmos DB](../app-service/scripts/app-service-cli-app-service-documentdb.md?toc=%2fcli%2fazure%2ftoc.json)|Skapa och ansluta en Azure Cosmos-DB-databas och ett Azure-webbapp.|
|||

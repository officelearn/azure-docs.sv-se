---
title: Introduktion till tabell-API:et för Azure Cosmos DB
description: Läs hur du kan använda Azure Cosmos DB för att lagra och ställa frågor till massiva volymer av nyckelvärdesdata med kort svarstid med hjälp av Azure Tables API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 07/26/2019
ms.author: sngun
ms.openlocfilehash: 5b2e2c51eaa878ba0ce8bc31c001575acebe6919
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240158"
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introduktion till Azure Cosmos DB | Tabell-API

[Microsoft Azure Cosmos-DB](introduction.md) tillhandahåller tabell-API:t för program som är skrivna för Azure-tabellagring och som behöver premiumfunktioner som:

* [Nyckelfärdig global distribution](distribute-data-globally.md).
* [Dedikerat dataflöde](partition-data.md) över hela världen.
* Latensvärden på enstaka millisekunder vid 99:e percentilen.
* Garanterat hög tillgänglighet.
* Automatisk sekundär indexering.

Program som är skrivna för Azure Table Storage kan migrera till Azure Cosmos DB med hjälp av ett tabell-API och på så sätt dra nytta av premiumfunktioner, utan kodändringar. Tabell-API:t har tillgängliga klient-SDK:er för .NET, Java, Python och Node.js.

> [!IMPORTANT]
> .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) är i underhållsläge och det kommer snart att vara inaktuellt. Uppgradera till det nya .NET Standard-biblioteket [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) för att fortsätta att få de senaste funktionerna som stöds av tabell-API:et.

## <a name="table-offerings"></a>Tabellerbjudanden
Om du använder Azure Table Storage får du följande fördelar genom att byta till Azure Cosmos DB:s tabell-API:

| | Azure Table Storage | Tabell-API för Azure Cosmos DB |
| --- | --- | --- |
| Svarstid | Snabb, men inga övre gränser för svarstid. | Ensiffriga millisekundfördröjning för läsningar och skrivningar, uppbackad med <10 ms latens för läsningar och skrivningar i den 99:e percentilen, i valfri skala, var som helst i världen. |
| Dataflöde | Modell med variabelt dataflöde. Tabeller har en gräns för skalbarhet på 20 000 åtgärder/s. | Mycket skalbara med [dedikerat reserverat dataflöde per tabell](request-units.md) som understöds av serviceavtal. Konton har ingen maxgräns för dataflöde och kan hantera >10 miljoner åtgärder/s per tabell. |
| Global distribution | En enda region med en valfri läsbar sekundär läsregion för hög tillgänglighet. Du kan inte initiera redundans. | [Nyckelfärdig global distribution](distribute-data-globally.md) från en till valfritt antal regioner. Stöd för [automatisk och manuell redundans](high-availability.md) när som helst och var som helst i världen. Multi-master kapacitet att låta alla regioner acceptera skrivåtgärder. |
| Indexering | Ett primärt index för PartitionKey och RowKey. Inga sekundära index. | Automatisk och fullständig indexering på alla egenskaper som standard, utan indexhantering. |
| Söka i data | Frågekörningen använder index för primär nyckel och genomsöker annars. | Frågor kan dra nytta av automatisk indexering av egenskaper för snabba frågetider. |
| Konsekvens | Stark inom primär region. Eventuell inom sekundär region. | [Fem väldefinierade konsekvensnivåer](consistency-levels.md) för att avvägma tillgänglighet, svarstid, dataflöde och konsekvens baserat på dina programbehov. |
| Prissättning | Optimerad för lagring. | Optimerad för dataflöde. |
| Serviceavtal | 99,9% till 99,99% tillgänglighet, beroende på replikeringsstrategi. | 99.999% lästillgänglighet, 99,99 % skrivtillgänglighet på ett konto med en region och 99,999 % skrivtillgänglighet på konton med flera regioner. [Omfattande SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/) som täcker tillgänglighet, svarstid, dataflöde och konsekvens. |

## <a name="get-started"></a>Komma igång

Skapa ett nytt Azure Cosmos DB-konto på [Azure-portalen](https://portal.azure.com). Kom sedan igång med vår [Snabbstart för tabell-API med hjälp av .NET](create-table-dotnet.md). 

> [!IMPORTANT]
> Om du skapade ett tabell-API-konto under förhandsversionen ska du skapa ett [nytt tabell-API-konto](create-table-dotnet.md#create-a-database-account) som fungerar med de allmänt tillgängliga SDK:erna för API-tabellen.
>

## <a name="next-steps"></a>Nästa steg

Här följer några tips för att komma igång:
* [Skapa en .NET-app med tabell-API:t](create-table-dotnet.md)
* [Utveckla med tabell-API:t i .NET](tutorial-develop-table-dotnet.md)
* [Fråga tabelldata med tabell-API](tutorial-query-table.md)
* [Lär dig konfigurera global distribution i Azure Cosmos DB med tabell-API:t](tutorial-global-distribution-table.md)
* [Azure Cosmos DB Tabell .NET Standard SDK](table-sdk-dotnet-standard.md)
* [Azure Cosmos DB-tabell .NET SDK](table-sdk-dotnet.md)
* [Azure Cosmos DB Tabell Java SDK](table-sdk-java.md)
* [Azure Cosmos DB Tabell Node.js SDK](table-sdk-nodejs.md)
* [Azure Cosmos DB-tabell SDK för Python](table-sdk-python.md)
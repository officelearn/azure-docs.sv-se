---
title: Introduktion till Azure Cosmos DB:s tabell-API | Microsoft Docs
description: "Lär dig hur du kan använda Azure Cosmos DB för att lagra och ställa frågor till omfattande volymer av nyckelvärdedata med kort svarstid med de populära API:erna för OSS MongoDB."
services: cosmos-db
author: bhanupr
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: c184b02f46ac2ad66c6ce607967a687c82d31477
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introduktion till Azure Cosmos DB | Tabell-API

[Microsoft Azure Cosmos-DB](introduction.md) tillhandahåller tabell-API:t (förhandsversion) för program som är skrivna för Azure-tabellagring och som behöver premiumfunktioner som:

* [Nyckelfärdig global distribution](distribute-data-globally.md).
* [Dedikerat dataflöde](partition-data.md) över hela världen.
* Latensvärden på enstaka millisekunder vid 99:e percentilen.
* Garanterat hög tillgänglighet.
* [Automatisk sekundär indexering](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

Dessa program kan migrera till Azure Cosmos DB med hjälp av tabell-API:t, och på så sätt dra nytta av premiumfunktioner, utan kodändringar. Tabell-API är tillgängligt för .NET och Python.

Vi rekommenderar att du tittar på nedanstående video, där Aravind Ramachandran förklarar hur du kommer igång med tabell-API:t för Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>Tabell-API:er för premium och standard
Om du använder tabellagring får du följande fördelar genom att byta till Azure Cosmos DB:s premiumtabellförhandsversion:

| | Azure Table Storage | Azure Cosmos DB: Table Storage (förhandsversion) |
| --- | --- | --- |
| Svarstid | Snabb, men inga övre gränser för svarstid. | Ensiffrig millisekundsvarstid för läsningar och skrivningar med <10 ms svarstid för läsning och <15 ms svarstid vid skrivning vid 99:e percentilen, i valfri skala, var som helst i världen. |
| Dataflöde | Modell med variabelt dataflöde. Tabeller har en gräns för skalbarhet på 20 000 åtgärder/s. | Mycket skalbara med [dedikerat reserverat dataflöde per tabell](request-units.md) som understöds av serviceavtal. Konton har ingen maxgräns för dataflöde och kan hantera >10 miljoner åtgärder/s per tabell. |
| Global distribution | En enda region med en valfri läsbar sekundär läsregion för hög tillgänglighet. Du kan inte initiera redundans. | [Nyckelfärdig global distribution](distribute-data-globally.md) mellan 1 och 30+ regioner. Stöd för [automatisk och manuell redundans](regional-failover.md) när som helst och var som helst i världen. |
| Indexering | Ett primärt index för PartitionKey och RowKey. Inga sekundära index. | Automatisk och fullständig indexering för alla egenskaper, utan indexhantering. |
| Fråga | Frågekörningen använder index för primär nyckel och genomsöker annars. | Frågor kan dra nytta av automatisk indexering av egenskaper för snabba frågetider. Databasmotorn i Azure Cosmos DB har stöd för mängdfunktioner samt geospatial sökning och sortering. |
| Konsekvens | Stark inom primär region. Eventuell inom sekundär region. | [Fem väldefinierade konsekvensnivåer](consistency-levels.md) för att balansera tillgänglighet, svarstid, dataflöde och konsekvens baserat på dina programbehov. |
| Prissättning | Optimerad för lagring. | Optimerad för dataflöde. |
| Serviceavtal | 99,99 % tillgänglighet. | 99,99 % tillgänglighet inom en enda region och möjlighet att lägga till fler regioner för högre tillgänglighet. [Branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) med allmän tillgänglighet. |

## <a name="get-started"></a>Kom igång

Skapa ett nytt Azure Cosmos DB-konto på [Azure-portalen](https://portal.azure.com). Kom sedan igång med vår [Snabbstart för tabell-API med hjälp av .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Nästa steg

Här följer några tips för att komma igång:
* [Skapa en .NET-app med tabell-API:t](create-table-dotnet.md)
* [Utveckla med tabell-API:t i .NET](tutorial-develop-table-dotnet.md)
* [Fråga tabelldata med tabell-API](tutorial-query-table.md)
* [Lär dig konfigurera global distribution i Azure Cosmos DB med tabell-API:t](tutorial-global-distribution-table.md)
* [Azure Cosmos DB-tabell .NET API](table-sdk-dotnet.md)
* [Azure Cosmos DB-tabell SDK för Python](table-sdk-python.md)


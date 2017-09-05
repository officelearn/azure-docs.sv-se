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
ms.translationtype: HT
ms.sourcegitcommit: 1c730c65194e169121e3ad1d1423963ee3ced8da
ms.openlocfilehash: 3ccc3b176df2f0a5d864554a74508292d272bd5a
ms.contentlocale: sv-se
ms.lasthandoff: 08/30/2017

---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introduktion till Azure Cosmos DB | Tabell-API

[Azure Cosmos DB](introduction.md) innehåller tabell-API (förhandsversion) för program som skrivits för Azure Table Storage-tjänsten och behöver förstklassiga funktioner som [nyckelfärdig global distribution](distribute-data-globally.md), [dedikerat datagenomflöde](partition-data.md) världen över, ensiffrig svarstid som den 99:e percentilen, garanterat hög tillgänglighet och [automatisk sekundär indexering](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Dessa program kan migrera till Azure Cosmos DB med tabell-API utan kodändringar och dra nytta av förstklassiga funktioner.

Vi rekommenderar att du kommer igång genom att titta på nedanstående video, där Aravind Ramachandran förklarar hur du kommer igång med tabell-API:er för Azure Cosmos DB.

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Table-API-for-Azure-Cosmos-DB/player]
> 
> 

## <a name="premium-and-standard-table-apis"></a>Tabell-API:er för premium och standard
Om du använder Azure Table Storage får du följande fördelar genom att byta till Azure Cosmos DB:s ”premiumtabellförhandsversion”:

|  | Azure Table Storage | Azure Cosmos DB: Table Storage (förhandsversion) |
| --- | --- | --- |
| Svarstid | Snabb, men inga övre gränser för svarstid | Ensiffrig millisekundsvarstid för läsningar och skrivningar med <10 ms svarstid för läsning och <15 ms svarstid vid skrivning vid 99:e percentilen, i valfri skala, var som helst i världen |
| Dataflöde | modell med variabelt dataflöde. Tabeller har en gräns för skalbarhet på 20 000 åtgärder/s | Mycket skalbara med [dedikerat reserverat dataflöde per tabell](request-units.md), som understöds av serviceavtal. Konton har ingen maxgräns för dataflöde och kan hantera >10 miljoner åtgärder/s per tabell |
| Global distribution | En enda region med en valfri läsbar sekundär läsregion för HA. Du kan inte initiera växling vid fel | [Nyckelfärdig global distribution](distribute-data-globally.md) från en till 30+ regioner, stöd för [automatisk och manuell redundans](regional-failover.md) när som helst och var som helst i världen |
| Indexering | Ett primärt index för PartitionKey och RowKey. Inga sekundära index | Automatisk och fullständig indexering för alla egenskaper utan indexhantering |
| Fråga | Frågekörningen använder index för primär nyckel och genomsöker annars. | Frågor kan dra nytta av automatisk indexering av egenskaper för snabba frågetider. Databasmotorn i Azure Cosmos DB har stöd för mängdfunktioner samt geospatial sökning och sortering. |
| Konsekvens | Stark inom primär region, eventuell med sekundär region | [Fem väldefinierade konsekvensnivåer](consistency-levels.md) för att balansera tillgänglighet, svarstid, dataflöde och konsekvens baserat på dina programbehov |
| Prissättning | Optimerad för lagring  | Optimerad för dataflöde |
| Serviceavtal | 99,9% tillgänglighet | 99,99% tillgänglighet inom en enda region och möjligheten att lägga till fler regioner för högre tillgänglighet. [Branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) med allmän tillgänglighet |

## <a name="how-to-get-started"></a>Så här kommer du igång

Skapa ett Azure Cosmos DB-konto i [Azure Portal](https://portal.azure.com) och kom igång med vår [snabbstart för Table API med .NET](create-table-dotnet.md). 

## <a name="next-steps"></a>Nästa steg

Här följer några tips för att komma igång:
* [Skapa en .NET-app med tabell-API:t](create-table-dotnet.md)
* [Utveckla med tabell-API:t i .NET](tutorial-develop-table-dotnet.md)
* [Fråga tabelldata med tabell-API](tutorial-query-table.md)
* [Så här konfigurerar du global distribution i Azure Cosmos DB med tabell-API](tutorial-global-distribution-table.md)
* [Azure Cosmos DB-tabell-API SDK för .NET](table-sdk-dotnet.md)


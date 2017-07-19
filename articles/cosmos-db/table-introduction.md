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
ms.date: 06/09/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.openlocfilehash: ef57753aeeace0086c815d83600f92422996032a
ms.contentlocale: sv-se
ms.lasthandoff: 07/08/2017


---
# <a name="introduction-to-azure-cosmos-db-table-api"></a>Introduktion till Azure Cosmos DB | Tabell-API

[Azure Cosmos DB](introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos DB erbjuder [nyckelfärdig global distribution](distribute-data-globally.md), [elastisk skalning av dataflöde och lagring](partition-data.md) världen över, ensiffrig svarstid som den 99:e percentilen, [fem väldefinierade konsekvensnivåer](consistency-levels.md) och garanterat hög tillgänglighet, och allt understöds av [branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar alla data automatiskt](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att du behöver bry dig om schema- eller indexhantering. Det stöder flera modeller och dokument, nyckelvärde graf och kolumndatamodeller. 

![API för Azure Table Storage och Azure Cosmos DB](./media/table-introduction/premium-tables.png) 

Azure Cosmos-DB tillhandahåller Table API (förhandsversion) för program som behöver en nyckelvärdeslagring med flexibelt schema, förutsägbar prestanda, global distributionsplats och hög genomströmning. Tabell-API fungerar på samma sätt som Azure Table Storage, men utnyttjar fördelarna med Azure Cosmos-databassystem. 

Du kan fortsätta att använda Azure Table Storage för tabeller med hög lagring och lägre dataflödeskrav. Azure Cosmos DB introducerar stöd för lagringsoptimerade tabeller i en kommande uppdatering, och befintliga och nya Azure Table Storage-konton uppgraderas till Azure Cosmos DB.

## <a name="premium-and-standard-table-apis"></a>Tabell-API:er för premium och standard
Om du använder Azure Table Storage får du följande fördelar genom att byta till Azure Cosmos DB:s ”premiumtabellförhandsversion”:

|  | Azure Table Storage | Azure Cosmos DB: Table Storage (förhandsversion) |
| --- | --- | --- |
| Svarstid | Snabb, men inga övre gränser för svarstid | Ensiffrig millisekundsvarstid för läsningar och skrivningar med <10 ms svarstid för läsning och <15 ms svarstid vid skrivning vid 99:e percentilen, i valfri skala, var som helst i världen |
| Dataflöde | Skalbar, men inte dedikerad dataflödesmodell. Tabeller har en gräns för skalbarhet på 20 000 åtgärder/s | Mycket skalbara med [dedikerat reserverat dataflöde per tabell](request-units.md), som understöds av serviceavtal. Konton har ingen maxgräns för dataflöde och kan hantera >10 miljoner åtgärder/s per tabell |
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
* Kom igång med [Azure Cosmos DB:s Table API](create-table-dotnet.md) med hjälp av SDK för NET Table.
* Läs mer om [Global distribution med Azure Cosmos DB](distribute-data-globally.md).
* Läs mer om [etablerat dataflöde i Azure Cosmos DB](request-units.md).


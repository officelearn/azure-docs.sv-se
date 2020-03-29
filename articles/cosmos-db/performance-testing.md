---
title: Prestanda- och skalningstestning med Azure Cosmos DB
description: Lär dig hur du gör skalnings- och prestandatester med Azure Cosmos DB. Du kan sedan utvärdera funktionerna i Azure Cosmos DB för högpresterande programscenarier.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fb510c5628913fb3fa37b572c4409aee5d1028ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76313764"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Prestanda- och skalningstestning med Azure Cosmos DB

Prestanda- och skalningstestning är ett viktigt steg i programutvecklingen. För många program har databasnivån en betydande inverkan på den övergripande prestandan och skalbarheten. Därför är det en viktig komponent i prestandatestning. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är specialbyggd för elastisk skala och förutsägbar prestanda. Dessa funktioner gör det till en utmärkt passform för program som behöver en högpresterande databasnivå. 

Den här artikeln är en referens för utvecklare som implementerar prestandatestsviter för sina Azure Cosmos DB-arbetsbelastningar. Den kan också användas för att utvärdera Azure Cosmos DB för högpresterande programscenarier. Den fokuserar främst på isolerade prestandatester av databasen, men innehåller även bästa praxis för produktionsapplikationer.

När du har läst den här artikeln kan du svara på följande frågor: 

* Var hittar jag ett exempel på .NET-klientprogram för prestandatestning av Azure Cosmos DB? 
* Hur uppnår jag höga dataflödesnivåer med Azure Cosmos DB från mitt klientprogram?

För att komma igång med koden, ladda ner projektet från [Azure Cosmos DB prestandatestexempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Målet med det här programmet är att visa hur du får bästa prestanda från Azure Cosmos DB med ett litet antal klientdatorer. Målet med exemplet är inte att uppnå den högsta dataflödeskapaciteten för Azure Cosmos DB (som kan skalas utan några gränser).
> 
> 

Om du letar efter konfigurationsalternativ på klientsidan för att förbättra Azure Cosmos DB-prestanda läser du [prestandatips för Azure Cosmos DB](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Kör prestandatestprogrammet
Det snabbaste sättet att komma igång är att kompilera och köra .NET-exemplet, enligt beskrivningen i följande steg. Du kan också granska källkoden och implementera liknande konfigurationer för dina egna klientprogram.

**Steg 1:** Hämta projektet från [Azure Cosmos DB-prestandatestningsexempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)eller gaffel GitHub-databasen.

**Steg 2:** Ändra inställningarna för EndpointUrl, AuthorizationKey, CollectionThroughput och DocumentTemplate (valfritt) i App.config.

> [!NOTE]
> Innan du etablerar samlingar med högt dataflöde läser du [sidan Prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för att uppskatta kostnaderna per samling. Azure Cosmos DB fakturerar lagring och dataflöde oberoende av detta varje timme. Du kan spara kostnader genom att ta bort eller sänka dataflödet för dina Azure Cosmos-behållare efter testning.
> 
> 

**Steg 3:** Kompilera och kör konsolappen från kommandoraden. Du bör se utdata enligt följande:

    C:\Users\cosmosdb\Desktop\Benchmark>DocumentDBBenchmark.exe
    Summary:
    ---------------------------------------------------------------------
    Endpoint: https://arramacquerymetrics.documents.azure.com:443/
    Collection : db.data at 100000 request units per second
    Document Template*: Player.json
    Degree of parallelism*: -1
    ---------------------------------------------------------------------

    DocumentDBBenchmark starting...
    Found collection data with 100000 RU/s
    Starting Inserts with 100 tasks
    Inserted 4503 docs @ 4491 writes/s, 47070 RU/s (122B max monthly 1KB reads)
    Inserted 17910 docs @ 8862 writes/s, 92878 RU/s (241B max monthly 1KB reads)
    Inserted 32339 docs @ 10531 writes/s, 110366 RU/s (286B max monthly 1KB reads)
    Inserted 47848 docs @ 11675 writes/s, 122357 RU/s (317B max monthly 1KB reads)
    Inserted 58857 docs @ 11545 writes/s, 120992 RU/s (314B max monthly 1KB reads)
    Inserted 69547 docs @ 11378 writes/s, 119237 RU/s (309B max monthly 1KB reads)
    Inserted 80687 docs @ 11345 writes/s, 118896 RU/s (308B max monthly 1KB reads)
    Inserted 91455 docs @ 11272 writes/s, 118131 RU/s (306B max monthly 1KB reads)
    Inserted 102129 docs @ 11208 writes/s, 117461 RU/s (304B max monthly 1KB reads)
    Inserted 112444 docs @ 11120 writes/s, 116538 RU/s (302B max monthly 1KB reads)
    Inserted 122927 docs @ 11063 writes/s, 115936 RU/s (301B max monthly 1KB reads)
    Inserted 133157 docs @ 10993 writes/s, 115208 RU/s (299B max monthly 1KB reads)
    Inserted 144078 docs @ 10988 writes/s, 115159 RU/s (298B max monthly 1KB reads)
    Inserted 155415 docs @ 11013 writes/s, 115415 RU/s (299B max monthly 1KB reads)
    Inserted 166126 docs @ 10992 writes/s, 115198 RU/s (299B max monthly 1KB reads)
    Inserted 173051 docs @ 10739 writes/s, 112544 RU/s (292B max monthly 1KB reads)
    Inserted 180169 docs @ 10527 writes/s, 110324 RU/s (286B max monthly 1KB reads)
    Inserted 192469 docs @ 10616 writes/s, 111256 RU/s (288B max monthly 1KB reads)
    Inserted 199107 docs @ 10406 writes/s, 109054 RU/s (283B max monthly 1KB reads)
    Inserted 200000 docs @ 9930 writes/s, 104065 RU/s (270B max monthly 1KB reads)

    Summary:
    ---------------------------------------------------------------------
    Inserted 200000 docs @ 9928 writes/s, 104063 RU/s (270B max monthly 1KB reads)
    ---------------------------------------------------------------------
    DocumentDBBenchmark completed successfully.
    Press any key to exit...


**Steg 4 (vid behov):** Det dataflöde som rapporteras (RU/s) från verktyget bör vara samma eller högre än samlingens etablerade genomströmning eller en uppsättning samlingar. Om det inte är, öka DegreeOfParallelism i små steg kan hjälpa dig att nå gränsen. Om dataflödet från klientappplatåer startar du flera instanser av appen på ytterligare klientdatorer. Om du behöver hjälp med det här steget fil en supportbiljett från [Azure-portalen](https://portal.azure.com).

När du har appen igång kan du prova olika [indexeringsprinciper](index-policy.md) och [konsekvensnivåer](consistency-levels.md) för att förstå deras inverkan på dataflöde och svarstid. Du kan också granska källkoden och implementera liknande konfigurationer som dina egna testsviter eller produktionsprogram.

## <a name="next-steps"></a>Nästa steg
I den här artikeln tittade vi på hur du kan utföra prestanda- och skalningstester med Azure Cosmos DB med hjälp av en .NET-konsolapp. Mer information finns i följande artiklar:

* [Exempel på prestandatestning av Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Klientkonfigurationsalternativ för att förbättra Azure Cosmos DB-prestanda](performance-tips.md)
* [Partitionering på serversidan i Azure Cosmos DB](partition-data.md)



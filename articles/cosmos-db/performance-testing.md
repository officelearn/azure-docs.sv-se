---
title: Prestanda och skalningstester med Azure Cosmos DB
description: Lär dig hur du skala och prestandatester med Azure Cosmos DB. Du kan sedan utvärdera funktionerna i Azure Cosmos DB för högpresterande Programscenarier.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 6d2863e39b5f28c56e2b9045513aa83326d8b8c7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043200"
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Prestanda och skalningstester med Azure Cosmos DB

Testar prestanda och skalning är ett viktigt steg i programutveckling. För många program har databasnivån en betydande inverkan på övergripande prestanda och skalbarhet. Därför är det en fundamental komponent prestandatester. [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) är specifikt byggd för flexibel skalbarhet och förutsägbara prestanda. De här funktionerna gör det passade bra för program som behöver en högpresterande databasnivå. 

Den här artikeln är en referens för utvecklare implementera prestanda testpaket för sina arbetsbelastningar i Azure Cosmos DB. Det kan också användas att utvärdera Azure Cosmos DB för högpresterande Programscenarier. Det fokuserar främst på isolerade prestandatester på databasen, men innehåller också Metodtips för program i produktion.

När du har läst den här artikeln kommer du att kunna besvara följande frågor: 

* Var hittar jag ett exempelklientprogram för .NET för prestandatestning hos Azure Cosmos DB? 
* Hur jag för att uppnå hög genomströmning nivåer med Azure Cosmos DB från mitt klientprogram?

Kom igång med kod genom att hämta projektet från [exempel för Azure Cosmos DB-prestandatest](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Målet med det här programmet är att demonstrera hur du får bästa möjliga prestanda från Azure Cosmos DB med ett litet antal klientdatorer. Målet med exemplet är inte för att uppnå högsta genomflödeskapaciteten i Azure Cosmos DB (som kan skala utan några begränsningar).
> 
> 

Om du letar efter klientsidans konfigurationsalternativ för att förbättra prestanda för Azure Cosmos DB, se [Azure Cosmos DB prestandatips](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Kör programmet för prestandatest
Det snabbaste sättet att komma igång är att kompilera och kör .NET-exemplet som beskrivs i följande steg. Du kan också granska källkoden och implementera liknande konfigurationer på dina egna program.

**Steg 1:** Hämta projektet från [exempel för Azure Cosmos DB-prestandatest](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), eller Förgrena GitHub-lagringsplatsen.

**Steg 2:** Ändra inställningarna för EndpointUrl, AuthorizationKey, CollectionThroughput och DocumentTemplate (valfritt) i App.config.

> [!NOTE]
> Innan du etablerar samlingar med dataflöden måste referera till den [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/) uppskatta kostnader per samling. Azure Cosmos DB fakturerar lagringen och dataflödet separat per timme. Du kan spara pengar genom att ta bort eller sänka dataflödet för Azure Cosmos DB-samlingar efter testning.
> 
> 

**Steg 3:** Kompilera och kör konsolappen från kommandoraden. Du bör se utdata som liknar följande:

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


**Steg 4 (vid behov):** Dataflödet rapporterade (RU/s) från verktyget ska vara samma eller högre än det etablerade dataflödet i samlingen eller en uppsättning samlingar. Om den inte öka DegreeOfParallelism i små steg kan hjälpa dig att nå gränsen. Om dataflödet från klientappen högplaåter, starta flera instanser av appen på ytterligare datorer. Om du behöver hjälp med det här e- askcosmosdb@microsoft.com eller öppna ett supportärende från den [Azure-portalen](https://portal.azure.com).

När du har appen körs kan du prova olika [indexeringsprinciper](index-policy.md) och [konsekvensnivåer](consistency-levels.md) att förstå deras inverkan på dataflöde och svarstid. Du kan också granska källkoden och implementera liknande konfigurationer till dina egna testpaket eller program i produktion.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har tittat vi på hur du kan utföra prestanda- och skalningstester med Azure Cosmos DB med hjälp av en .NET-konsolapp. Mer information finns i följande artiklar:

* [Azure Cosmos DB prestandatest exemplet](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Klienten konfigurationsalternativ för att förbättra prestanda för Azure Cosmos DB](performance-tips.md)
* [Serversidan partitionering i Azure Cosmos DB](partition-data.md)



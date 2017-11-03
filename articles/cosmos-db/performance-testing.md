---
title: Azure DB Cosmos skala och prestandatester | Microsoft Docs
description: "Lär dig hur du utföra skalnings- och prestandatester med Azure Cosmos DB"
keywords: Prestandatestning
services: cosmos-db
author: arramac
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: f4c96ebd-f53c-427d-a500-3f28fe7b11d0
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2017
ms.author: arramac
ms.openlocfilehash: d52df1d1f9a29a6fc2a7a3a5e7a6d9fdeaa865e3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="performance-and-scale-testing-with-azure-cosmos-db"></a>Prestanda och skalning testa med Azure Cosmos DB
Prestanda och skalningstester är ett viktigt steg programutveckling. För många program har på databasnivå en betydande inverkan på övergripande prestanda och skalbarhet. Därför är en kritisk komponent i prestandatester. [Azure Cosmos-DB](https://azure.microsoft.com/services/cosmos-db/) är specialbyggt för elastisk skalbarhet och förutsägbar prestanda. Dessa funktioner är det en bra val för program som behöver en hög prestanda databasnivå. 

Den här artikeln är en referens för utvecklare som implementerar prestanda testpaket för sina Azure DB som Cosmos-arbetsbelastningar. Den kan även användas för att utvärdera Azure Cosmos DB för högpresterande Programscenarier. Den fokuserar huvudsakligen på isolerade prestandatester på databasen, men det innehåller även rekommenderade metoder för program i produktion.

När du har läst den här artikeln kommer du att kunna svara på följande frågor: 

* Var hittar jag exempel klientprogrammet för .NET för prestandatestning av Azure Cosmos DB 
* Hur uppnå hög genomströmning nivåer med Azure Cosmos DB från min klientprogrammet?

Hämta projektet från att komma igång med kod [Azure Cosmos DB prestandatestning exempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark). 

> [!NOTE]
> Målet med det här programmet är att demonstrera hur att få bästa möjliga prestanda från Azure Cosmos-databasen med ett litet antal klientdatorer. Målet för det här exemplet är inte för att uppnå högsta genomflödeskapaciteten i Azure Cosmos DB (som kan skalas utan några gränser).
> 
> 

Om du letar efter klientsidan konfigurationsalternativ för att förbättra prestanda för Azure Cosmos DB [Azure Cosmos DB prestandatips](performance-tips.md).

## <a name="run-the-performance-testing-application"></a>Kör programmet för prestandatestning
Det snabbaste sättet att komma igång är att kompilera och köra .NET-exempel som beskrivs i följande steg. Du kan också granska källkoden och implementera liknande konfigurationer på egna klientprogram.

**Steg 1:** hämta projektet från [Azure Cosmos DB prestandatestning exempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark), eller duplicera GitHub-lagringsplatsen.

**Steg 2:** ändra inställningarna för EndpointUrl och AuthorizationKey, CollectionThroughput DocumentTemplate (valfritt) i App.config.

> [!NOTE]
> Innan du etablerar samlingar med hög genomströmning måste referera till den [priser sidan](https://azure.microsoft.com/pricing/details/cosmos-db/) att uppskatta kostnader per samling. Azure Cosmos databaslagring fakturor och genomströmning oberoende på timbasis. Du kan spara kostnader genom att radera eller sänker genomflödet i Azure DB som Cosmos-samlingar efter testning.
> 
> 

**Steg 3:** kompilera och köra konsolapp-från kommandoraden. Du bör se utdata som liknar följande:

    C:\Users\documentdb\Desktop\Benchmark>DocumentDBBenchmark.exe
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


**Steg 4 (vid behov):** genomflödet rapporterade (RU/s) från verktyget ska vara samma eller högre än det tillhandahållna dataflödet i mängden. Om det inte kan öka DegreeOfParallelism i små steg hjälpa dig att nå gränsen. Om genomströmning från din klient högplaåter starta flera instanser av appen på ytterligare datorer. Om du behöver hjälp med det här steget e- askcosmosdb@microsoft.com eller filen ett supportärende från den [Azure-portalen](https://portal.azure.com).

När du har appen körs, kan du försöka olika [indexering principer](indexing-policies.md) och [konsekvensnivåer](consistency-levels.md) att förstå deras inverkan på genomflöde och svarstid. Du kan också granska källkoden och implementera liknande konfigurationer till testpaket eller program i produktion.

## <a name="next-steps"></a>Nästa steg
I den här artikeln vi har tittat på hur du kan utföra prestanda och skalning testa med Azure Cosmos DB med hjälp av en .NET-konsolapp. Mer information finns i följande artiklar:

* [Azure DB Cosmos prestandatestning exempel](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/documentdb-benchmark)
* [Klienten konfigurationsalternativ för att förbättra prestanda för Azure Cosmos DB](performance-tips.md)
* [Serversidan partitionering i Azure Cosmos DB](partition-data.md)



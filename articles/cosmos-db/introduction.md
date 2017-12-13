---
title: Introduktion till Azure Cosmos DB | Microsoft Docs
description: "Läs om Azure Cosmos DB. Den här globalt distribuerade databasen med flera modeller har skapats för låg svarstid, elastisk skalbarhet och hög tillgänglighet."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: mimig
ms.custom: mvc
ms.openlocfilehash: f34790ad670b488159e945be9bf4ba378cc5e94a
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="welcome-to-azure-cosmos-db"></a>Välkommen till Azure Cosmos DB

Azure Cosmos DB är Microsofts globalt distribuerade databas för flera datamodeller. Med ett knappklick, låter Azure DB Cosmos dig att elastiskt och oberoende skala dataflöde och lagring över valfritt antal av Azures geografiska regioner. Det erbjuder garantier när det gäller dataflöde, svarstider, tillgänglighet och konsekvens med omfattande [serviceavtal](https://aka.ms/acdbsla) (SLA:er) något som ingen annan databastjänst kan erbjuda. Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

![Azure Cosmos DB är Microsofts globalt distribuerade databastjänst med elastisk utskalning, garanterat låga svarstider, fem konsekvensmodeller och omfattande garanterade serviceavtal](./media/introduction/azure-cosmos-db.png)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

## <a name="key-capabilities"></a>De viktigaste funktionerna
Som en globalt distribuerad databastjänst erbjuder Azure Cosmos DB följande funktioner för att hjälpa dig att bygga skalbara och högdynamiska program:

* **Nyckelfärdig global distribution**
    * Du kan [distribuera dina data](distribute-data-globally.md) till valfritt antal [Azure-regioner](https://azure.microsoft.com/regions/) genom att [klicka på en knapp](tutorial-global-distribution-documentdb.md). På så sätt kan du placera dina data där dina användare finns, och säkerställa minsta möjliga tidsfördröjning till dina kunder. 
    * Med hjälp av Azure Cosmos DB som har flera API:er registrerar appen alltid var den närmaste regionen finns och skickar begäranden till närmaste datacenter. Allt detta är möjligt utan konfigurationsändringar. Du ställer in skrivregion och så många skrivskyddade regioner som du vill och resten hanteras åt dig.

* **Flera datamodeller och populära API:er för att komma åt och fråga efter data**
    * En atom-postsekvensbaserad (ARS) datamodell som Azure Cosmos DB bygger på internt stöd för flera datamodeller, inklusive men inte begränsat till dokument, diagram, nyckelvärde, tabeller och kolumndatamodeller.
    * API:er för följande datamodeller stöds med SDK:er som är tillgängliga på flera språk:
        * [DocumentDB API](documentdb-introduction.md): En JSON-databasmotor utan schema med SQL-frågemöjligheter.
        * [MongoDB API](mongodb-introduction.md): En MongoDB-databastjänst som byggts ovanpå Cosmos DB. Kompatibel med befintliga bibliotek, drivrutiner, verktyg och program för MongoDB.
        * [Table API](table-introduction.md): En databastjänst med nyckelvärde som har byggts för att tillhandahålla premiumfunktioner för Azure Table Storage-program.
        * [Graph (Gremlin) API](graph-introduction.md): En grafdatabastjänst som byggts enligt [Apache TinkerPop-specifikationen](http://tinkerpop.apache.org/).
        * [Cassandra API](cassandra-introduction.md): Ett nyckel-/värdelager som byggts på [Apache Cassandra](https://cassandra.apache.org/)-implementeringen. 
        * Ytterligare datamodeller kommer snart!

* **Skala elastiskt dataflöde och lagring på begäran, globalt**
    * Skala enkelt databasflödet med [sekundprecision](request-units.md) och ändra inställningarna när som helst. 
    * Skala lagringsutrymmet [transparent och automatiskt](partition-data.md) för att hantera utrymmesbehoven nu och för all framtid.

* **Bygg högdynamiska och verksamhetskritiska program**
    * Azure Cosmos DB garanterar sina kunder svarstider från slutpunkt till slutpunkt som ligger inom den 99:e percentilen. 
    * För ett typiskt 1 KB-objekt, garanterar Cosmos DB en svarstid på läsningar från slutpunkt till slutpunkt på under 10 ms och indexerade skrivningar under 15 ms i den 99:e percentilen, inom samma Azure-region. Median-svarstiderna är betydligt lägre (under 5 ms).

* **Se till att tillgängligheten alltid är så hög som möjligt**
    * Serviceavtal med 99,99 % tillgänglighet för alla konton med tillgång till en eller flera regioner med konsekvensmodellen ”relaxed” (avslappnad). 99,999 % läsningstillgänglighet för alla databaskonton med tillgång till flera regioner.
    * Distribuera till valfritt antal [Azure-regioner](https://azure.microsoft.com/regions) för högre tillgänglighet.
    * [Simulera ett fel](regional-failover.md) i en eller flera regioner med garantier om noll dataförlust. 

* **Skriv globalt distribuerade program, på rätt sätt**
    * Fem [konsekvensmodeller](consistency-levels.md) ger ett spektrum av stark SQL-liknande konsekvens hela vägen till NoSQL-liknande eventuell konsekvens och allt däremellan. 
  
* **Pengarna tillbaka-garanti**
    * Dina data kommer dit de ska snabbt, eller pengarna tillbaka. 
    * [Serviceavtal](https://aka.ms/acdbsla) för tillgänglighet, svarstid, dataflöde och konsekvens. 

* **Ingen hantering av databasscheman/-index**
    * Sluta oroa dig för att hålla dina databasscheman och -index synkroniserade med schemat för ditt program. Vi är schema-fria. 
    * Azure Cosmos DB:s databasmotor är fullständigt schemaoberoende. Den indexerar automatiskt alla data den tar in utan att kräva något schema eller index och hanterar frågor blixtsnabbt. 

* **Låg totalkostnad**
    * Fem till tio gånger [mer kostnadseffektivt](https://aka.ms/cosmos-db-tco-paper) än en icke-hanterad lösning.
    * Tre gånger billigare än DynamoDB.

## <a name="capability-comparison"></a>Jämförelse av funktioner

Azure Cosmos DB erbjuder de bästa funktionerna för relations- och icke-relationsdatabaser.

| Funktioner | Relationsdatabaser   | Icke-relationella databaser (NoSQL) |    Azure Cosmos DB |
| --- | --- | --- | --- |
| Global distribution | Nej | Nej | Ja, nyckelfärdig distribution i fler än 30 områden, med flera API: er|
| Horisontell skalning | Nej | Ja | Ja, oberoende skalning av lagring och dataflöde är möjligt | 
| Svarstidsgarantier | Nej | Ja | Ja, 99 % läsningar på < 10 ms och skrivningar på < 15 ms | 
| Hög tillgänglighet | Nej | Ja | Ja, Cosmos DB är alltid aktiverat, har PACELC-kompromisser och innehåller alternativ för automatisk och manuell redundans vid fel|
| Datamodell + API | Relations + SQL | Multi-modell + OSS API | Multi-modell + SQL + OSS API (mer kommer snart) |
| Serviceavtal | Ja | Nej | Ja, omfattande serviceavtal för svarstider, dataflöde, konsekvens och tillgänglighet |

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Lösningar som har nytta av Azure Cosmos DB

Alla [webb-, mobil-, spel- och IoT-tillämpningar](use-cases.md) som behöver hantera stora mängder läs- och skrivoperationer i [global](distribute-data-globally.md) skala med korta svarstider för en mängd olika data drar nytta av Azure Cosmos DB:s [garanterade](https://azure.microsoft.com/support/legal/sla/cosmos-db/) tillgänglighet, höga genomströmning, korta svarstider och justerbara konsekvenskontroll. Lär dig mer om hur CosmosDB kan användas på [IoT och telematik](use-cases.md#iot-and-telematics), [Handel och marknadsföring](use-cases.md#retail-and-marketing), [Spel](use-cases.md#gaming) och [Webb- och mobilprogram](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Nästa steg
Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Kom igång med DocumentDB-API:n för Azure Cosmos DB](create-documentdb-dotnet.md)
* [Kom igång med MongoDB-API:n för Azure Cosmos DB](create-mongodb-nodejs.md)
* [Kom igång med Graph-API:n för Azure Cosmos DB](create-graph-dotnet.md)
* [Kom igång med Table-API:n för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

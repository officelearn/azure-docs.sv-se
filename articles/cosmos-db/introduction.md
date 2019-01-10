---
title: Introduktion till Azure Cosmos DB
description: Läs om Azure Cosmos DB. Den här globalt distribuerade databasen med flera modeller har skapats för låg svarstid, elastisk skalbarhet och hög tillgänglighet, och den ger inbyggt stöd för NoSQL-data.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: overview
ms.date: 12/18/2018
ms.author: sngun
ms.openlocfilehash: b384bc51ac371ef75f5128c92f7e4b8d7f45ecc6
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034955"
---
# <a name="welcome-to-azure-cosmos-db"></a>Välkommen till Azure Cosmos DB

Dagens program måste alltid vara mycket responsiva och ständigt online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Programmen behöver kunna svara i realtid på stora förändringar av användningen vid hög belastning, lagra ständigt ökande datavolymer och göra dessa data tillgängliga för användare på millisekunder.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Med ett knappklick, låter Azure DB Cosmos dig att elastiskt och oberoende skala dataflöde och lagring över valfritt antal av Azures geografiska regioner. Du kan elastiskt skala dataflöde och lagring och dra fördel av snabb dataåtkomst på ensiffriga millisekunder med hjälp av din favorit-API bland SQL, MongoDB, Cassandra, Tables eller Gremlin. Cosmos DB tillhandahåller omfattande [serviceavtal](https://aka.ms/acdbsla) (SLA:er) för garantier beträffande dataflöde, svarstid, tillgänglighet och konsekvens, något som ingen annan databastjänst kan erbjuda.

Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB är Microsofts globalt distribuerade databastjänst med elastisk utskalning, garanterat låga svarstider, fem konsekvensmodeller och omfattande garanterade serviceavtal](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="turnkey-global-distribution"></a>Nyckelfärdig global distribution

Med Cosmos DB kan du skapa mycket responsiva program med hög tillgänglighet, runt om i världen. Cosmos DB replikerar data transparent oavsett var dina användare finns, så att användarna kan interagera med en replik av de data som är närmast dem.

Med Cosmos DB kan du lägga till eller ta bort Azure-regioner på ditt Cosmos-konto när som helst genom att klicka på en knapp. Cosmos DB replikerar sömlöst dina data till alla regioner som är associerade med ditt Cosmos-konto samtidigt som ditt program fortsätter att ha hög tillgänglighet tack vare tjänstens funktioner för kunna vara värd för flera webbplatser.

Mer information finns i artikeln om [global distribution](distribute-data-globally.md).

### <a name="always-on"></a>Alltid ”på”

Tack vare djupgående integrering med Azure-infrastrukturen och [transparent replikering av flera original](global-dist-under-the-hood.md), tillhandahåller Cosmos DB en [hög tillgänglighet](high-availability.md) på 99,999 % för både läsning och skrivning. Cosmos DB ger dig också möjligheten att anropa regional redundans för Cosmos-kontot programmatiskt (eller via portalen). Med den här funktionen kan du säkerställa att resten av programmet också är utformat för att redundansväxla vid regionalt haveri om en Cosmos-databas utsätts för automatisk redundansväxling.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastisk skalning av dataflöde och lagringsutrymme, runt om i världen

Cosmos DB är utformat med transparent horisontell partitionering och replikering av flera original och erbjuder oöverträffad elastisk skalbarhet för skrivning och läsning över hela världen. Du kan elastiskt skala upp från tusentals till hundratals miljoner begäranden/sekund i hela världen med ett enda API-anrop och endast betala för det dataflöde (och den lagring) som du använder. Den här funktionen hjälper dig att hantera oväntade toppar i arbetsbelastningar utan att behöva överetablera för topparna. Se avsnitten om [partitionering i Cosmos DB](partitioning-overview.md), [etablerat dataflöde för containrar och databaser](set-throughput.md) och [skalning av etablerat dataflöde globalt](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garanterad kort svarstid vid 99:e percentil, runt om i världen

Med Cosmos DB kan du skapa mycket responsiva och världsomfattande program. Med sitt nya protokoll för replikering av flera original och [skrivoptimerad databasmotor utan spärr](index-policy.md) garanterar Cosmos DB mindre än 10 ms svarstid för både läsning och (indexerad) skrivning på den 99:e percentilen världen över. Den här funktionen möjliggör kontinuerlig inmatning av data och blixtsnabba frågor för mycket responsiva appar.

### <a name="precisely-defined-multiple-consistency-choices"></a>Ett flertal exakt definierade konsekvensval

Du behöver inte längre göra extrema [avvägningar mellan konsekvens, tillgänglighet, svarstid och programmerbarhet](consistency-levels-tradeoffs.md). Cosmos DB:s protokoll för replikering av flera original är noggrant utformat för att erbjuda [fem väldefinierade konsekvensnivåalternativ](consistency-levels.md) – stark, begränsad föråldring, konsekvens-prefix, session och slutlig – för en intuitiv programmeringsmodell med kort svarstid och hög tillgänglighet för ditt globalt distribuerade program.

### <a name="no-schema-or-index-management"></a>Ingen hantering av schema eller index

Att hålla databasschema och index synkroniserade med ett programs schema är särskilt svårt för globalt distribuerade appar. Men med Cosmos DB behöver du inte hantera scheman eller index. Databasmotorn är fullständigt schemaoberoende.  Eftersom ingen schema- eller indexhantering krävs, behöver du inte heller bekymra dig om programdriftstopp när du migrerar scheman. Cosmos DB [indexerar automatiskt alla data](index-policy.md) – varken scheman eller index krävs – och hanterar frågor snabbt.

### <a name="battle-tested-database-service"></a>Beprövad databastjänst

Cosmos DB är en grundläggande tjänst på Azure. I nästan tio år har Cosmos DB använts av många av Microsofts produkter för uppdragskritiska tillämpningar i global skala, som Skype, Xbox, Office 365, Azure och många andra. Idag är Cosmos DB en av de snabbast växande tjänsterna på Azure och används av många externa kunder och program som kräver elastisk skalning och/eller nyckelfärdig replikering av flera original på flera datacenter/i flera regioner för kort svarstid och hög tillgänglighet för både läsning och skrivning.

### <a name="ubiquitous-regional-presence"></a>Ständig regional närvaro

Cosmos DB är tillgängligt i alla Azure-regioner över hela världen, inklusive 54 + regioner i offentliga moln, Azure China 21Vianet, Azure Germany, Azure Government och Azure Government for Department of Defense (DoD). Se [Cosmos DB:s regionala närvaro](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Säker som standard och företagsredo

Cosmos DB är certifierat för en [mängd olika efterlevnadsstandarder](compliance.md). Dessutom krypteras alla data i Cosmos DB både i vila och i rörelse. Cosmos DB erbjuder auktorisering på radnivå och följer stränga säkerhetskrav.

### <a name="significant-tco-savings"></a>Betydande besparingar på den totala ägandekostnaden

Eftersom Cosmos DB är en helt hanterad tjänst, behöver du inte längre hantera och köra komplexa distributioner och uppgraderingar av din databasprogramvara på ett flertal datacenter eller betala för support, licenser och åtgärder. Se avsnittet om att [optimera kostnader med Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Branschledande omfattande serviceavtal

Cosmos DB är den första och enda tjänsten som erbjuder [branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) med 99,999 % hög tillgänglighet, svarstid för läsning och skrivning på 99:e percentilen, garanterat dataflöde och konsekvens.

### <a name="apache-spark--cosmos-db--operational-analytics-at-global-scale"></a>Apache Spark + Cosmos DB = driftanalyser i realtid på global skala

Du kan köra [Apache Spark](spark-connector.md) på data som lagras i Cosmos DB. Med den här funktionen kan du göra du göra driftanalyser i realtid med kort svarstid i global skala utan att påverka transaktionsbelastningar som arbetar direkt mot Cosmos DB.

### <a name="develop-applications-for-cosmos-db-using-popular-nosql-apis"></a>Utveckla program för Cosmos DB med populära NoSQL API:er

Cosmos DB erbjuder ett urval av API:er för att uppdatera och fråga mot data som lagras i Cosmos-databasen. Som standard [ kan du använda SQL](how-to-sql-query.md) för att uppdatera och fråga mot data i Cosmos-databasen.

Cosmos DB implementerar också trådprotokoll för [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) och [Azure Table Storage](table-introduction.md) direkt i tjänsten. På så vis kan du rikta klienters drivrutiner (och verktyg) för de vanligt förekommande NoSQL-API:erna direkt till din Cosmos-databas. Tack vare stödet av trådprotokoll för vanliga NoSQL-API:er kan du göra följande med Cosmos DB:

* Migrera ditt program enkelt till Cosmos DB samtidigt som du bevarar betydande delar av din programlogik.
* Hålla ditt program portabelt och förbli molnleverantörsoberoende.
* Skaffa branschledande, ekonomiskt uppbackade serviceavtal för vanliga NoSQL-API:er. 
* Skala det etablerade dataflödet och lagringen elastiskt för dina databaser utifrån dina behov och betala bara för det dataflöde och lagringsutrymme du behöver. Detta medför betydande kostnadsbesparingar.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Lösningar som har nytta av Azure Cosmos DB

Alla [webb-, mobil-, spel- och IoT-program](use-cases.md) som behöver hantera stora mängder data-, läs- och skrivoperationer i [global](distribute-data-globally.md) skala med svarstider i nära realtid för en mängd olika data drar nytta av Azure Cosmos DB:s [garanterade](https://azure.microsoft.com/support/legal/sla/cosmos-db/) höga tillgänglighet, snabba dataflöde, korta svarstider och justerbara konsekvenskontroll. Lär dig mer om hur Azure Cosmos DB kan användas på [IoT och telematik](use-cases.md#iot-and-telematics), [Handel och marknadsföring](use-cases.md#retail-and-marketing), [Spel](use-cases.md#gaming) och [Webb- och mobilprogram](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Nästa steg

Läs mer om Cosmos DB:s funktioner för [global distribution](distribute-data-globally.md) och [partitionering](partitioning-overview.md).

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

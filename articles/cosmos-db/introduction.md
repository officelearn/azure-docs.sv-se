---
title: Introduktion till Azure Cosmos DB
description: Läs om Azure Cosmos DB. Den här globalt distribuerade databasen med flera modeller har skapats för låg svarstid, elastisk skalbarhet och hög tillgänglighet, och den ger inbyggt stöd för NoSQL-data.
author: rimman
ms.service: cosmos-db
ms.topic: overview
ms.date: 04/08/2019
ms.author: rimman
ms.openlocfilehash: a965db4334e88d032f767bd7b855dea6f3ff174f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61042977"
---
# <a name="welcome-to-azure-cosmos-db"></a>Välkommen till Azure Cosmos DB

Dagens program måste alltid vara mycket responsiva och ständigt online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Programmen behöver kunna svara i realtid på stora förändringar av användningen vid hög belastning, lagra ständigt ökande datavolymer och göra dessa data tillgängliga för användare på millisekunder.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Genom att klicka på en knapp, Cosmos DB ger dig möjlighet att Elastiskt och skala oberoende dataflöde och lagring över valfritt antal Azure-regioner över hela världen. Du kan Elastiskt skala dataflöde och lagring och dra nytta av snabb och enkel ensiffriga dataåtkomst med hjälp av din favorit-API, inklusive SQL, MongoDB, Cassandra, tabeller eller Gremlin. Cosmos DB tillhandahåller omfattande [serviceavtal](https://aka.ms/acdbsla) (SLA) för dataflöde, svarstider, tillgänglighet och konsekvensgarantier, något som ingen annan databastjänst erbjuder.

Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB är Microsofts globalt distribuerade databastjänst med elastisk utskalning, garanterat låga svarstider, fem konsekvensmodeller och omfattande garanterade serviceavtal](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="turnkey-global-distribution"></a>Nyckelfärdig global distribution

Med Cosmos DB kan du skapa mycket responsiva program med hög tillgänglighet, runt om i världen. Cosmos DB replikerar data transparent oavsett var dina användare finns, så att användarna kan interagera med en replik av data som är närmast dem.

Med Cosmos DB kan du lägga till eller ta bort Azure-regioner på ditt Cosmos-konto när som helst genom att klicka på en knapp. Cosmos DB sömlöst dina data replikeras till alla regioner som är associerat med ditt Cosmos medan programmet fortsätter att vara med hög tillgänglighet tack vare att den *flera värdar* funktionerna i tjänsten. Mer information finns i artikeln om [global distribution](distribute-data-globally.md).

### <a name="always-on"></a>Alltid på

Tack vare djupgående integrering med Azure-infrastrukturen och [transparent Multi-Master-replikering](global-dist-under-the-hood.md), Cosmos DB tillhandahåller [99,999% hög tillgänglighet](high-availability.md) för både läsningar och skrivningar. Cosmos DB ger dig också möjligheten att anropa regional redundans för Cosmos-kontot programmatiskt (eller via portalen). Den här funktionen hjälper till att säkerställa att ditt program är utformat för att växling vid fel om regionalt haveri.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastisk skalning av dataflöde och lagringsutrymme, runt om i världen

Cosmos DB är utformat med transparent horisontell partitionering och replikering av flera original och erbjuder oöverträffad elastisk skalbarhet för skrivning och läsning över hela världen. Du kan elastiskt skala upp från tusentals till hundratals miljoner begäranden/sekund i hela världen med ett enda API-anrop och endast betala för det dataflöde (och den lagring) som du använder. Den här funktionen hjälper dig att hantera oväntade toppar i arbetsbelastningar utan att behöva överetablera för topparna. Mer information finns i [partitionering i Cosmos DB](partitioning-overview.md), [etablerat dataflöde på behållare och databaser](set-throughput.md), och [skala dataflöde globalt](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garanterad kort svarstid vid 99:e percentil, runt om i världen

Med Cosmos DB kan du skapa mycket responsiva och världsomfattande program. Med sitt nya protokoll för replikering av flera original och [skrivoptimerad databasmotor utan spärr](index-policy.md) garanterar Cosmos DB mindre än 10 ms svarstid för både läsning och (indexerad) skrivning på den 99:e percentilen världen över. Den här funktionen möjliggör kontinuerlig inmatning av data och blixtsnabba frågor för mycket responsiva appar.

### <a name="precisely-defined-multiple-consistency-choices"></a>Ett flertal exakt definierade konsekvensval

När skapa globalt distribuerade program i Cosmos DB, har du inte längre att göra extrem [rätt balans mellan konsekvens, tillgänglighet, svarstid och dataflöde](consistency-levels-tradeoffs.md). Cosmos DB: s Multi-Master-replikering protocol noggrant är avsett att erbjuda [fem väldefinierade konsekvensval](consistency-levels.md) - *stark*, *begränsad föråldring*, *session*, *konsekvent prefix*, och *slutlig* – för en intuitiv programmeringsmodell med kort svarstid och hög tillgänglighet för dina globalt distribuerade program.

### <a name="no-schema-or-index-management"></a>Ingen hantering av schema eller index

Att hålla databasschema och index synkroniserade med ett programs schema är särskilt svårt för globalt distribuerade appar. Med Cosmos DB kan behöver du inte bry dig om schema- eller indexhantering. Databasmotorn är fullständigt schemaoberoende.  Eftersom ingen schema- eller indexhantering krävs, behöver du inte heller bekymra dig om programdriftstopp när du migrerar scheman. Cosmos DB [indexerar automatiskt alla data](index-policy.md) och hanterar frågor snabbt.

### <a name="battle-tested-database-service"></a>Beprövad databastjänst

Cosmos DB är en grundläggande tjänst i Azure. För nästan ett årtionde har Cosmos DB använts av många av Microsofts produkter för verksamhetskritiska program på global nivå, inklusive Skype, Xbox, Office 365, Azure och många andra. Idag, är Cosmos DB en av de snabbast växande tjänsterna på Azure, som används av många externa kunder och verksamhetskritiska program som kräver elastisk skalning, nyckelfärdig global distribution, Multi-Master-replikering för låg latens och hög tillgänglighet för både läsningar och skrivningar.

### <a name="ubiquitous-regional-presence"></a>Ständig regional närvaro

Cosmos DB är tillgängligt i alla Azure-regioner över hela världen, inklusive 54 + regioner i offentligt moln [Azure Kina 21Vianet](https://www.azure.cn/en-us/), Azure Germany, Azure Government och Azure Government för Department of Defense (DoD). Se [Cosmos DB:s regionala närvaro](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Säker som standard och företagsredo

Cosmos DB är certifierat för en [mängd olika efterlevnadsstandarder](compliance.md). Dessutom krypteras alla data i Cosmos DB både i vila och i rörelse. Cosmos DB erbjuder auktorisering på radnivå och följer stränga säkerhetskrav.

### <a name="significant-tco-savings"></a>Betydande besparingar på den totala ägandekostnaden

Eftersom Cosmos DB är en helt hanterad tjänst kan behöver du inte längre hantera och driva komplexa flera datacenter distributioner och uppgraderingar av din databasprogramvara, betala för support, licenser eller åtgärder eller behöva etablera din databas för den högsta arbetsbelastningen. Mer information finns i [optimera kostnader med Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Branschledande omfattande serviceavtal

Cosmos DB är den första och enda tjänsten som erbjuder [branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) med 99,999 % hög tillgänglighet, svarstid för läsning och skrivning på 99:e percentilen, garanterat dataflöde och konsekvens.

### <a name="globally-distributed-operational-analytics-with-spark"></a>Globalt distribuerade operativ analys med Spark

Du kan köra [Spark](spark-connector.md) direkt på data som lagras i Cosmos DB. Med den här funktionen kan du göra du göra driftanalyser i realtid med kort svarstid i global skala utan att påverka transaktionsbelastningar som arbetar direkt mot Cosmos DB. Mer information finns i [distribueras globalt verksamhetsanalyser](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-nosql-apis"></a>Utveckla program i Cosmos DB med populära NoSQL APIs

Cosmos DB erbjuder ett urval av API: er för att arbeta med dina data som lagras i Cosmos-databasen. Som standard [du kan använda SQL](how-to-sql-query.md) (en kärna API) för att fråga din Cosmos-databas. Cosmos DB implementerar också API: er för [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) och [Azure Table Storage](table-introduction.md). Du kan peka klienten drivrutiner (och verktyg) för vanliga NoSQL (t.ex. MongoDB, Cassandra, Gremlin) direkt till din Cosmos-databas. Genom att stödja wire-protokoll för vanliga NoSQL APIs kan Cosmos DB du:

* Migrera ditt program enkelt till Cosmos DB samtidigt som du bevarar betydande delar av din programlogik.
* Hålla ditt program portabelt och förbli molnleverantörsoberoende.
* Hämta en fullständigt hanterad molntjänst med branschens ledande, ekonomisk serviceavtal för vanliga NoSQL APIs. 
* Skala det etablerade dataflödet och lagringen elastiskt för dina databaser utifrån dina behov och betala bara för det dataflöde och lagringsutrymme du behöver. Detta medför betydande kostnadsbesparingar.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Lösningar som har nytta av Azure Cosmos DB

Alla [webb-, mobil, spel och IoT-program](use-cases.md) som behöver hantera stora mängder data-, Läs- och skrivoperationer i en [global skala](distribute-data-globally.md) med svarstider i nära realtid för en mängd olika data drar nytta av Cosmos DB: s [garanterat hög tillgänglighet](https://azure.microsoft.com/support/legal/sla/cosmos-db/), högt dataflöde, svarstider och justerbara konsekvenskontroll. Lär dig mer om hur Azure Cosmos DB kan användas för att skapa [IoT och telematik](use-cases.md#iot-and-telematics), [handel och marknadsföring](use-cases.md#retail-and-marketing), [spel](use-cases.md#gaming) samt [webb- och mobilprogram](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Nästa steg

Läs mer om Cosmos DB viktiga begrepp [nyckelfärdig global distribution](distribute-data-globally.md) och [partitionering](partitioning-overview.md) och [etablerat dataflöde](request-units.md).

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

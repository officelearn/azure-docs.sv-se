---
title: Introduktion till Azure Cosmos DB
description: Läs om Azure Cosmos DB. Den här globalt distribuerade databasen med flera modeller har skapats för låg svarstid, elastisk skalbarhet och hög tillgänglighet, och den ger inbyggt stöd för NoSQL-data.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: overview
ms.date: 10/23/2019
ms.openlocfilehash: 2a09d5af013e804f33327855fb7b9f2104bc225c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240396"
---
# <a name="welcome-to-azure-cosmos-db"></a>Välkommen till Azure Cosmos DB

Dagens program måste alltid vara mycket responsiva och ständigt online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Programmen behöver kunna svara i realtid på stora förändringar av användningen vid hög belastning, lagra ständigt ökande datavolymer och göra dessa data tillgängliga för användare på millisekunder.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Med ett klick på en knapp kan du med Cosmos DB elastiskt och självständigt skala dataflöde och lagring över valfritt antal Azure-regioner över hela världen. Du kan elastiskt skala dataflöde och lagring och dra nytta av snabb, ensiffrig millisekund dataåtkomst med hjälp av ditt favorit-API, inklusive: SQL, MongoDB, Cassandra, Tables eller Gremlin. Cosmos DB tillhandahåller omfattande [servicenivåavtal](https://aka.ms/acdbsla) (SLA) för dataflöde, svarstid, tillgänglighet och konsekvensgarantier, något som ingen annan databastjänst erbjuder.

Du kan [prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, kostnadsfritt och åtaganden eller använda den [kostnadsfria Azure Cosmos DB-nivån](optimize-dev-test.md#azure-cosmos-db-free-tier) för att få ett konto med de första 400 RU/s och 5 GB lagringsutrymme gratis.

> [!div class="nextstepaction"]
> [Prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB är Microsofts globalt distribuerade databastjänst med elastisk utskalning, garanterat låga svarstider, fem konsekvensmodeller och omfattande garanterade serviceavtal](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="turnkey-global-distribution"></a>Nyckelfärdig global distribution

Med Cosmos DB kan du skapa mycket responsiva program med hög tillgänglighet, runt om i världen. Cosmos DB replikerar dina data öppet var användarna än befinner sig, så att användarna kan interagera med en replik av de data som ligger närmast dem.

Med Cosmos DB kan du lägga till eller ta bort Azure-regioner på ditt Cosmos-konto när som helst genom att klicka på en knapp. Cosmos DB replikerar sömlöst dina data till alla regioner som är associerade med ditt *Cosmos-konto* medan ditt program fortsätter att vara mycket tillgängligt, tack vare tjänstens funktioner för flera mål. Mer information finns i artikeln om [global distribution](distribute-data-globally.md).

### <a name="always-on"></a>Alltid på

Genom djup integrering med [Azure-infrastruktur och transparent multi-master replikering](global-dist-under-the-hood.md)ger Cosmos DB [99,999 % hög tillgänglighet](high-availability.md) för både läsningar och skrivningar. Cosmos DB ger dig också möjligheten att anropa regional redundans för Cosmos-kontot programmatiskt (eller via portalen). Den här funktionen hjälper till att säkerställa att ditt program är utformat för redundans vid regional katastrof.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastisk skalning av dataflöde och lagringsutrymme, runt om i världen

Cosmos DB är utformat med transparent horisontell partitionering och replikering av flera original och erbjuder oöverträffad elastisk skalbarhet för skrivning och läsning över hela världen. Du kan elastiskt skala upp från tusentals till hundratals miljoner begäranden/sekund i hela världen med ett enda API-anrop och endast betala för det dataflöde (och den lagring) som du använder. Den här funktionen hjälper dig att hantera oväntade toppar i arbetsbelastningar utan att behöva överetablera för topparna. Mer information finns [i partitionering i Cosmos DB](partitioning-overview.md), etablerat [dataflöde på behållare och databaser](set-throughput.md)och [skalning av etablerat dataflöde globalt](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garanterad kort svarstid vid 99:e percentil, runt om i världen

Med Cosmos DB kan du skapa mycket responsiva och världsomfattande program. Med sin nya multi-master replikering protokoll och spärr-fri och [skriv-optimerad databasmotor](index-policy.md), Cosmos DB garanterar mindre än 10-ms latenser för båda, läser (indexerad) och skriver på den 99: e percentilen, runt om i världen. Den här funktionen möjliggör kontinuerlig inmatning av data och blixtsnabba frågor för mycket responsiva appar.

### <a name="precisely-defined-multiple-consistency-choices"></a>Ett flertal exakt definierade konsekvensval

När du skapar globalt distribuerade program i Cosmos DB behöver du inte längre göra extrema [kompromisser mellan konsekvens, tillgänglighet, svarstid och dataflöde](consistency-levels-tradeoffs.md). Cosmos DB:s replikeringsprotokoll med flera master är noggrant utformat för att erbjuda [fem väldefinierade konsekvensval](consistency-levels.md) - *som starka,* *avgränsade föråldringstillfällen,* *session,* *konsekventprefix*och *eventuellt* – för en intuitiv programmeringsmodell med låg latens och hög tillgänglighet för ditt globalt distribuerade program.

### <a name="no-schema-or-index-management"></a>Ingen hantering av schema eller index

Att hålla databasschema och index synkroniserade med ett programs schema är särskilt svårt för globalt distribuerade appar. Med Cosmos DB behöver du inte hantera schema- eller indexhantering. Databasmotorn är fullständigt schemaoberoende.  Eftersom ingen schema- eller indexhantering krävs, behöver du inte heller bekymra dig om programdriftstopp när du migrerar scheman. Cosmos DB [indexerar automatiskt alla data](index-policy.md) och hanterar frågor snabbt.

### <a name="battle-tested-database-service"></a>Beprövad databastjänst

Cosmos DB är en grundläggande tjänst i Azure. I nästan ett decennium har Cosmos DB använts av många av Microsofts produkter för verksamhetskritiska program i global skala, inklusive Skype, Xbox, Office 365, Azure och många andra. Idag är Cosmos DB en av de snabbast växande tjänsterna på Azure, som används av många externa kunder och verksamhetskritiska program som kräver elastisk skala, totalenergi global distribution, multimasterreplikering för låg latens och hög tillgänglighet för båda läser och skriver.

### <a name="ubiquitous-regional-presence"></a>Ständig regional närvaro

Cosmos DB är tillgängligt i alla Azure-regioner över hela världen, inklusive 54+ regioner i offentligt moln, [Azure China 21Vianet,](https://www.azure.cn/en-us/)Azure Germany, Azure Government och Azure Government for Department of Defense (DoD). Se [Cosmos DB:s regionala närvaro](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Säker som standard och företagsredo

Cosmos DB är certifierat för en [mängd olika efterlevnadsstandarder](compliance.md). Dessutom krypteras alla data i Cosmos DB både i vila och i rörelse. Cosmos DB erbjuder auktorisering på radnivå och följer stränga säkerhetskrav.

### <a name="significant-tco-savings"></a>Betydande besparingar på den totala ägandekostnaden

Eftersom Cosmos DB är en fullständigt hanterad tjänst behöver du inte längre hantera och driva komplexa distributioner och uppgraderingar av databasprogram, betala för support, licensiering eller åtgärder eller måste etablera databasen för den högsta arbetsbelastningen. Mer information finns i [Optimera kostnaden med Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Branschledande omfattande serviceavtal

Cosmos DB är den första och enda tjänsten som erbjuder [branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) med 99,999 % hög tillgänglighet, svarstid för läsning och skrivning på 99:e percentilen, garanterat dataflöde och konsekvens.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globalt distribuerade operativa analyser och AI med inbyggd Apache Spark

Du kan köra [Spark](spark-connector.md) direkt på data som lagras i Cosmos DB. Med den här funktionen kan du göra du göra driftanalyser i realtid med kort svarstid i global skala utan att påverka transaktionsbelastningar som arbetar direkt mot Cosmos DB. Mer information finns i [Globalt distribuerad operativ analys](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Utveckla program på Cosmos DB med populära API:er för öppen källkod (OSS)

Cosmos DB erbjuder ett urval av API:er för att arbeta med dina data som lagras i Cosmos-databasen. Som standard [kan du använda SQL](how-to-sql-query.md) (ett kärn-API) för att fråga din Cosmos-databas. Cosmos DB implementerar även API:er för [Cassandra,](cassandra-introduction.md) [MongoDB,](mongodb-introduction.md) [Gremlin](graph-introduction.md) och [Azure Table Storage](table-introduction.md). Du kan peka klientdrivrutiner (och verktyg) för den vanliga NoSQL (t.ex. Genom att stödja trådprotokollen för vanliga NoSQL API:er kan du med Cosmos DB:

* Migrera ditt program enkelt till Cosmos DB samtidigt som du bevarar betydande delar av din programlogik.
* Hålla ditt program portabelt och förbli molnleverantörsoberoende.
* Få en fullständigt hanterad molntjänst med branschledande, ekonomiskt stödda SLA:er för de vanliga NoSQL-API:erna. 
* Skala det etablerade dataflödet och lagringen elastiskt för dina databaser utifrån dina behov och betala bara för det dataflöde och lagringsutrymme du behöver. Detta medför betydande kostnadsbesparingar.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Lösningar som har nytta av Azure Cosmos DB

Alla [webb-, mobil-, spel- och IoT-program](use-cases.md) som behöver hantera enorma mängder data, läser och skriver i [global skala](distribute-data-globally.md) med nästan verkliga svarstider för en mängd olika data kommer att dra nytta av Cosmos [DB:s garanterade hög tillgänglighet,](https://azure.microsoft.com/support/legal/sla/cosmos-db/)hög genomströmning, låg latens och tunable konsekvens. Lär dig mer om hur Azure Cosmos DB kan användas för att skapa [IoT och telematik](use-cases.md#iot-and-telematics), [handel och marknadsföring](use-cases.md#retail-and-marketing), [spel](use-cases.md#gaming) samt [webb- och mobilprogram](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Nästa steg

Läs mer om Cosmos DB:s centrala begrepp [nyckelfärdig global distribution](distribute-data-globally.md) och [partitionering](partitioning-overview.md) och [etablerat dataflöde](request-units.md).

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

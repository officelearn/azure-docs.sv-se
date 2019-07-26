---
title: Introduktion till Azure Cosmos DB
description: Läs om Azure Cosmos DB. Den här globalt distribuerade databasen med flera modeller har skapats för låg svarstid, elastisk skalbarhet och hög tillgänglighet, och den ger inbyggt stöd för NoSQL-data.
author: rimman
ms.service: cosmos-db
ms.topic: overview
ms.date: 07/23/2019
ms.author: rimman
ms.openlocfilehash: ee863b6003444ec099753a6488f3fe4078338e60
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384132"
---
# <a name="welcome-to-azure-cosmos-db"></a>Välkommen till Azure Cosmos DB

Dagens program måste alltid vara mycket responsiva och ständigt online. Instanser av dessa program måste distribueras i datacenter som ligger nära användarna för att uppnå kort svarstid och hög tillgänglighet. Programmen behöver kunna svara i realtid på stora förändringar av användningen vid hög belastning, lagra ständigt ökande datavolymer och göra dessa data tillgängliga för användare på millisekunder.

Azure Cosmos DB är Microsofts globalt distribuerade databastjänst för flera datamodeller. Med ett klick på en knapp kan du Cosmos DB att elastiskt och oberoende skala data flöde och lagrings utrymme i valfritt antal Azure-regioner över hela världen. Du kan skala data flöde och lagrings utrymme elastiskt, och dra nytta av snabb, ensiffriga millisekunder för data åtkomst med ditt favorit-API, inklusive SQL, MongoDB, Cassandra, tables eller Gremlin. Cosmos DB tillhandahåller omfattande [service avtal](https://aka.ms/acdbsla) (service avtal) för data flöde, svars tid, tillgänglighet och konsekvens garantier, något som inte har någon annan databas tjänst.

Du kan [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration, utan kostnad och åtaganden.

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

![Azure Cosmos DB är Microsofts globalt distribuerade databastjänst med elastisk utskalning, garanterat låga svarstider, fem konsekvensmodeller och omfattande garanterade serviceavtal](./media/introduction/azure-cosmos-db.png)

## <a name="key-benefits"></a>Viktiga fördelar

### <a name="turnkey-global-distribution"></a>Nyckelfärdig global distribution

Med Cosmos DB kan du skapa mycket responsiva program med hög tillgänglighet, runt om i världen. Cosmos DB replikerar data transparent var användarna är, så att användarna kan interagera med en replik av de data som är närmast dem.

Med Cosmos DB kan du lägga till eller ta bort Azure-regioner på ditt Cosmos-konto när som helst genom att klicka på en knapp. Cosmos DB replikerar sömlöst dina data till alla regioner som är kopplade till ditt Cosmos-konto medan ditt program fortsätter att vara hög tillgängligt, tack vare funktionerna för *flera värdar* i tjänsten. Mer information finns i artikeln om [global distribution](distribute-data-globally.md).

### <a name="always-on"></a>Alltid på

Med en djupgående integrering med Azure-infrastrukturen och [transparent multi-master-replikering](global-dist-under-the-hood.md)ger Cosmos DB [99,999% hög tillgänglighet](high-availability.md) för både läsningar och skrivningar. Cosmos DB ger dig också möjligheten att anropa regional redundans för Cosmos-kontot programmatiskt (eller via portalen). Den här funktionen hjälper till att säkerställa att ditt program är utformat för redundans vid regional haveriering.

### <a name="elastic-scalability-of-throughput-and-storage-worldwide"></a>Elastisk skalning av dataflöde och lagringsutrymme, runt om i världen

Cosmos DB är utformat med transparent horisontell partitionering och replikering av flera original och erbjuder oöverträffad elastisk skalbarhet för skrivning och läsning över hela världen. Du kan elastiskt skala upp från tusentals till hundratals miljoner begäranden/sekund i hela världen med ett enda API-anrop och endast betala för det dataflöde (och den lagring) som du använder. Den här funktionen hjälper dig att hantera oväntade toppar i arbetsbelastningar utan att behöva överetablera för topparna. Mer information finns i [partitionering i Cosmos DB](partitioning-overview.md), [tillhandahållet data flöde på behållare och databaser](set-throughput.md)och skalning av [allokerat data flöde globalt](scaling-throughput.md).

### <a name="guaranteed-low-latency-at-99th-percentile-worldwide"></a>Garanterad kort svarstid vid 99:e percentil, runt om i världen

Med Cosmos DB kan du skapa mycket responsiva och världsomfattande program. Med den nya multi-master Replication-protokollet och låsnings fri och [skrivbar databas motor](index-policy.md), Cosmos DB garanterar mindre än 10 ms fördröjning för båda, läser (indexerat) och skriver i 99 percentilen hela världen. Den här funktionen möjliggör kontinuerlig inmatning av data och blixtsnabba frågor för mycket responsiva appar.

### <a name="precisely-defined-multiple-consistency-choices"></a>Ett flertal exakt definierade konsekvensval

När du skapar globalt distribuerade program i Cosmos DB behöver du inte längre göra extrema [kompromisser mellan konsekvens, tillgänglighet, svars tid och data flöde](consistency-levels-tradeoffs.md). Cosmos DBS protokollet för multi-master-replikering är omsorgsfullt utformat för att erbjuda [fem väldefinierade konsekvens alternativ](consistency-levels.md) - *stark*, *gräns*för föråldrad, *session*, *konsekvent prefix*och *eventuell* – för en intuitiv programmerings modell med låg latens och hög tillgänglighet för ditt globalt distribuerade program.

### <a name="no-schema-or-index-management"></a>Ingen hantering av schema eller index

Att hålla databasschema och index synkroniserade med ett programs schema är särskilt svårt för globalt distribuerade appar. Med Cosmos DB behöver du inte hantera schema-eller index hantering. Databasmotorn är fullständigt schemaoberoende.  Eftersom ingen schema- eller indexhantering krävs, behöver du inte heller bekymra dig om programdriftstopp när du migrerar scheman. Cosmos DB [indexerar automatiskt alla data](index-policy.md) och hanterar frågor snabbt.

### <a name="battle-tested-database-service"></a>Beprövad databastjänst

Cosmos DB är en grundläggande tjänst i Azure. För nästan en tio års perioden har Cosmos DB använts av många av Microsofts produkter för verksamhets kritiska program i global skala, inklusive Skype, Xbox, Office 365, Azure och många andra. Idag är Cosmos DB en av de snabbast växande tjänsterna på Azure, som används av många externa kunder och verksamhets kritiska program som kräver elastisk skala, nyckel färdig global distribution, multi-master-replikering för låg latens och hög tillgänglighet för båda läsningar och skrivningar.

### <a name="ubiquitous-regional-presence"></a>Ständig regional närvaro

Cosmos DB är tillgängligt i alla Azure-regioner över hela världen, inklusive 54 + regioner i offentliga moln, [Azure Kina 21Vianet](https://www.azure.cn/en-us/), Azure Germany, Azure Government och Azure Government för DoD (Department of försvar). Se [Cosmos DB:s regionala närvaro](regional-presence.md).

### <a name="secure-by-default-and-enterprise-ready"></a>Säker som standard och företagsredo

Cosmos DB är certifierat för en [mängd olika efterlevnadsstandarder](compliance.md). Dessutom krypteras alla data i Cosmos DB både i vila och i rörelse. Cosmos DB erbjuder auktorisering på radnivå och följer stränga säkerhetskrav.

### <a name="significant-tco-savings"></a>Betydande besparingar på den totala ägandekostnaden

Eftersom Cosmos DB är en helt hanterad tjänst behöver du inte längre hantera och använda komplexa distributioner av flera data Center och uppgraderingar av din databas program vara, betala för support, licensiering eller åtgärder eller måste etablera databasen för hög belastning. Mer information finns i [optimera kostnader med Cosmos DB](total-cost-ownership.md).

### <a name="industry-leading-comprehensive-slas"></a>Branschledande omfattande serviceavtal

Cosmos DB är den första och enda tjänsten som erbjuder [branschledande omfattande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/) med 99,999 % hög tillgänglighet, svarstid för läsning och skrivning på 99:e percentilen, garanterat dataflöde och konsekvens.

### <a name="globally-distributed-operational-analytics-and-ai-with-natively-built-in-apache-spark"></a>Globalt distribuerad operativa analys och AI med inbyggd inbyggd Apache Spark

Du kan köra [Spark](spark-connector.md) direkt på data som lagras i Cosmos DB. Med den här funktionen kan du göra du göra driftanalyser i realtid med kort svarstid i global skala utan att påverka transaktionsbelastningar som arbetar direkt mot Cosmos DB. Mer information finns i [globalt distribuerade operativa analyser](lambda-architecture.md).

### <a name="develop-applications-on-cosmos-db-using-popular-open-source-software-oss-apis"></a>Utveckla program på Cosmos DB med hjälp av populära OSS-API: er med öppen källkod

Cosmos DB erbjuder ett urval av API: er för att arbeta med dina data som lagras i Cosmos-databasen. Som standard [kan du använda SQL](how-to-sql-query.md) (ett Core API) för att skicka frågor till Cosmos-databasen. Cosmos DB implementerar också API: er för [Cassandra](cassandra-introduction.md), [MongoDB](mongodb-introduction.md), [Gremlin](graph-introduction.md) och [Azure Table Storage](table-introduction.md). Du kan peka klient driv rutiner (och verktyg) för de NoSQL som används ofta (t. ex. MongoDB, Cassandra, Gremlin) direkt till din Cosmos-databas. Genom att stödja kabel protokollen för vanliga NoSQL-API: er kan du i Cosmos DB:

* Migrera ditt program enkelt till Cosmos DB samtidigt som du bevarar betydande delar av din programlogik.
* Hålla ditt program portabelt och förbli molnleverantörsoberoende.
* Få en fullständigt hanterad moln tjänst med branschledande, ekonomiskt säkerhetskopierade service avtal för vanliga NoSQL-API: er. 
* Skala det etablerade dataflödet och lagringen elastiskt för dina databaser utifrån dina behov och betala bara för det dataflöde och lagringsutrymme du behöver. Detta medför betydande kostnadsbesparingar.

## <a name="solutions-that-benefit-from-azure-cosmos-db"></a>Lösningar som har nytta av Azure Cosmos DB

Alla [webb-, mobil-, spel-och IoT-program](use-cases.md) som behöver hantera stora mängder data, läsningar och skrivningar i en [global skala](distribute-data-globally.md) med nästan faktiska svars tider för en mängd olika data kommer att dra nytta av Cosmos DB [garanterad hög tillgänglighet ](https://azure.microsoft.com/support/legal/sla/cosmos-db/), högt data flöde, låg latens och justerbara konsekvens. Lär dig mer om hur Azure Cosmos DB kan användas för att skapa [IoT och telematik](use-cases.md#iot-and-telematics), [handel och marknadsföring](use-cases.md#retail-and-marketing), [spel](use-cases.md#gaming) samt [webb- och mobilprogram](use-cases.md#web-and-mobile-applications).

## <a name="next-steps"></a>Nästa steg

Läs mer om grundläggande begrepp för Cosmos DB [global distribution](distribute-data-globally.md) och [partitionering](partitioning-overview.md) och allokerat [data flöde](request-units.md).

Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Komma igång med SQL-API för Azure Cosmos DB](create-sql-api-dotnet.md)
* [Kom igång med Azure Cosmos DB-API:n för Azure MongoDB](create-mongodb-nodejs.md)
* [Kom igång med Cassandra-API för Azure Cosmos DB](create-cassandra-dotnet.md)
* [Komma igång med Gremlin API för Azure Cosmos DB](create-graph-dotnet.md)
* [Komma igång med Tabell-API för Azure Cosmos DB](create-table-dotnet.md)

> [!div class="nextstepaction"]
> [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/)

---
title: Introduktion till Azure Cosmos DB | Microsoft Docs
description: "Läs om Azure Cosmos DB. Den här globalt distribuerade databasen med flera modeller har skapats för låg svarstid, elastisk skalbarhet och hög tillgänglighet."
services: cosmosdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: a855183f-34d4-49cc-9609-1478e465c3b7
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/10/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 1591a7935b10e22164d26a09c4e923101b517227
ms.contentlocale: sv-se
ms.lasthandoff: 05/10/2017


---

# <a name="welcome-to-azure-cosmos-db"></a>Välkommen till Azure Cosmos DB

Azure Cosmos DB är Microsofts globalt distribuerade databas för flera datamodeller. Med ett knappklick, låter Azure DB Cosmos dig att elastiskt och oberoende skala dataflöde och lagring över valfritt antal av Azures geografiska regioner. Det erbjuder garantier när det gäller dataflöde, svarstider, tillgänglighet och konsekvens med omfattande [serviceavtal](https://aka.ms/acdbsla) (SLA:er) något som ingen annan databastjänst kan erbjuda.

![Azure Cosmos DB är Microsofts globalt distribuerade databastjänst med elastisk utskalning, garanterat låga svarstider, fem konsekvensmodeller och omfattande garanterade serviceavtal](./media/introduction/azure-cosmos-db.png)

Azure Cosmos DB innehåller en skrivoptimerad, resursstyrd, schemaoberoende databasmotor som har inbyggt stöd för flera datamodeller: nyckelvärden, dokument, diagram och kolumner. Det stöder även flera API:er för dataåtkomst inklusive [MongoDB](../documentdb/documentdb-protocol-mongodb.md), [DocumentDB SQL](../documentdb/documentdb-introduction.md), [Gremlin](graph-introduction.md) (förhandsgranskning) och [Azure Tables](table-introduction.md) (förhandsgranskning) på ett utökningsbart sätt. 

Azure Cosmos DB startade i slutet av 2010 för att komma tillrätta med problem som upplevdes av storskaliga program inom Microsoft. Eftersom att skapa globalt distribuerade program inte är ett unikt problem för Microsoft, har vi gjort tjänsten externt tillgänglig för alla Azure-utvecklare i form av Azure DocumentDB. Azure Cosmos DB är nästa stora steg i utvecklingen av DocumentDB och vi gör det nu tillgängligt för dig att använda. Som en del av den här versionen av Azure Cosmos DB, är DocumentDB-kunder (med sina data) automatiskt Azure DB Cosmos-kunder. Övergången är sömlös och de har nu åtkomst till ett bredare utbud av nya funktioner som erbjuds av Azure Cosmos DB. 

## <a name="capability-comparison"></a>Jämförelse av funktioner

Azure Cosmos DB erbjuder de bästa funktionerna för relations- och icke-relationsdatabaser.

| Funktioner | Relations-databaser    | Icke-relationsdatabaser (NoSQL) |     Azure Cosmos DB |
| --- | --- | --- | --- |
| Global distribution | x | x | ✓ Nyckelfärdig, 30+ regioner, flera värdar |
| Horisontell skalning | x | ✓ | ✓ Oberoende skalning av lagring och dataflöde | 
| Svarstidsgarantier | x | ✓ | ✓ < 10 ms för läsningar, < 15 ms för skrivningar på p99 | 
| Hög tillgänglighet | x | ✓ | ✓ Alltid på, PACELC-avvägningar, automatisk och manuell redundans |
| Datamodell + API | Relations + SQL | Multi-modell + OSS API | Multi-modell + SQL + OSS API (mer kommer snart) |
| Serviceavtal | ✓ | x | ✓ Omfattande serviceavtal för svarstider, dataflöde, konsekvens och tillgänglighet |

## <a name="key-capabilities"></a>De viktigaste funktionerna
Som en globalt distribuerad databastjänst, erbjuder Azure Cosmos DB följande funktioner för att hjälpa dig att bygga skalbara, globalt distribuerade och högdynamiska program:

* [**Nyckelfärdig global distribution**](#global-distribution)
    * Ditt program finns direkt tillgängligt för dina användare, var som helst. Nu kan dina data vara det också.
    * Oroa dig inte om maskinvara, lägga till noder, virtuella datorer eller kärnor. Det är bara att peka och klicka så finns dina data där. 

* [**Flera datamodeller och populära API:er för att komma åt och fråga data**](#data-models)
    * Stöd för flera datamodeller inklusive nyckel-värde, dokument, diagram och kolumner.
    * Utökningsbara API:er för Node.js, Java, .NET, .NET Core, Python och MongoDB.
    * SQL och Gremlin för frågor. 

* [**Skala elastiskt dataflöde och lagring på begäran, globalt**](#horizontal-scale)
    * Skala enkelt dataflöde med [sekund-](../documentdb/documentdb-request-units.md) och [minut-](https://aka.ms/acdbrupm)precision och ändra närhelst du vill. 
    * Skala lagring [transparent och automatiskt](partition-data.md) för att täcka upp dina storleksbehov nu och för evigt.

* [**Bygg högdynamiska och verksamhetskritiska program**](#low-latency) 
    * Få åtkomst till dina data med svarstider på ensiffriga millisekunder i 99:e percentilen var som helst i världen. 

* [**Tillse en ”alltid-på”-tillgänglighet**](#high-availability)
    * 99.99 % tillgänglighet inom en region.
    * Distribuera till valfritt antal [Azure-regioner](https://azure.microsoft.com/regions) för högre tillgänglighet.
    * [Simulera ett fel](../documentdb/documentdb-regional-failovers.md) i en eller flera regioner med garantier om noll dataförlust. 

* [**Skriv globalt distribuerade program, på rätt sätt**](#consistency)
    * [Fem konsekvensmodeller](../documentdb/documentdb-consistency-levels.md) modeller ger en stark SQL-liknande konsekvens för NoSQL-liknande eventuell konsekvens och allt däremellan. 
  
* [**Pengarna tillbaka-garanti**](#sla) 
    * Dina data kommer dit de ska snabbt, eller pengarna tillbaka. 
    * [Serviceavtal](https://aka.ms/acdbsla) för tillgänglighet, svarstid, dataflöde och konsekvens. 

* [**Ingen hantering av databasscheman/-index**](#schema-free)
    * Sluta oroa dig för att hålla dina databasscheman och -index synkroniserade med schemat för ditt program. Vi är schema-fria. 

* [**Låg totalkostnad**](#tco)
    * Fem till tio gånger [mer kostnadseffektivt](https://aka.ms/documentdb-tco-paper) än en icke-hanterad lösning.
    * Tre gånger billigare än DynamoDB.

<a id="global-distribution"></a>

## <a name="global-distribution"></a>Global distribution
Azure Cosmos DB-behållare distribueras i två dimensioner: 

1. Inom en viss region partitioneras alla resurser horisontellt med resurspartitioner (lokal distribution). 
2. Varje resurspartition replikeras också över geografiska regioner (global distribution). 

![Ett diagram som illustrerar den globala distributionen av Azure Cosmos DB](./media/introduction/azure-cosmos-db-global-distribution.png) 

När lagring och dataflöde behöver skalas, utför Cosmos DB partitionshanteringsåtgärderna på ett transparent sätt över alla regioner. Oberoende av skala, distribution eller fel, fortsätter Cosmos DB att tillhandahålla en enskild systemavbildning av de globalt distribuerade resurserna. 

Global distribution av resurser i Cosmos DB är [nyckelfärdig](../documentdb/documentdb-distribute-data-globally.md). Du kan när som helst, med några knapptryckningar (eller programmässigt med ett enda API-anrop), associera valfritt antal geografiska regioner med ditt databaskonto. 

Oavsett hur mycket data eller antalet regioner, garanterar Cosmos DB att varje nyassocierad region börjar bearbeta kundbegäranden på under timmen i den 99:e percentilen. Det åstadkoms genom att parallellisera seedning och kopiering av data från alla källresurspartitioner till den nyassocierade regionen. Kunder kan också ta bort en befintlig region eller ta en region som tidigare varit associerad med deras databaskonto offline.

<a id="data-models"></a>
## <a name="multi-model-multi-api-support"></a>Stöd för flera modeller och API:er
 Azure Cosmos DB har inbyggt stöd för flera datamodeller inklusive dokument, nyckel-värde, diagram och kolumnfamiljen. Core-innehållsmodellen av Cosmos DB:s-databasmotor är baserad på en atom-post-sekvens (ARS). Atomer består av små uppsättningar primitiva typer som sträng, bool och siffra. Poster är strukturer bestående av de här typerna. Sekvenser är matriser som består av atomer, poster eller sekvenser. 

![Flera modeller och API:er Cosmos Azure DB](./media/introduction/azure-cosmos-db-multimodel.png) 
 
 Databasmotorn kan effektivt översätta och projicera olika datamodeller på den ARS-baserade datamodellen. Kärndatamodellen för Cosmos DB är internt åtkomlig från dynamiskt skrivet programmeringsspråk och kan visas som det är som JSON. 
 
 Tjänsten stöder också populära databas-API:er för dataåtkomst och frågor. Cosmos DB:s databasmotor stöder för närvarande [DocumentDB SQL](../documentdb/documentdb-introduction.md), [MongoDB](../documentdb/documentdb-protocol-mongodb.md), [Azure Tables](table-introduction.md) (förhandsversion) och [Gremlin](graph-introduction.md) (förhandsversion). Du kan fortsätta att utveckla program med populära OSS API:er och få alla fördelar med en beprövad och fullständigt hanterad, globalt distribuerad databastjänst. 

<a id="horizontal-scale"></a>
## <a name="horizontal-scaling-of-storage-and-throughput"></a>Horisontell skalning av lagring och dataflöde
Alla data i en Cosmos-DB-behållare (till exempel dokumentsamling, tabell eller diagram) är horisontellt partitionerade och hanteras transparent av resurspartitioner. En resurspartition är en konsekvent och högtillgänglig behållare för data som har partitionerats med en [användarspecificerad partitionsnyckel](partition-data.md). Den erbjuder en enda systemavbildning för en uppsättning resurser som den hanterar och är en grundläggande enhet för skalbarhet och distribution. Cosmos DB är utformad för att låta dig elastiskt skala dataflöde baserat på programmets trafikmönster över olika geografiska regioner för att stödja fluktuerande arbetsbelastningar som varierar både geografiskt och tidsmässigt. Tjänsten hanterar partitionerna transparent utan att kompromissa med tillgängligheten, konsekvensen, svarstiden eller dataflödet för en Cosmos DB-behållare.  
 
![Azure Cosmos DB är horisontellt skalbar](./media/introduction/azure-cosmos-db-partitioning.png) 

Du kan elastiskt skala dataflödet i en Azure Cosmos DB-behållare genom att programmässigt etablera dataflöde med hjälp av [begärandeenheter per sekund (RU/s)](../documentdb/documentdb-request-units.md). Internt hanterar tjänsten transparent resurspartitioner för att leverera dataflödet i en given behållare. Cosmos DB garanterar att dataflödet finns tillgängligt för användning i alla regioner som associerats med behållaren. Det nya dataflödet är effektivt inom fem sekunder från att ändringen av konfigurerat dataflödesvärde gjorts. 

Du kan etablera dataflöde för en Cosmos DB-behållare med per sekund och [per minut (RU/m)](request-units-per-minute.md) precision. Det etablerade dataflödet på minutprecision används för att hantera oväntade toppar i arbetsbelastning som sker med en per sekund-precision. 

<a id="low-latency"></a>
## <a name="low-latency-guarantees-at-the-99th-percentile"></a>Låga svarstidsgarantier i 99:e percentilen
Som en del av sitt serviceavtal, garanterar Cosmos DB sina kunder svarstider från slutpunkt till slutpunkt som ligger i 99:e percentilen. För ett typiskt 1 KB-objekt, garanterar Cosmos DB en svarstid på läsningar från slutpunkt till slutpunkt på under 10 ms och indexerade skrivningar under 15 ms i den 99:e percentilen, inom samma Azure-region. Median-svarstiderna är betydligt lägre (under 5 ms).  Med en övre gräns för bearbetning av begäranden för varje databastransaktion, låter Cosmos DB kunder skilja tydligt mellan transaktioner med hög svarstid och en databas som är otillgänglig.

<a id="high-availability"></a>
## <a name="transparent-multi-homing-and-9999-high-availability"></a>Transparent flera värdar och 99.99% hög tillgänglighet
Du kan dynamiskt associera "prioriteter" till de områden som är associerade med ditt Azure Cosmos DB-databaskonto. Prioriteter används för att dirigera begäranden till specifika regioner vid regionala fel. Om ett ytterst osannolikt regionalt haveri skulle ske, växlar Cosmos DB automatiskt över i prioritetsordning.

Om du vill testa tillgängligheten från slutpunkt till slutpunkt för programmet, kan du [manuellt utlösa redundans](../documentdb/documentdb-regional-failovers.md) (frekvensbegränsat till två åtgärder inom en timme). Cosmos DB garanterar noll dataförlust vid manuella regionala redundanser. Om ett regionalt haveri skulle inträffa, garanterar Cosmos DB en övre gräns för dataförlust vid den systeminitierade automatiska redundansen. Du behöver inte distribuera om ditt program efter en regional redundans och serviceavtal för tillgänglighet upprätthålls av Azure Cosmos DB. 

I det här scenariot, låter Cosmos DB dig interagera med resurser antingen via logiska (regionsoberoende) eller fysiska (regionsspecifika) slutpunkter. Det tidigare säkerställer att programmet transparent kan ha flera värdar vid redundans. Det senare ger precisionskontroll över programmet för att styra läsningar och skrivningar till specifika regioner. Cosmos DB garanterar ett serviceavtal med 99.99 % tillgänglighet för varje databaskonto. Tillgänglighetsgarantierna är oberoende av skalan (etablerat dataflöde och lagring), antalet regioner eller geografiskt avstånd mellan regionerna som är associerade med en viss databas. 

<a id="consistency"></a>
## <a name="multiple-well-defined-consistency-models"></a>Flera, väldefinierade konsekvensmodeller
Kommersiellt distribuerade databaser är indelade i två kategorier: databaser som inte alls erbjuder väldefinierade, beprövade konsekvensval och databaser som erbjuder två extremt programmerbara val (stark kontra eventuell konsekvens). Den tidigare tynger programmerare med detaljerna i sina replikeringsprotokoll och förväntar sig att de ska göra svåra avvägningar mellan konsekvens, tillgänglighet, svarstid och dataflöde. Den senare tvingar en att välja ett av två extremval. Trots mängden forskning och förslag på över 50 konsekvensmodeller, har den distribuerade databascommunityn inte kunna kommersialisera konsekvensnivåer utöver stark och eventuell konsekvens. 

Cosmos DB låter dig välja mellan [fem väldefinierade konsekvensmodeller](../documentdb/documentdb-consistency-levels.md) längs konsekvensspektrumet: stark, begränsad föråldring, [session](http://dl.acm.org/citation.cfm?id=383631), konsekvent prefix och eventuell. 

![Azure Cosmos DB erbjuder flera väldefinierade (avslappnade) konsekvensmodeller att välja mellan](media/introduction/azure-cosmos-db-consistency-levels.png)

Följande tabell visar de specifika garantier som varje konsekvensnivå erbjuder.
 
**Konsekvensnivåer och garantier**

| Konsekvensnivå    | Garantier |
| --- | --- |
| Stark | Lineariserbarhet |
| Begränsad föråldring    | Konsekvent Prefix. Läsningar släpar efter skrivningar med k-prefix eller t-intervall |
| Session    | Konsekvent Prefix. Monotoniska läsningar, monotoniska skrivningar, läs-dina-skrivningar, skrivning-följer-läsning |
| Konsekvent prefix    | De uppdateringar som returneras är något prefix av alla uppdateringar, utan några mellanrum |
| Eventuell    | Oordnade läsningar |

Du kan konfigurera standard-konsekvensnivå på ditt Cosmos DB-konto (och senare åsidosätta konsekvens för en specifik läsbegäran). Internt gäller standardkonsekvensnivån för data inom partitionsuppsättningarna vilket kan innebära span-regioner. 


<a id="sla"></a>
## <a name="guaranteed-service-level-agreements"></a>Garanterade serviceavtal

Cosmos DB är den första hanterade databastjänsten som erbjuder 99.99 % [serviceavtalsgarantier](https://aka.ms/acdbsla) vad det gäller tillgänglighet, dataflöde, låga svarstider och konsekvens.
* Tillgänglighet: drifttid 99.99% serviceavtal för drifttidstillgänglighet för alla data- och kontrollplansåtgärder.
* Dataflöde: 99.99% av begäranden slutförs 
* Svarstid: 99.99% av < 10 ms svarstider i 99:e percentilen
* Konsekvens: garanti att 100 % av läsbegäranden uppfyller konsekvens för den konsekvensnivå du valt.


<a id="schema-free"></a>
## <a name="schema-free"></a>Schemafri

Både relations- och NoSQL-databaser tvingar dig att handskas med schema- och indexhantering, versionshantering och migrering. Allt det här kan vara extremt utmanande i en global konfiguration. Men oroa dig inte! Cosmos DB får det här problemet att försvinna! Med Cosmos DB behöver du inte hantera scheman och index, handskas med schemaversionshantering eller oroa dig för driftavbrott när du migrerar scheman. Cosmos DB:s databasmotor är fullständigt schemaoberoende. Den indexerar automatiskt alla data den tar in utan att kräva något schema eller index och hanterar frågor blixtrande snabbt. 

<a id="tco"></a>
## <a name="low-cost-of-ownership"></a>Låg totalkostnad

 När alla överväganden för de totala ägandekostnaderna (TCO) beaktas, kan hanterade molntjänster som Azure Cosmos DB vara fem till tio gånger mer kostnadseffektiva än sina OSS-motparter som körs på lokala eller virtuella datorer. Azure DB Cosmos är också upp till två till tre gånger billigare än DynamoDB för arbetsbelastningar med hög volym. Läs mer i [TCO dokumentationen](https://aka.ms/documentdb-tco-paper). 

## <a name="next-steps"></a>Nästa steg
Kom igång med Azure Cosmos DB med någon av våra snabbstarter:

* [Kom igång med DocumentDB-API:n för Azure Cosmos DB](create-documentdb-dotnet.md)
* [Kom igång med MongoDB-API:n för Azure Cosmos DB](create-mongodb-nodejs.md)
* [Kom igång med Graph-API:n för Azure Cosmos DB](create-graph-dotnet.md)
* [Kom igång med Table-API:n för Azure Cosmos DB](create-table-dotnet.md)


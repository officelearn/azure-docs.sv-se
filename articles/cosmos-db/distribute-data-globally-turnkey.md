---
title: Hur Azure Cosmos DB tillhandahåller nyckelfärdig global distribution | Microsoft Docs
description: Lär dig mer om global skala geo-replikering, multimaster, redundans och data med hjälp av globala databaser från Azure Cosmos DB, en globalt distribuerad databastjänst.
services: cosmos-db
author: markjbrown
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.openlocfilehash: 1aa0fa3d4d9e1821a6980d9334456a78eba7bfbc
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956922"
---
# <a name="how-azure-cosmos-db-enables-turnkey-global-distribution"></a>Hur Azure Cosmos DB tillhandahåller nyckelfärdig global distribution
Azure Cosmos DB tillhandahåller följande funktioner som gör det möjligt att enkelt skriva globalt distribuerade program. Dessa funktioner är tillgängliga via Azure Cosmos DB: s resource provider-baserad [REST API: er](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/) samt Azure-portalen.

## <a id="GlobalDistribution"></a>Global distribution

### <a id="RegionalPresence"></a>Allt vanligare regionala närvaro 
Azure ständigt växande dess geografisk närvaro genom att föra [nya regioner](https://azure.microsoft.com/regions/) online. Azure Cosmos DB klassificeras som en *grundläggande tjänst* i Azure och är tillgänglig i alla nya Azure-regioner som standard. På så sätt kan du associera en geografisk region med ditt Azure Cosmos DB-databaskonto som öppnar den nya regionen för företag i Azure.

### <a id="MultiMasterSupport"></a>Stöd för flera huvudservrar
Azure Cosmos DB har inbyggd, serversidan stöd för flera master regioner som lika deltar i en skrivning var som helst-modell. Det här stödet ger < 10 ms skrivsvarstid och 99,999% skrivtillgänglighet av [med ekonomisk uppbackning serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Multimaster är tillgänglig för alla API:, inklusive [SQL](sql-api-introduction.md), [MongoDB](mongodb-introduction.md), [Cassandra](cassandra-introduction.md), [Graph](graph-introduction.md), och [tabell](table-introduction.md) och på alla SDK: N för Cosmos DB. Azure Cosmos DB stöder 4 olika konsekvensnivåer (bunden föråldring, session, enhetligt prefix och slutlig) för konton med hantering av flera funktioner.

### <a id="UnlimitedRegionsPerAccount"></a>Koppla ett obegränsat antal regioner med ditt Azure Cosmos DB-databaskonto
Azure Cosmos DB kan du associera valfritt antal Azure-regioner med ditt Azure Cosmos DB-databaskonto. Utanför geografiska avgränsningar begränsningar (till exempel Kina, Tyskland) finns det inga begränsningar för antalet regioner som kan associeras med ditt Azure Cosmos DB-databaskonto. Följande bild visar ett databaskonto som konfigurerats för att omfatta flera 25 Azure-regioner:

![Azure Cosmos DB-databaskontot över 25 Azure-regioner](./media/distribute-data-globally-turnkey/spanning-regions.png)


### <a id="PolicyBasedGeoFencing"></a>Principbaserad geografiska avgränsningar
Azure Cosmos DB är utformad för att stödja principbaserad geografiska avgränsningar. Geografiska avgränsningar är en viktig komponent för att säkerställa databegränsningar för styrning och efterlevnad och kan inte koppla en viss region med ditt konto. Exempel på geografiska avgränsningar inkludera (men är inte begränsat till) Konfigurera global distribution till regionerna inom ett nationellt moln (till exempel Kina och Tyskland) eller inom ett government skatt (till exempel Australien). Principerna som styrs med hjälp av metadata för din Azure-prenumeration.

### <a id="DynamicallyAddRegions"></a>Dynamiskt lägga till och ta bort regioner
Azure Cosmos DB kan du lägga till (koppla) eller ta bort (koppla bort) regioner från ditt databaskonto vid varje givet tillfälle (se [föregående bild](#UnlimitedRegionsPerAccount)). Tack vare parallell replikering av data över partitioner Azure Cosmos DB garanterar att när en ny region läggs det är tillgängligt för åtgärder inom 30 minuter var som helst i världen (förutsatt att dina data är 100 TB eller mindre). 

### <a id="FailoverPriorities"></a>Prioriteter för redundans
När du inte använder multimaster, kunder kan styra exakt i vilken ordning regionala redundanstestningar i händelse av ett avbrott, Azure Cosmos DB kan du associera en *prioritet* med olika regioner som associeras med kontot (se den bilden nedan). Azure Cosmos DB säkerställer att de automatisk redundans sker i prioritetsordning som du har angett. Läs mer om regionala redundanstestningar [automatisk regionala redundanser för kontinuitet för företag i Azure Cosmos DB](regional-failover.md). Följande bild visar hur en klient i Azure Cosmos DB kan konfigurera redundans prioritetsordning (till höger) för regioner som associeras med ett databaskonto:

![Konfigurera redundansprioriteringar med Azure Cosmos DB](./media/distribute-data-globally-turnkey/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Flera, väldefinierade konsekvensmodeller för globalt distribuerade databaser
Azure Cosmos DB stöder [flera väldefinierade, intuitivt och praktiska konsekvensmodeller](consistency-levels.md) understöds av serviceavtal. Du kan välja en specifik konsekvensmodell (från listan över tillgängliga alternativ) beroende på arbetsbelastningen/scenarier.

### <a id="TunableConsistency"></a>Finjusterbar konsekvens för globalt replikerade databaser
Azure Cosmos DB kan du programmässigt åsidosätta och lätta standardalternativet för konsekvens på basis av per begäran vid körning.

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Kan konfigureras dynamiskt Läs och Skriv-regioner
Azure Cosmos DB kan du konfigurera regioner (som associerats med databasen) för ”läsa”, ”skriva” eller ”Läs/Skriv” regioner.

### <a id="ElasticallyScaleThroughput"></a>Elastiskt skala dataflöde i Azure-regioner
Du kan Elastiskt skala ett Azure Cosmos DB-behållare genom att etablering dataflöde programmässigt. Dataflödet tillämpas på alla regioner som Azure Cosmos DB-behållare distribueras i.

### <a id="GeoLocalReadsAndWrites"></a>GEO-lokala läsningar och skrivningar
Den viktigaste fördelen med en globalt distribuerad databas är att den erbjuder snabb svarstid till data överallt i världen. Azure Cosmos DB erbjuder låg latens (< 10 millisekunder) läser och skriver den 99: e percentilen över hela världen. Det innebär att alla läsningar hanteras från regionen närmast (lokalt). För att leverera en läsbegäran används lokalt i den region där läsningen utfärdas kvorum. Samma gäller för skrivningar. En skrivning bekräftas endast när en majoritet av repliker har hållbarheten har säkerställts skrivningen lokalt men utan att gated på fjärranslutna repliker att bekräfta skrivningar. Om du vill placera den på olika sätt fungerar protokollet replikering i Azure Cosmos DB under förutsättning som läsning och skrivning beslutsförhet alltid är lokala för den region där begäran har utfärdats.

### <a id="ManualFailover"></a>Manuell redundans
Azure Cosmos DB kan du utlösa redundans för ett databaskonto för att verifiera den *från slutpunkt till slutpunkt* tillgänglighet egenskaperna för hela programmet (utanför databasen). Eftersom både säkerhet och liveness egenskaperna för fel vid identifiering och ledare val garanterat, Azure Cosmos DB garanterar *noll dataförlust* för en klient-initierad manuell redundans-åtgärd.

### <a id="AutomaticFailover"></a>Automatisk redundans
Azure Cosmos DB stöder automatisk växling vid fel under en eller flera regionala avbrott. Azure Cosmos DB upprätthåller dess lässvarstid, drifttid, konsekvens och dataflöde SLA: er under en regional redundans. Azure Cosmos DB erbjuder en övre gräns för varaktigheten för en automatisk redundans-åtgärden har slutförts. Det här är fönstret förlust av data under ett regionalt strömavbrott.

### <a id="GranularFailover"></a>Utformad för olika redundans Precision
För närvarande exponeras funktioner för automatisk och manuell redundans vid Granulariteten för databaskontot. Observera att internt Azure Cosmos DB är utformad att erbjuda *automatisk* redundans med bättre precision för en databas, en behållare eller till och med en partition (för en behållare som äger ett antal nycklar). 

### <a id="MultiHomingAPIs"></a>Flera värdar i Azure Cosmos DB

Med logiska (regionsoberoende) eller fysiska (regionsspecifika) slutpunkter i ett enda ställe scenario där du har en skrivregion och flera Läs-repliker, Azure Cosmos DB kan du samverka med en-databas. Med logiska slutpunkter säkerställer att programmet transparent kan ha flera värdar vid redundans. Det senare, en fysisk slutpunkt, ger precisionskontroll över programmet för att styra läsningar och skrivningar till specifika regioner. I scenarier med flera regioner för skrivskyddad ange användare regionspecifika slutpunkter och regioner har lagts till i prioritetsordning för användning och omdirigerar.

Du hittar information om hur du konfigurerar Läs inställningar för den [SQL API](../cosmos-db/tutorial-global-distribution-sql-api.md), [tabell-API](../cosmos-db/tutorial-global-distribution-table.md), och [MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) i de här artiklarna.

### <a id="TransparentSchemaMigration"></a>Öppet och konsekvent schema- eller indexhantering Databasmigrering 
Azure Cosmos DB är helt [schemaoberoende](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Den unika designen av databasmotorn kan Azure Cosmos DB för att automatiskt och indexera synkront alla data vid inmatning, utan att kräva något schema eller sekundära index från användaren. På så sätt kan du iterera ditt globalt distribuerade program snabbt utan att behöva bekymra dig om schema- eller indexhantering Databasmigrering eller samordna flera fas lanseringar av schemaändringar. Azure Cosmos DB garanterar att alla ändringar som du uttryckligen har gjort indexeringsprinciper inte leder till försämrade prestanda eller tillgänglighet.  

### <a id="ComprehensiveSLAs"></a>Omfattande serviceavtal (utöver hög tillgänglighet)
Som en globalt distribuerad databastjänst, Azure Cosmos DB erbjuder väldefinierade och omfattande serviceavtal för **tillgänglighet**, **svarstid**, **dataflöde**, och **konsekvens** för den databas som körs i global skala, oavsett antalet regioner som associeras med databasen.  

## <a id="LatencyGuarantees"></a>Svarstidsgarantier
Den viktigaste fördelen med en globalt distribuerad databastjänst som Azure Cosmos DB är att erbjuda snabb svarstid till dina data var som helst i världen. Azure Cosmos DB erbjuder garanterat låga svarstider i 99: e percentilen för olika databasoperationer. Protokollet replikering som använder Azure Cosmos DB garanterar att databasåtgärderna (läsning och skrivning) utförs alltid i regionen som är lokala för klientens. Svarstiden SLA för Azure Cosmos DB ger garantier 99: e percentilen för både läsningar, (synkront) indexerade skrivningar och frågor för olika storlekar på begäran och svar. Svarstidsgarantier för skrivningar omfattar varaktiga majoritet kvorum incheckningar i den lokala regionen.

### <a id="LatencyAndConsistency"></a>Svarstids relation med konsekvens 
För en globalt distribuerad tjänst som erbjuder stark konsekvens i en global konfiguration krävs att synkront replikera skrivningar eller synkront utföra interregionala läsningar. Hastigheten på ljust och tillförlitlighet för WAN-nätverk avgör att stark konsekvens resulterar i ökad latens och minskad tillgänglighet för databasåtgärder. Därför garanterad låg latens vid den 99: e percentilen och 99,99% tillgänglighet för alla och alla konton i flera regioner med Avslappnad konsekvens och 99,999% tillgänglighet på alla databaskonton, tjänsten för att kunna erbjuda skyddas med asynkron replikering. Den här i sin tur kräver att tjänsten måste också erbjuda [konsekvens med väldefinierade, Avslappnad modell(er)](consistency-levels.md) – svagare än starka (för att ge garantier för låg svarstid och tillgänglighet) och vi rekommenderar starkare än ”konsekvens” (med en intuitiv programmeringsmodell).

Azure Cosmos DB garanterar att en Läsåtgärd inte krävs för att kontakta repliker över flera regioner för att leverera garanterad för specifika konsekvens. På samma sätt kan det säkerställer att en skrivåtgärd inte blockeras medan data replikeras i alla regioner det vill säga skrivningar asynkront replikeras över regioner). Båda stark samt flera Avslappnad konsekvensnivåer är tillgängliga för databaskonton. 

### <a id="LatencyAndAvailability"></a>Svarstids relation med tillgänglighet 
Är de två sidorna av samma mynt svarstid och tillgänglighet. Tala om svarstid för en åtgärd i stabilt tillstånd och tillgänglighet om det förekommer fel och nätverkspartitioner. Från programs synvinkel är en långsam databasåtgärd som körs särskilja från en databas som inte är tillgänglig. 

För att skilja lång svarstid från otillgänglighet, tillhandahåller Azure Cosmos DB en absolut övre gräns för svarstiden för olika databasoperationer. Om Databasåtgärden tar längre tid än den övre gränsen för att slutföra, returnerar ett timeout-fel i Azure Cosmos DB. SERVICEAVTAL för Azure Cosmos DB-tillgänglighet garanterar att inställningarna för timeout räknas mot serviceavtal för tillgänglighet. 

### <a id="LatencyAndThroughput"></a>Svarstids relation med dataflöde
Azure Cosmos DB gör inte dig att välja mellan svarstid och dataflöde. De godkänner serviceavtalet för båda svarstid på 99: e percentilen och ger det dataflöde som du har etablerat. 

## <a id="ConsistencyGuarantees"></a>Konsekvensgarantier
Medan den [stark konsekvensmodell](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) är guld standard i data programmabilitys levereras med högre svarstid (i stabilt tillstånd) brant priset och minskad tillgänglighet (vid fel). 

Azure Cosmos DB erbjuder en väldefinierad programmeringsmodell för dig att resonera kring replikerade datakonsekvens. För att aktivera du enkelt skapa globalt distribuerade program med funktionen med flera värdar, är konsekvensmodeller som exponeras av Azure Cosmos DB avsedda att vara regionsoberoende och oberoende från regionen där läsningar och skrivningar inte hanteras. 

Konsekvens serviceavtal (SLA) för Azure Cosmos DB garanterar att 100% av läsbegäranden uppfyller konsekvens garanti för den konsekvensmodellen som angetts av dig (antingen på databaskontot eller begäran-nivå). En läsbegäran anses uppfyller konsekvens SLA, om de konsekvensgarantier som som är associerade med konsekvensnivån är uppfyllda. Följande tabell visar konsekvensproblem och konsekvens gurantees. Var och en av den här konsekvensmodell garanterar ett serviceavtal på 100%. 

|Konsekvensmodell  | Egenskaper  |
|---------|---------|
|Stark |  Linearizable  |
|Begränsad föråldring  |  Monoton Läs (inom en region), enhetligt prefix bunden föråldring &lt; K, T   |
|Session  |  Läs dina egna skrivning läst Monotonic, konsekvent prefix    |
|Konsekvent prefix  | Konsekvent prefix  |

### <a id="ConsistencyAndAvailability"></a>Konsekvenss relation med tillgänglighet
Den [är omöjligt resultatet](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) av den [CAP-satsen](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) bevisar att den är verkligen omöjligt för ett system som är tillgängliga och erbjuder linearizable konsekvens vid fel. Database-tjänsten måste du vara CP eller Asien och Stillahavsområdet, där CP system hanteringskluster tillgänglighet av linearizable konsekvens medan AP system hanteringskluster [linearizable konsekvens](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) inaktuell och ersatts med tillgänglighet. Azure Cosmos DB är aldrig strider mot den begärda konsekvensmodellen, vilket gör det formellt en CP-system. Men i praktiken är konsekvens inte en lösning där allt eller inget; Det finns flera väldefinierade konsekvensmodeller längs konsekvensspektrumet mellan linearizable och slutlig konsekvens. Identifierar flera Avslappnad konsekvensmodeller som gäller för verkliga scenarier är intuitivt för att använda i Azure Cosmos DB. Azure Cosmos DB navigerar konsekvens tillgänglighet kompromisser genom att erbjuda en [flera restriktiva ännu väldefinierade konsekvensmodeller](consistency-levels.md) och en 99,99% tillgänglighet för alla enkel databaskonton för region och 99,999% läsa och skriva tillgänglighet för alla databaskonton. 

### <a id="ConsistencyAndAvailability"></a>Konsekvenss relation med svarstid
En mer omfattande variant av CAP-satsen kallas [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), vilket även konton för svarstid och konsekvens kompromisser i ett stabilt tillstånd. Det anger att ett databassystem måste välja i ett stabilt tillstånd mellan konsekvens och svarstid. Med flera Avslappnad konsekvensmodeller (understöds av asynkron replikering och lokal läsning och skrivning beslutsförhet), garanterar Azure Cosmos DB att alla läsningar och skrivningar är lokala för Läs och Skriv regioner respektive. På så sätt kan Azure Cosmos DB att erbjuda låga svarstidsgarantier i regionen för de angivna konsekvensmodeller.  

### <a id="ConsistencyAndThroughput"></a>Konsekvenss relation med dataflöde
Eftersom implementeringen av en specifik konsekvensmodell beror på valet av en [kvorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), dataflöde också varierar beroende på valet av en konsekvensmodell. Till exempel i Azure Cosmos DB, RU-kostnad för starkt konsekventa läsningar är ungefär *dubbla* som så småningom konsekventa läsningar. I det här fallet behöver du etablera dubbla de mediereserverade enheterna för att uppnå samma dataflödet. Följande bild visar förhållandet mellan Läs kapacitet för en specifik konsekvensmodell i Azure Cosmos DB:

![Förhållandet mellan konsekvens och dataflöde](./media/distribute-data-globally-turnkey/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Dataflöde garantier 
Azure Cosmos DB kan du skala dataflöde (samt lagring), Elastiskt i alla regioner beroende på dina behov eller begäran. Följande bild visar en enskild Azure Cosmos DB-behållare vågrätt partitionerad (över tre resurspartitioner inom en region) och sedan distribueras globalt över Azure-regioner:

![Azure Cosmos DB distribueras och partitionerad behållare](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

Hämtar en Azure Cosmos DB-behållare som distribueras i två dimensioner (i) inom en region och (ii) i olika regioner. Så här gör du: 

* **Lokal distribution**: inom en region, en Azure Cosmos DB-behållare skalas vågrätt ut i *resurspartitioner*. Varje resurspartition hanterar en uppsättning nycklar och är mycket konsekvent och högtillgänglig som fysiskt representeras av fyra repliker kallas även en *replikuppsättningen* och tillstånd replikeringen mellan dessa repliker. Azure Cosmos DB är ett fullständigt resurs styrda system där en resurspartition är ansvarig för att leverera sin andel av genomströmning för budget på systemresurser som allokeras till den. Skalning av en Azure Cosmos DB-behållare är transparent för användarna. Azure Cosmos DB hanterar resource partitionerna och delar upp och slår ihop dem efter behov som lagring och dataflöde behov förändras. 
* **Global distribution**: om det är en databas för flera regioner, var och en av resurspartitioner distribueras sedan i dessa regioner. Resurspartitioner ägande samma uppsättning nycklar mellan olika regioner formuläret *partitionsuppsättning* (se [föregående bild](#ThroughputGuarantees)).  Resurspartitioner i en partitionsuppsättning samordnas med tillstånd datorreplikering i flera regioner som är kopplade till databasen. Beroende på konsekvensnivå konfigurerats, konfigureras resurspartitioner i en partitionsuppsättning dynamiskt med olika topologier (till exempel star, sammankopplad kedja, trädet osv.). 

Tack vare en högdynamiska partition hantering, belastningsutjämning och strikt resursstyrning låter Azure Cosmos DB dig Elastiskt skala dataflöde i flera Azure-regioner som associeras med en Azure Cosmos DB-behållare eller -databas. Ändrar dataflöde är en åtgärd för körning i Azure Cosmos DB. Precis som andra databasåtgärder, Azure Cosmos DB garanterar den absoluta övre gränsen för svarstid för din begäran om att ändra etablerat dataflöde. Följande bild visar till exempel en kund behållare med Elastiskt etablerat dataflöde (mellan 1-10M begäranden/sek mellan två regioner) baserat på begäran.

![Azure Cosmos DB etablerat Elastiskt dataflöde](./media/distribute-data-globally-turnkey/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Dataflödes relation med konsekvens 
Det är densamma som beskrivs i [Konsekvenss relation med dataflöde](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Dataflödes relation med tillgänglighet
Azure Cosmos DB fortsätter hantera dess hög tillgänglighet när ändringar görs etablerat dataflöde. Azure Cosmos DB hanterar transparent resurspartitioner (och utför dela, sammanfoga och klona operations) och säkerställer att åtgärderna inte försämras prestanda eller tillgänglighet, även om programmet Elastiskt ökar eller minskar dataflöde. 

## <a id="AvailabilityGuarantees"></a>Tillgänglighet
Azure Cosmos DB erbjuder en tillgänglighet på 99,99% serviceavtal (SLA) för alla databaskonton i en enda region och alla konton i flera regioner med Avslappnad konsekvens och 99,999% tillgänglighet på alla databaskonton. Enligt beskrivningen ovan, är tillgänglighetsgarantier för Azure Cosmos DB en absolut övre gräns för svarstid för varje data- och kontrollplansåtgärder. Tillgänglighetsgarantier ändras inte med antalet regioner eller geografiska avståndet mellan regioner. Tillgänglighet kan användas med avseende på både manuell samt automatisk växling vid fel. Azure Cosmos DB erbjuder transparent flera API: er som se till att ditt program kan köras mot logiska slutpunkter och transparent kan dirigera begäranden till en ny region vid fel eller specifika regionala slutpunkter i prioritetsordning för användning. Ditt program behöver inte distribueras om av en regional redundans och tillgänglighet serviceavtal bevaras vid alla tidpunkter.

### <a id="AvailabilityAndConsistency"></a>Tillgänglighetsrelationen med konsekvens, svarstid och dataflöde
Tillgänglighetsrelationen med konsekvens, svarstid och dataflöde beskrivs i avsnitten [Konsekvenss relation med tillgänglighet](#ConsistencyAndAvailability), [Svarstids relation med tillgänglighet](#LatencyAndAvailability) och [Dataflödes relation med tillgänglighet](#ThroughputAndAvailability). 

## <a id="CustomerFacingSLAMetrics"></a>Kundinriktad SLA-mått
Azure Cosmos DB exponerar transparent mått för dataflöde, svarstid, konsekvens och tillgänglighet. De här måtten är tillgängliga via programmering eller via Azure portal (se bilden nedan). Du kan också ställa in aviseringar på olika tröskelvärden med Azure Application Insights. Följande bild visar konsekvens, svarstid, dataflöde och mått på tillgänglighet som är transparent tillgängliga för varje klient:
 
![Azure Cosmos DB kund-synliga SLA-mått](./media/distribute-data-globally-turnkey/customer-slas.png)

## <a id="Next Steps"></a>Nästa steg

* [Azure Cosmos DB globalt viktiga fördelar](distribute-data-globally-benefits.md)

* [Så här konfigurerar du global databasreplikering för Azure Cosmos DB](tutorial-global-distribution-sql-api.md)

* [Så här aktiverar du multimaster för Azure Cosmos DB-konton](enable-multi-master.md)

* [Så här automatisk och manuell redundans fungerar i Azure Cosmos DB](regional-failover.md)

* [Förstå konfliktlösning i Azure Cosmos DB](multi-master-conflict-resolution.md)

* [med hjälp av multimaster med öppen källkod NoSQL-databaser](multi-master-oss-nosql.md)


## <a id="References"></a>Referenser
1. Eric Brewer. [Mot Robust distribuerade system](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Brewer. [CAP tolv år senare – hur reglerna har ändrats](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Bo Lynch. - [Brewer&#39;s antaganden och möjligheten att konsekvent och tillgängliga Partition feltoleranta webbtjänster](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Daniel Abadi. [Konsekvens kompromisser i Modern distribuerade system databasdesign](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Martin Kleppmann. [Stoppa anropa databaser CP eller Asien och Stillahavsområdet](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Peter Bailis m.fl. [Avsnittet om sannolikhetsbunden begränsad föråldring (PBS) för praktiska partiella beslutsförhet](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor och ull. [Belastning, kapacitet och tillgänglighet i kvorum system](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy och öljande. [Lineralizability: En är korrekt villkor för samtidiga objekt](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [Azure Cosmos DB-SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

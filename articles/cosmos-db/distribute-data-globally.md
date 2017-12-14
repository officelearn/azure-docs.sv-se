---
title: Distribuera data globalt med Azure Cosmos DB | Microsoft Docs
description: "Läs mer om planeten skala geo-replikering, redundans och data återställning med hjälp av globala databaser från Azure Cosmos DB, ett globalt distribuerade kommer mutli-modell database-tjänsten."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: ba5ad0cc-aa1f-4f40-aee9-3364af070725
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: arramac
ms.openlocfilehash: 4427e65930aaeac6335e31dcfe3479baa6fdb6cd
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-distribute-data-globally-with-azure-cosmos-db"></a>Hur du distribuerar data globalt med Azure Cosmos DB
Azure är allt vanligare - den har en global storleken över 30 + geografiska regioner och kontinuerligt växer. Med dess världen är en av de olika funktionerna som Azure erbjuder sina utvecklare möjlighet att skapa, distribuera och hantera enkelt globalt distribuerade program. 

[Azure Cosmos DB](../cosmos-db/introduction.md) är Microsofts globalt distribuerade databastjänst för flera datamodeller för verksamhetskritiska program. Azure Cosmos-DB tillhandahåller NYCKELFÄRDIGT global distributionsplatsen [elastisk skalbarhet av dataflöden och lagringsutrymmen](../cosmos-db/partition-data.md) över hela världen, en siffra millisekunders latens vid 99th percentilen [fem väldefinierade konsekvensnivåer](consistency-levels.md), och garanteras hög tillgänglighet, alla säkerhetskopieras av [branschledande serviceavtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Azure Cosmos DB [indexerar alla data automatiskt](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) utan att du behöver bry dig om schema- eller indexhantering. Det stöder flera modeller och dokument, nyckelvärde graf och kolumndatamodeller. Som en moln-föddes tjänst framtagen Azure Cosmos DB noggrant med flera innehavare och global distributionsplatsen från grunden upp.

**En enda Azure DB som Cosmos-samling partitioneras och distribuerade över flera Azure-regioner**

![Azure DB Cosmos-samling partitioneras och distribuerade i tre områden](./media/distribute-data-globally/global-apps.png)

Vi har lärt dig när du skapar Azure Cosmos DB lägger till global distributionsplatsen inte vara efterhand - det får inte vara ”bult på-ovanpå ett” plats ”-databassystem. Funktionerna som erbjuds av en globalt distribuerad databas som omfattar utöver att av traditionella geografiska disaster recovery (Geo DR) som erbjuder ”enskild plats”-databaser. Plats-databaser som erbjuder Geo DR-funktionen är en strikt uppsättning globalt distribuerade databaser. 

Med Azure Cosmos DB NYCKELFÄRDIGT distributionslistor utvecklare behöver inte skapa sina egna scaffold-teknik för replikering genom att använda antingen Lambda-mönster (till exempel [AWS DynamoDB replikering](https://github.com/awslabs/dynamodb-cross-region-library/blob/master/README.md)) via databasloggen eller genom att göra ”dubbla skrivningar” över flera regioner. Vi rekommenderar inte metoderna eftersom det är omöjligt att se till att dessa metoder är korrekt och ge ljud SLA: er. 

Vi ger en översikt över Azure Cosmos DB global distributionsplatsen funktioner i den här artikeln. Dessutom beskrivs Azure Cosmos DB unik metod för att tillhandahålla heltäckande SLA: er. 

## <a id="EnableGlobalDistribution"></a>Aktivera NYCKELFÄRDIGT global distributionsplatsen
Azure Cosmos-DB tillhandahåller följande funktioner som gör det möjligt att enkelt skriva planeten skala program. Dessa funktioner är tillgängliga via Azure Cosmos DB resource provider-baserad [REST API: er](https://docs.microsoft.com/rest/api/documentdbresourceprovider/) samt Azure-portalen.

### <a id="RegionalPresence"></a>Allt vanligare regionala förekomst 
Azure ständigt växande geografiska sig genom att ta [nya områden](https://azure.microsoft.com/regions/) online. Azure Cosmos-databasen är tillgänglig i alla nya Azure-regioner som standard. På så sätt kan du associera en geografisk region med din Azure Cosmos DB databaskonto som Azure öppnar den nya regionen för företag.

### <a id="UnlimitedRegionsPerAccount"></a>Associera ett obegränsat antal regioner med din Azure DB som Cosmos-databaskonto
Azure Cosmos-DB kan du associera valfritt antal Azure-regioner med ditt konto för Azure DB som Cosmos-databasen. Utanför geobegränsning begränsningar (till exempel Kina, Tyskland) finns det inga begränsningar för antalet regioner som kan vara kopplad till ditt konto för Azure DB som Cosmos-databasen. Följande bild visar ett databaskonto som konfigurerats för över 25 Azure-regioner.  

**En klients Azure Cosmos DB databasen konto spanning 25 Azure-regioner**

![Azure DB Cosmos-databaskonto utsträckning 25 Azure-regioner](./media/distribute-data-globally/spanning-regions.png)

### <a id="PolicyBasedGeoFencing"></a>Principbaserad geobegränsning
Azure Cosmos-DB är avsedd att har principbaserad geobegränsning funktioner. Geobegränsning är en viktig komponent så databegränsningar för styrning och efterlevnad och förhindra att associera en viss region med ditt konto. Exempel på geobegränsning inkluderar (men inte är begränsade till) scope global distributionsplatsen till regioner inom ett suveräna moln (till exempel Kina och Tyskland) eller i ett government skatt (till exempel Australien). Principerna kontrolleras med hjälp av metadata för din Azure-prenumeration.

### <a id="DynamicallyAddRegions"></a>Dynamiskt lägga till och ta bort områden
Azure Cosmos-DB kan du lägga till (koppla) eller ta bort (koppla bort) regioner till ditt konto vid någon tidpunkt (se [föregående bild](#UnlimitedRegionsPerAccount)). Tack vare replikering av data mellan partitioner parallellt, garanterar Azure Cosmos DB att när en ny region är online, Azure Cosmos DB är tillgänglig inom 30 minuter var som helst i världen för upp till 100 TBs. 

### <a id="FailoverPriorities"></a>Prioriteringar för växling vid fel
Kontrollera exakt i vilken ordning regional växling vid fel när det finns flera regioner avbrott Azure Cosmos DB aktiverar kontot du associera prioritet till olika regioner som är kopplade till databasen (se följande bild). Azure Cosmos-DB garanterar att den automatisk redundans inträffar i prioritetsordning som du angav. Mer information om regional växling vid fel finns [automatisk regional växling vid fel för kontinuitet i Azure Cosmos DB](regional-failover.md).

**En klient i Azure Cosmos DB kan konfigurera redundans prioritetsordning (höger) för områden som är associerad med ett databaskonto**

![Konfigurera redundans prioriteter med Azure Cosmos DB](./media/distribute-data-globally/failover-priorities.png)

### <a id="ConsistencyLevels"></a>Flera väldefinierade konsekvenskontroll modeller för globalt replikerade databaser
Azure Cosmos-DB exponerar [flera väldefinierade konsekvensnivåer](consistency-levels.md) backas upp av SLA: er. Du kan välja en specifik konsekvent modell (från listan över tillgängliga alternativ) beroende på arbetsbelastningen/scenarier. 

### <a id="TunableConsistency"></a>Justerbara konsekvens för globalt replikerade databaser
Azure Cosmos-DB kan du åsidosätta programmässigt och slappna av standardalternativet för konsekvenskontroll på grundval per begäran vid körning. 

### <a id="DynamicallyConfigurableReadWriteRegions"></a>Dynamiskt konfigurerbara Läs- och skrivåtgärder regioner
Azure Cosmos-DB kan du konfigurera regioner (som är kopplade till databasen) för ”läsa”, ”skriva” eller ”läsa/skriva” regioner. 

### <a id="ElasticallyScaleThroughput"></a>Elastiskt skalning genomströmning över Azure-regioner
Du kan Elastiskt skala en samling Azure Cosmos DB genom etablering genomströmning programmässigt. Genomflödet tillämpas på alla regioner samlingen distribueras i.

### <a id="GeoLocalReadsAndWrites"></a>GEO-lokala läser och skriver
Den viktigaste fördelen med en globalt distribuerad databas är att erbjuda låg latens åtkomst till data var som helst i världen. Azure Cosmos-DB erbjuder låg latens garantier på P99 för olika databasåtgärder. Det garanterar att alla Läs dirigeras till den närmaste lokala skrivskyddade regionen. Om du vill hantera en läsbegäran används kvorum lokalt på den region där Läs utfärdas. Detsamma gäller för skrivningar. En skrivning bekräftas endast när en majoritet av replikerna begått varaktigt skrivningen lokalt men utan att gated på fjärranslutna repliker bekräfta skrivningar. Placera olika fungerar protokollet replikering av Azure Cosmos DB med antagande som läsning och skrivning beslutsförhet alltid är lokala för läsa och skriva regioner, respektive begäran utfärdats.

### <a id="ManualFailover"></a>Manuellt initiera regional växling vid fel
Azure Cosmos-DB kan du utlösa redundansväxling av databaskonto att verifiera den *slutpunkt till slutpunkt* tillgänglighet egenskaperna för hela programmet (utanför databasen). Eftersom både säkerhet och liveness egenskaper för identifiering och ledande val för fel är garanterat Azure Cosmos DB garanterar *noll dataförlust* för en klient-initierad manuell redundansväxling.

### <a id="AutomaticFailover"></a>Automatisk redundans
Azure Cosmos-DB stöder automatisk redundans om en eller flera regionala avbrott. Under en regional växling vid fel upprätthåller Azure Cosmos DB dess skrivskyddade svarstid, drifttid tillgänglighet, konsekvens och genomströmning SLA: er. Azure Cosmos-DB tillhandahåller ett övre gränsvärde för en automatisk redundans för att slutföra varaktighet. Detta är fönstret förlust av data under regionalt strömavbrott.

### <a id="GranularFailover"></a>Utformad för olika redundans granulariteter
Funktioner för automatisk och manuell redundans exponeras för närvarande på Granulariteten för databaskontot. Observera internt Azure Cosmos DB är utformad för att erbjuda *automatisk* växling vid fel på ökad detaljnivå med en databas, samling eller även en partition (i en samling som äger en mängd nycklar). 

### <a id="MultiHomingAPIs"></a>Flera API: er i Azure Cosmos DB
Azure Cosmos-DB kan du samverka med databasen med hjälp av antingen logiska (region oberoende) eller fysisk (regionspecifika)-slutpunkter. Om du använder logiska slutpunkter säkerställer att programmet transparent kan vara multi-homed vid redundans. Senare, fysiska slutpunkter, ange finmaskig kontroll till programmet att omdirigera läser och skriver till vissa regioner.

Du hittar information om hur du konfigurerar Läs inställningar för den [SQL API](../cosmos-db/tutorial-global-distribution-documentdb.md), [Graph API](../cosmos-db/tutorial-global-distribution-graph.md), [tabell API](../cosmos-db/tutorial-global-distribution-table.md), och [MongoDB API](../cosmos-db/tutorial-global-distribution-mongodb.md) i sina respektive länkade artiklar.

### <a id="TransparentSchemaMigration"></a>Migrering av öppet och konsekvent databasen schemat och index 
Azure Cosmos-DB är helt [schema oberoende](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf). Den unika designen av dess databasmotorn tillåter att det automatiskt och index synkront alla data som den en utan att något schema eller sekundärindex från dig. På så sätt kan du snabbt iterera globalt distribuerade program utan att oroa sig för migrering av databasen schemat och index eller koordinera med lanseringar av schemaändringar. Azure Cosmos-DB garanterar att indexera principer som du uttryckligen har gjort ändringar inte resulterar i försämring av prestanda eller tillgänglighet.  

### <a id="ComprehensiveSLAs"></a>Omfattande SLA (utöver bara hög tillgänglighet)
Som ett globalt distribuerad databas-tjänsten erbjuder Azure Cosmos DB väldefinierade SLA för **förlust av data**, **tillgänglighet**, **svarstid P99**, **genomströmning** och **konsekvenskontroll** för databasen som helhet, oavsett antalet områden som är kopplade till databasen.  

## <a id="LatencyGuarantees"></a>Latens garantier
Den viktigaste fördelen med en tjänst för globalt distribuerad databas som Azure Cosmos DB är att erbjuda låg latens tillgång till dina data var som helst i världen. Azure Cosmos-DB tillhandahåller garanterad låg svarstid P99 för olika databasåtgärder. Protokollet replikering som använder Azure Cosmos DB säkerställer att databasåtgärderna (helst både läsning och skrivning) alltid utförs i regionen som är lokala för som klienten. Svarstiden SLA för Azure Cosmos DB innehåller P99 för både läsningar (synkront) indexerade skrivningar och frågor för olika storlekar på förfrågan och svar. Latens garantier för skrivningar innehålla beständiga majoritet kvorum incheckningar inom det lokala datacentret.

### <a id="LatencyAndConsistency"></a>Latenss relationen med konsekvenskontroll 
För en global tjänst att erbjuda stark konsekvens i en global inställning behöver synkront replikera skrivningar eller synkron utföra mellan region läsningar – enstaka snabbare och tillförlitlighet för WAN-nätverk kräver att strong konsekvenskontroll resulterar i ökad latens och minskad tillgänglighet för databasåtgärder. Därför för att kunna erbjuda garanterad låg latens på P99 och 99,99% tillgänglighet för alla enskild region och konton för alla flera regioner med Avslappnad konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databasen, skyddas med tjänsten asynkron replikering. Den här i sin tur kräver att tjänsten måste också erbjuda [väldefinierade, Avslappnad konsekvenskontroll choice(s)](consistency-levels.md) – svagare än stark (för att ge garantier för låg latens och tillgänglighet) och helst starkare än ”slutliga” konsekvensen (för att erbjuda en intuitiv programmeringsmodell).

Azure Cosmos-DB säkerställer att en Läsåtgärd inte krävs för att kontakta repliker över flera regioner att leverera specifika konsekvenskontroll nivån garanti. På samma sätt ser till att en skrivning inte hämta blockeras medan data replikeras mellan alla regioner (d.v.s. skrivningar asynkront replikeras över regioner). För flera regioner databasen konton är flera Avslappnad konsekvensnivåer tillgängliga. 

### <a id="LatencyAndAvailability"></a>Latenss relationen med tillgänglighet 
Är två sidor i samma mynt svarstid och tillgänglighet. Vi om fördröjning av åtgärden i stabilt tillstånd och tillgänglighet i händelse av fel. Från programs synvinkel är en långsam körs databasåtgärd särskiljas från en databas som inte är tillgänglig. 

Skilj fördröjningar från att ger Azure Cosmos DB en absolut övre gräns på svarstiden för olika databasåtgärder. Om Databasåtgärden tar längre tid än den övre gränsen för att slutföra returnerar Azure Cosmos DB ett timeout-fel. Azure Cosmos DB tillgänglighet SLA säkerställer att inställningarna för timeout är räknas av mot tillgänglighets-SLA. 

### <a id="LatencyAndThroughput"></a>Latenss relation med genomströmning
Azure Cosmos-DB göra inte dig att välja mellan svarstid och genomströmning. Den godkänner SLA för båda svarstid P99 och leverera som du har etablerat dataflöde. 

## <a id="ConsistencyGuarantees"></a>Konsekvens garanterar
När den [stark konsekvens modellen](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) är guld standard för programmering, levereras brant priset för högre latens (i stabilt tillstånd) och minskad tillgänglighet (i händelse av fel). 

Azure Cosmos-DB erbjuder en väldefinierad programmeringsmodell för dig att orsak om replikerade datakonsekvens. För att aktivera du bygga multi-homed program är konsekvenskontroll modeller som exponeras av Azure Cosmos DB avsedda att vara region-oberoende och inte är beroende av regionen från där läsningar och skrivningar hanteras. 

SERVICENIVÅAVTAL för Azure Cosmos DB konsekvens garanterar att 100% för läsbegäranden uppfyller konsekvenskontroll garanti för konsekvensnivå som begärs av du antingen (konsekvenskontroll Standardnivå på databaskontot) eller åsidosatt värdet på begäran. En läsbegäran anses uppfyller SLA konsekvenskontroll om alla konsekvens garanterar som är associerade med konsekvensnivå är uppfyllda. I följande tabell innehåller konsekvens garanterar som motsvarar specifika konsekvensnivåer som erbjuds av Azure Cosmos DB.

**Konsekvens garanterar som är associerade med en viss konsekvensnivå i Azure Cosmos DB**

<table>
    <tr>
        <td><strong>Konsekvensnivå</strong></td>
        <td><strong>Egenskaper för konsekvenskontroll</strong></td>
        <td><strong>SLA</strong></td>
    </tr>
    <tr>
        <td rowspan="3">Session</td>
        <td>Läsa dina egna skrivning</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Monotonisk Läs</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konsekvent prefix</td>
        <td>100%</td>
    </tr>
    <tr>
        <td rowspan="3">Begränsad föråldrad</td>
        <td>Monotonisk läsa (inom ett område)</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konsekvent prefix</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Föråldrad bunden &lt; K, T</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Konsekvent prefix</td>
        <td>Konsekvent prefix</td>
        <td>100%</td>
    </tr>
    <tr>
        <td>Stark</td>
        <td>Linearizable</td>
        <td>100%</td>
    </tr>
</table>

### <a id="ConsistencyAndAvailability"></a>Konsekvenskontrolls relationen med tillgänglighet
Den [är omöjligt resultatet](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf) av den [CAP-sats](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf) bevisar att den är faktiskt omöjligt för att systemet ska vara tillgängliga och erbjuda linearizable konsekvens i händelse av fel. Database-tjänsten måste du vara CP eller AP - CP system över tillgänglighet för linearizable konsekvenskontroll medan AP system över [linearizable konsekvenskontroll](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) för tillgänglighet. Azure Cosmos-DB överskrider aldrig begärda konsekvensnivå, vilket gör det formellt en CP-system. Men i praktiken konsekvenskontroll är inte en allt eller inget lösning – det finns flera väldefinierade konsekvenskontroll modeller längs spektrumet konsekvens mellan linearizable och eventuell konsekvenskontroll. Vi har försökt att identifiera flera Avslappnad konsekvenskontroll modeller med verkligheten tillämplighet och en intuitiv programmeringsmodell i Azure Cosmos-databasen. Azure Cosmos-DB navigerar konsekvenskontroll tillgänglighet kompromisser genom att erbjuda en [flera mjukas upp ännu väldefinierade konsekvensnivåer](consistency-levels.md) och en 99,99% tillgänglighet för alla enskild region och konton för alla flera regioner med mjukas upp konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databasen. 

### <a id="ConsistencyAndAvailability"></a>Konsekvenskontrolls relation med en fördröjning
En mer omfattande variation av CAP föreslogs av Prof. Daniel Abadi och kallas [PACELC](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf), vilket även konton för fördröjning och konsekvens kompromisser i stabilt tillstånd. Det anger att databassystemet måste välja i stabilt tillstånd mellan konsekvens och svarstid. Med flera Avslappnad konsekvenskontroll modeller (understöds av asynkron replikering och lokal läsning, skrivning beslutsförhet) säkerställer Azure Cosmos DB att alla läsningar och skrivningar är lokala för läsa och skriva regioner respektive.  Detta gör att Azure Cosmos DB att erbjuda låg latens garanterar för regionen för konsekvensnivåer.  

### <a id="ConsistencyAndThroughput"></a>Konsekvenskontrolls relation med genomströmning
Eftersom implementeringen av en specifik konsekvent modell beror på valet av en [kvorum](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf), genomflödet också varierar beroende på valet av konsekvenskontroll. Till exempel i Azure Cosmos DB RU tillägget för starkt konsekvent läsningar är ungefär dubbla som läser av överensstämmelse. I det här fallet behöver du etablera dubbla RUs på din samling för att uppnå samma genomflöde.
 
**Förhållandet mellan Läs kapacitet för en specifik konsekvensnivå i Azure Cosmos DB**

![Förhållandet mellan konsekvens och dataflöde](./media/distribute-data-globally/consistency-and-throughput.png)

## <a id="ThroughputGuarantees"></a>Genomströmning garantier 
Azure Cosmos-DB kan du skala genomströmning (samt lagring), Elastiskt över olika regioner, beroende på efterfrågan. 

**En enda Azure DB som Cosmos-samling partitionerad (över tre shards) och därefter distribueras över tre Azure-regioner**

![Azure Cosmos-DB distribueras och partitionerade samlingar](../cosmos-db/media/introduction/azure-cosmos-db-global-distribution.png)

En samling Azure Cosmos DB hämtar distribueras med hjälp av två dimensioner – inom en region och sedan över regioner. Så här gör du: 

* Inom en enskild region är en samling Azure Cosmos DB skala ut vad gäller resurs partitioner. Varje resurspartition hanterar en uppsättning nycklar och är starkt konsekvent och hög tillgänglighet tack vare tillstånd replikeringen mellan en uppsättning repliker. Azure Cosmos-DB är ett fullständigt resurs styrs system där en resurspartition ansvarar för att leverera sin andel av genomströmning för budget systemresurser som allokerats till den. Skalning av en samling Azure Cosmos DB är helt transparent – Azure Cosmos DB hanterar resurs partitioner och delar upp och slår ihop den efter behov. 
* Var och en av partitionerna resurs distribueras sedan över flera regioner. Resursen partitioner äger samma uppsättning nycklar över olika regioner formuläret partitionsuppsättningen (se [föregående bild](#ThroughputGuarantees)).  Resursen partitioner i en partition är samordnad med tillstånd datorreplikering över flera regioner. Beroende på nivån konsekvenskontroll konfigurerats konfigureras resurs partitioner i en partition dynamiskt med olika topologier (till exempel star, sammankopplad kedja, träd osv.). 

Tack vare en mycket dynamisk partition hantering, belastningsutjämning och strikt resurs styrning kan Azure Cosmos DB du Elastiskt skala genomströmning över flera Azure-regioner på ett Azure DB som Cosmos-samling. Ändra dataflödet på en samling är en körningsåtgärd i Azure Cosmos-DB - som med andra databasåtgärder Azure Cosmos DB garanterar den absoluta övre gränsen på svarstiden för din begäran att ändra genomflödet. Följande bild visar exempelvis en kund samling med Elastiskt etablerat dataflöde (mellan 1-10M-begäranden per sekund mellan två regioner) baserat på efterfrågan.
 
**En kund samling med Elastiskt etablerat dataflöde (1-10M begäranden per sekund)**

![Azure Cosmos-DB etablerat Elastiskt dataflöde](./media/distribute-data-globally/elastic-throughput.png)

### <a id="ThroughputAndConsistency"></a>Genomströmnings relationen med konsekvenskontroll 
Samma som [Konsekvenskontrolls relation med genomströmning](#ConsistencyAndThroughput).

### <a id="ThroughputAndAvailability"></a>Genomströmnings relationen med tillgänglighet
Azure Cosmos-DB fortsätter att upprätthålla sin tillgänglighet när ändringarna sparas till genomflödet. Azure Cosmos-DB transparent hanterar partitioner (exempelvis dela dokument, klona operations) och säkerställer att åtgärderna inte försämra prestanda eller tillgänglighet, medan programmet Elastiskt ökar eller minskar genomflöde. 

## <a id="AvailabilityGuarantees"></a>Garantier för tillgänglighet
Azure Cosmos-DB erbjuder en 99,99% tillgänglighet SLA för alla enskild region och konton för alla flera regioner med Avslappnad konsekvens och 99,999% läsa tillgänglighet för alla konton i flera regioner databasen. Som beskrivits tidigare kan inkludera en absolut övre gräns för svarstid för alla data och kontroll plan som Azure Cosmos DB garantier för tillgänglighet. Garantier för tillgänglighet är steadfast och ändras inte med antalet regioner eller geografiska avståndet mellan regioner. Garantier för tillgänglighet tillämpas med både manuell samt automatisk redundans. Azure Cosmos-DB erbjuder transparent flera API: er som se till att programmet kan köras mot logiska slutpunkter och transparent kan vidarebefordra begäranden till den nya regionen vid redundans. Placera olika ditt program behöver inte distribueras på regional växling vid fel och tillgänglighet SLA bevaras.

### <a id="AvailabilityAndConsistency"></a>Tillgänglighetsrelationen med konsekvenskontroll, svarstid och genomströmning
Tillgänglighetsrelationen med konsekvenskontroll, svarstid och genomströmning beskrivs i [Konsekvenskontrolls relationen med tillgänglighet](#ConsistencyAndAvailability), [Svarstids relationen med tillgänglighet](#LatencyAndAvailability) och [genomströmnings relationen med tillgänglighet](#ThroughputAndAvailability). 

## <a id="GuaranteesAgainstDataLoss"></a>Garanterar och systembeteendet för ”dataförlust”
I Azure Cosmos DB görs varje partition (för en samling) hög tillgänglighet med ett antal repliker som är fördelade på minst 10 20 feldomäner. Alla skrivningar arbetar synkront och varaktigt med ett kvorum majoritet av replikerna innan de bekräftas till klienten. Asynkron replikering används med samordning mellan partitioner som sprider sig över flera regioner. Azure Cosmos-DB garanterar att det finns ingen dataförlust för en klient-initierad manuell växling vid fel. Under automatisk redundans garanterar Azure Cosmos DB en övre gränsen för intervallet konfigurerade avgränsas föråldrad i fönstret för förlust av data som en del av sitt SERVICENIVÅAVTAL.

## <a id="CustomerFacingSLAMetrics"></a>Kund-riktade servicenivåavtal (SLA)
Azure Cosmos-DB exponerar transparent mått genomflöde, svarstid, konsekvens och tillgänglighet. De här måtten är tillgängliga och programmässigt via Azure portal (se följande figur). Du kan också ställa in aviseringar på olika tröskelvärden som använder Azure Application Insights.
 
**Konsekvenskontroll, svarstid, genomflöde och tillgänglighet mått är transparent tillgängliga för varje klient**

![Azure DB Cosmos kunden synliga servicenivåavtal (SLA)](./media/distribute-data-globally/customer-slas.png)

## <a id="Next Steps"></a>Nästa steg
* Om du vill implementera globala replikering på ditt Azure DB som Cosmos-konto med hjälp av Azure portal finns [hur du utför Azure Cosmos DB globala databasreplikering med Azure-portalen](tutorial-global-distribution-documentdb.md).
* Läs om hur du implementerar multimaster arkitekturer med Azure Cosmos DB i [arkitekturer för flera master-databasen med Azure Cosmos DB](multi-region-writers.md).
* Läs mer om hur automatisk och manuell redundans fungerar i Azure Cosmos DB i [Regional växling vid fel i Azure Cosmos DB](regional-failover.md).

## <a id="References"></a>Referenser
1. Eric Brewer. [Mot Robust distribuerade system](https://people.eecs.berkeley.edu/~brewer/cs262b-2004/PODC-keynote.pdf)
2. Eric Brewer. [CAP tolv år senare – hur reglerna har ändrats](http://informatik.unibas.ch/fileadmin/Lectures/HS2012/CS341/workshops/reportsAndSlides/PresentationKevinUrban.pdf)
3. Bo Lynch. - [Brewer &#39; s antaganden och möjligheten att konsekvent, tillgänglig, Partition feltoleranta webbtjänster](http://www.glassbeam.com/sites/all/themes/glassbeam/images/blog/10.1.1.67.6951.pdf)
4. Mikael Abadi. [Konsekvenskontroll kompromisser i Modern distribuerade system databasstruktur](http://cs-www.cs.yale.edu/homes/dna/papers/abadi-pacelc.pdf)
5. Ek Kleppmann. [Stoppa anropar databaser CP eller Asien](https://martin.kleppmann.com/2015/05/11/please-stop-calling-databases-cp-or-ap.html)
6. Peter Bailis m.fl. [Probabilistic avgränsas föråldrad (PBS) för praktiska partiella beslutsförhet](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
7. Naor och ull. [Läs in, kapacitet och tillgänglighet i kvorum system](http://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf)
8. Herlihy och öljande. [Lineralizability: En är korrekt villkor för samtidiga objekt](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf)
9. [SERVICENIVÅAVTAL för Azure Cosmos-DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/)

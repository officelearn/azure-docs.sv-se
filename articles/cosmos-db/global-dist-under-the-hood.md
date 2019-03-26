---
title: Global distribution med Azure Cosmos DB – under huven
description: Den här artikeln ger teknisk information som rör global distribution av Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: a599be52575ed06cdb3a3713fc2f0915ab2f6c2b
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407495"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Globala data-distribution med Azure Cosmos DB – under huven

Azure Cosmos DB är en grundläggande tjänst i Azure, så att den har distribuerats i alla Azure-regioner över hela världen, inklusive offentlig, suveräna, Department of Defense (DoD) och government-moln. Inom ett datacenter, vi distribuera och hantera Azure Cosmos DB på enorma stämplar av datorer, var och en med dedikerad lokal lagring. Inom ett datacenter, är Azure Cosmos DB distribuerade över många kluster vart och ett potentiellt körs flera generationer av maskinvara. Datorer i ett kluster är vanligtvis fördelade på 10-20 feldomäner för hög tillgänglighet inom en region. Följande bild visar Cosmos DB globalt system topologin:

![System-topologi](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Det är nyckelfärdig global distribution i Azure Cosmos DB:** När som helst, med några få klick, eller programmässigt med ett enda API-anrop, kan du lägga till eller ta bort geografiska områden som är associerade med din Cosmos-databas. En Cosmos-databas i sin tur består av en uppsättning Cosmos-behållare. I Cosmos DB kan fungera behållare som de logiska enheterna för distribution och skalbarhet. Samlingar, tabeller och diagram som du skapar är (internt) bara Cosmos-behållare. Behållare är fullständigt schemaoberoende och ger en omfattning för en fråga. Data i en Cosmos-behållare indexeras automatiskt vid inmatning. Automatisk indexering kan du fråga data utan att behöva krångla med schema- eller indexhantering hantering, särskilt i en global konfiguration.  

- I en viss region data i en behållare som distribueras med hjälp av en partitionsnyckel, som du anger och hanteras transparent av de underliggande fysiska partitionerna (*lokal distribution*).  

- Varje fysisk partition replikeras också över geografiska regioner (*globalt*). 

När en app med Cosmos DB Elastiskt skala dataflödet för en Cosmos-behållare eller förbrukar mer lagringsutrymme, Cosmos DB transparent hanterar partition hanteringsåtgärder (dela, klona och ta bort) i alla regioner. Oberoende av skala, distribution eller fel, fortsätter Cosmos DB att tillhandahålla en enskild systemavbildning av data i behållare som distribueras globalt över flera regioner.  

I följande bild visas data i en behållare distribueras i två dimensioner – inom en region och i regioner över hela världen:  

![Fysiska partitioner](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

En fysisk partition implementeras av en grupp av repliker, kallas en *replikuppsättningen*. Varje dator är värd för hundratals repliker som motsvarar olika fysiska partitioner inom en fast uppsättning processer som du ser i bilden ovan. Repliker som motsvarar de fysiska partitionerna placeras dynamiskt och belastningsutjämnas mellan datorer inom ett kluster och datacenter inom en region.  

En replik tillhör unikt en Azure Cosmos DB-klient. Varje replik har en instans av Cosmos DB [databasmotorn](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), som hanterar resurserna som de associera index. Cosmos DB database engine fungerar på ett atom-postsekvensbaserad (ARS) utifrån typen. Motorn är oberoende av konceptet med ett schema, suddar ut gränsen mellan värdena struktur och instans för poster. Cosmos DB ger fullständig schemat agnosticism genom att automatiskt indexera allt vid inmatning på ett effektivt sätt, där användarna kan fråga efter deras globalt distribuerade data utan att behöva bry dig om schema- eller indexhantering.

Cosmos-databasmotor består av komponenter, inklusive implementering av flera samordning primitiver, språkkörningar, frågeprocessorn, lagring och indexering delsystem som ansvarar för transaktionell lagring och indexering av data, respektive. För att ge hållbarhet och hög tillgänglighet databasmotorn bevaras dess data och index på SSD: er och replikerar den bland database engine-instanser inom repliken-uppsättning respektive. Större klienter motsvarar högre skalning av dataflöde och lagring och ha antingen större eller fler repliker, eller bådadera. Alla komponenter i systemet är fullständigt asynkron – ingen tråd blockerar någonsin och varje tråd fungerar tillfällig utan att det medför några onödiga tråd-växlar. Hastighetsbegränsande och ytterligare belastning är inkopplade över hela spektrumet från åtkomstkontroll till alla i/o-sökvägar. Cosmos-databasmotor är utformat att utnyttja detaljerade samtidighet och leverera högt dataflöde när du arbetar inom frugal mängder systemresurser.

Cosmos DB: s global distribution är beroende av två viktiga abstraktioner – *-replikuppsättningar* och *partitionsuppsättningarna*. En replikuppsättning är en modulbaserad Lego-block för samordning och en partitionsuppsättning är ett dynamiskt överlägg på en eller flera geografiskt distribuerade fysiska partitioner. Att förstå hur globala distributionen fungerar finns det viktigt att förstå dessa två viktiga abstraktioner. 

## <a name="replica-sets"></a>Replikuppsättningar

En fysisk partition är materialiserad som en fristående och dynamiskt belastningsutjämnad grupp av repliker som sprids över flera feldomäner, kallas en replikuppsättning. Den här uppsättningen implementerar sammantaget protokollet replikerade tillstånd datorn för att göra data inom fysisk partition med hög tillgänglighet, hållbar och konsekvent. Replikuppsättningen medlemskap *N* är dynamisk – det håller växlar mellan *NMin* och *nmax* baserat på felen, administrativa åtgärder och tiden för misslyckades återskapa eller återställa replikerna. Baserat på medlemskap ändras kan replikeringen protokollet också Omkonfigurerar storleken på läsa och skriva beslutsförhet. Om du vill distribuera ett enhetligt sätt det dataflöde som har tilldelats en viss fysisk partition, ska vi använda två idéer: 

- Först är kostnaden för bearbetning av skrivbegäranden på ledaren högre än kostnaden för tillämpning av uppdateringarna på i det följande. På motsvarande sätt ledare budgeterade mer systemresurser än följare. 

- För det andra, så långt möjligt Läs kvorum för en viss konsekvensnivå uteslutande består av följande replikerna. Vi undvika att kontakta ansvariga för att betjäna läsningar såvida inte. Vi använder ett antal idéer från research på relationen mellan [belastning och kapacitet](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i kvorum-datorer för den [fem konsekvensmodeller](consistency-levels.md) som har stöd för Cosmos DB.  

## <a name="partition-sets"></a>Partitionsuppsättningarna

En grupp med fysiska partitioner från var och en av de konfigurerade med databasregioner Cosmos består för att hantera samma uppsättning nycklar replikeras över alla konfigurerade regioner. Den här högre samordning primitiv kallas för en partition-set - ett geografiskt distribuerade dynamisk överlägg över fysiska partitioner som hanterar en viss uppsättning nycklar. När en viss fysisk partition (en-replikuppsättning) är begränsad i ett kluster, en partitionsuppsättning kan sträcka sig över kluster, Datacenter och geografiska områden som visas i följande bild:  

![Partitionsuppsättningarna](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Du kan tänka dig en partitionsuppsättning som geografiskt spridda ”super-replikuppsättningar”, som består av flera-replikuppsättningar ägande samma uppsättning nycklar. Liknar en replikuppsättning, en partition-set medlemskap är också dynamiskt – det varierar beroende på implicit fysisk partition hanteringsåtgärder Lägg till/ta bort nya partitioner till och från en viss partitionsuppsättning (till exempel när du skalar ut dataflöde på en behållare, Lägg till/ta bort en region till din Cosmos-databas, eller när fel uppstår) Tack vare att var och en av partitionerna (i en partitionsuppsättning) hantera partition-set-medlemskap i en egen replikuppsättningen, medlemskapet är fullständigt decentraliserad och mycket tillgängligt. Topologin för överlägg mellan fysiska partitioner upprättas också under omkonfiguration av en partitionsuppsättning. Topologin väljs dynamiskt baserat på konsekvensnivå, geografiska avstånd och tillgänglig nätverksbandbredd mellan käll- och mål fysiska partitioner.  

Tjänsten kan du konfigurera Cosmos-databaser med en enda skrivregionen eller flera Skriv-regioner och beroende på valet-partitionsuppsättningarna är konfigurerade för att godkänna skrivningar i exakt en eller alla regioner. Systemet använder ett två nivåer, kapslade konsensus-protokoll – en nivå fungerar inom replikerna för en replikuppsättning av en fysisk partition som tar emot skrivningar och den andra fungerar på nivån för en partitionsuppsättning att ge fullständig skrivordning garanterar för alla allokerade skrivningar i partition-uppsättningen. Den här flera lager, kapslade konsensus är viktigt för implementeringen av våra strikta serviceavtal för hög tillgänglighet, samt implementeringen av konsekvensmodeller som Cosmos DB erbjuder sina kunder.  

## <a name="conflict-resolution"></a>Konfliktlösning

Våra design för update-spridning, konfliktlösning och orsakssamband spåra inspiration från det tidigare arbetet på [klövsjukeepidemin algoritmer](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) och [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) system. Kärnor på idéer har texten, överlevt och ange en lämplig referensram för att kommunicera Cosmos-DB systemdesign, har de också genomgått betydande omvandling som vi tillämpade dem Cosmos DB-system. Detta krävs, eftersom de tidigare system utformades varken med resurs-styrning eller med en skala som Cosmos DB måste att fungera och inte heller att tillhandahålla funktioner (till exempel begränsad föråldring konsekvens) och strikta och omfattande Serviceavtal som Cosmos DB levererar till sina kunder.  

Kom ihåg att en partitionsuppsättning distribueras över flera regioner och följer Cosmos DB-databaser (multimaster) replikering-protokollet för att replikera data mellan de fysiska partitioner som består av en viss partitionsuppsättning. Varje fysisk partition (av en partitionsuppsättning) accepterar skrivningar och används vanligtvis läsningar för klienter som är lokala för den regionen. Skrivningar som accepteras av en fysisk partition inom en region är varaktigt allokerat och gjort högtillgänglig inom fysisk partition innan de blir erkända till klienten. Här är preliminär skrivningar och sprids till andra fysiska partitioner i partition-uppsättningen med hjälp av en anti entropi-kanal. Klienter kan begära preliminär eller allokerade skrivningar genom att skicka en rubrik för begäran. Anti entropi spridning (inklusive frekvensen för spridning) är dynamiskt, baserat på topologin för partition-set, regionala närhet fysiska partitioner och konsekvens som konfigurerats nivå. Cosmos DB följer ett schema för primära genomförandet med en dynamiskt valda avgörandet partition inom en partitionsuppsättning. Avgörandet valet är dynamisk och är en del av omkonfiguration av partitionen-set baserat på överlägget topologi. Allokerade skrivningar (inklusive flera row/batchar uppdateringar) garanterat beställas. 

Vi använder kodade vektor klockor (som innehåller region-ID och logiska klockor motsvarande till varje nivå av konsensus vid replikuppsättningen- och partitionsuppsättning, respektive) för orsakssamband spårning och versionen vektorer att identifiera och lösa Uppdateringskonflikter. Topologin och peer val av algoritmen är utformade för att se till att fasta och minimal lagring och minimal nätverksresurser för versionsvektorer. Algoritmen garanterar egenskapen strikt konvergens.  

För Cosmos-databaser som konfigurerats med flera Skriv-regioner, innehåller systemet ett antal flexibla automatisk konflikt lösning principer för utvecklare att välja bland, inklusive: 

- Senaste-Write-Wins (LWW) som normalt används en systemdefinierade tidsstämpelsegenskapen (som baseras på protokollet tidssynkronisering clock). Cosmos DB kan du ange någon annan anpassad numeriska egenskap som ska användas för konfliktlösning.  
- Programdefinierade anpassade principen för konfliktlösning (uttryckt via merge procedurer) som är avsett för programdefinierade semantik avstämningen av konflikter. De här procedurerna hämta anropas vid identifiering av Skriv-Skriv-konflikt inom ramen för en databastransaktion på serversidan. Systemet innehåller exakt en gång garanterar för körning av en merge-procedur som en del av protokollet åtagande. Det finns flera exempel som är tillgängliga för dig att experimentera med.  

## <a name="consistency-models"></a>Konsekvensmodeller

Om du konfigurerar din Cosmos-databas med en eller flera Skriv-regioner kan välja du mellan fem väldefinierade konsekvensmodeller. Med stöd för att aktivera flera Skriv-regioner som nyligen lagts till följer några viktiga aspekter av konsekvensnivåerna:  

Som garanterar tidigare, begränsad föråldring konsekvens att alla läsningar inom k-prefix eller t sekunder från den senaste skrivningen i någon av regionerna. Dessutom är läsningar med begränsad föråldring, konsekvens garanterat Monoton och med konsekvent prefix garantier. Protokollet anti entropi fungerar på ett sätt som rate-limited och garanterar att ackumuleras inte prefixen och givet på skrivningar har inte tillämpas. Som tidigare, läsa sessionen konsekvens garanterar Monoton läsa Monoton skriva dina egna skrivningar, skrivning-följer-Läs- och konsekvent prefix att garanterar över hela världen. För databaser som konfigurerats med stark konsekvens, fördelarna med multi-hantering (låg skrivfördröjningen, hög skrivning tillgänglighet) gäller inte på grund av synkron replikering över regioner.

Semantiken för de fem konsekvensmodeller i Cosmos DB beskrivs [här](consistency-levels.md) och matematiskt visas med hjälp av en övergripande TLA + specifikationer [här](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Nästa steg

Lär dig sedan att konfigurera global distribution med hjälp av följande artiklar:

* [Så här konfigurerar du klienter för flera värdar](how-to-manage-database-account.md#configure-clients-for-multi-homing)
* [Lägg till/ta bort regioner från ditt databaskonto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Så här skapar du en principen för anpassad konfliktlösning för SQL-API-konton](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

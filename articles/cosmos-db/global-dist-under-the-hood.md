---
title: Global distribution med Azure Cosmos DB – under huven
description: Den här artikeln ger teknisk information som rör global distribution av Azure Cosmos DB
author: dharmas-cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: dharmas
ms.reviewer: sngun
ms.openlocfilehash: ce943fbed0774667100f6de4c60f91c0b02de6c3
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615344"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Global data distribution med Azure Cosmos DB – under huven

Azure Cosmos DB är en grundläggande tjänst i Azure, så den distribueras i alla Azure-regioner över hela världen, inklusive offentlig, suverän, departementet försvar (DoD) och myndigheter. Inom ett datacenter, vi distribuera och hantera Azure Cosmos DB på enorma stämplar av datorer, var och en med dedikerad lokal lagring. Inom ett datacenter, är Azure Cosmos DB distribuerade över många kluster vart och ett potentiellt körs flera generationer av maskinvara. Datorer i ett kluster sprids vanligt vis över 10-20 fel domäner för hög tillgänglighet inom en region. Följande bild visar Cosmos DB globalt system topologin:

![System-topologi](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Global distribution i Azure Cosmos DB är nyckel färdiga:** När som helst, med några klick eller program mässigt med ett enda API-anrop, kan du lägga till eller ta bort de geografiska regioner som är kopplade till Cosmos-databasen. En Cosmos-databas, i sin tur, består av en uppsättning Cosmos-behållare. I Cosmos DB kan fungera behållare som de logiska enheterna för distribution och skalbarhet. Samlingar, tabeller och diagram som du skapar är (internt) bara Cosmos-behållare. Behållare är fullständigt schema-oberoende och anger en omfattning för en fråga. Data i en Cosmos-behållare indexeras automatiskt vid inmatning. Med automatisk indexering kan användare fråga data utan besvär i schema-eller index hantering, särskilt i en globalt distribuerad installation.  

- I en specifik region distribueras data i en behållare med hjälp av en partitionsnyckel som du anger och som hanteras transparent av de underliggande fysiska partitionerna (*lokal distribution*).  

- Varje fysisk partition replikeras också över geografiska regioner (*global distribution*). 

När en app som använder Cosmos DB elastiskt skala data flödet i en Cosmos-behållare eller använder mer lagrings utrymme, hanterar Cosmos DB transparent hanterings åtgärder för partitioner (dela, klona och ta bort) över alla regioner. Oberoende av skala, distribution eller fel, fortsätter Cosmos DB att tillhandahålla en enskild systemavbildning av data i behållare som distribueras globalt över flera regioner.  

Som du ser i följande bild distribueras data i en behållare längs två dimensioner – inom en region och mellan regioner, globalt:  

![Fysiska partitioner](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

En fysisk partition implementeras av en grupp repliker, som kallas *replik uppsättning*. Varje dator är värd för hundratals repliker som motsvarar olika fysiska partitioner i en fast uppsättning processer som visas i bilden ovan. Repliker som motsvarar de fysiska partitionerna placeras dynamiskt och belastningsutjämnas mellan datorer inom ett kluster och datacenter inom en region.  

En replik tillhör unikt en Azure Cosmos DB-klient. Varje replik har en instans av Cosmos DB [databasmotorn](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), som hanterar resurserna som de associera index. Cosmos-databasmotorn använder sig av en-baserad (Atom-Record-Sequence)-baserad typ system. Motorn är oberoende till begreppet schema, vilket gör att det går att minska gränserna mellan struktur-och instans värden för poster. Cosmos DB ger fullständig schemat agnosticism genom att automatiskt indexera allt vid inmatning på ett effektivt sätt, där användarna kan fråga efter deras globalt distribuerade data utan att behöva bry dig om schema- eller indexhantering.

Cosmos-databasmotorn består av komponenter, inklusive implementering av flera koordinerande primitiver, language runtime, Query processor och de lagrings-och indexerings under system som ansvarar för transaktions lagring och indexering av data, benämningar. För att ge hållbarhet och hög tillgänglighet databasmotorn bevaras dess data och index på SSD: er och replikerar den bland database engine-instanser inom repliken-uppsättning respektive. Större klienter motsvarar högre skalbarhet och lagrings utrymme och har antingen större eller fler repliker eller både och. Alla komponenter i systemet är fullständigt asynkron – ingen tråd blockerar någonsin och varje tråd fungerar tillfällig utan att det medför några onödiga tråd-växlar. Hastighetsbegränsande och ytterligare belastning är inkopplade över hela spektrumet från åtkomstkontroll till alla i/o-sökvägar. Cosmos-databasmotorn är utformad för att utnyttja detaljerad samtidighet och leverera högt data flöde samtidigt som du arbetar inom Frugal-mängder system resurser.

Cosmos DB global distribution är beroende av två nyckel abstraktioner – *replik uppsättningar* och *partitionsuppsättningar*. En replikuppsättning är en modulbaserad Lego-block för samordning och en partitionsuppsättning är ett dynamiskt överlägg på en eller flera geografiskt distribuerade fysiska partitioner. Att förstå hur globala distributionen fungerar finns det viktigt att förstå dessa två viktiga abstraktioner. 

## <a name="replica-sets"></a>Replikuppsättningar

En fysisk partition är materialiserad som en självhanterad och dynamiskt belastningsutjämnad grupp av repliker som sprids över flera fel domäner, som kallas replik uppsättning. Den här uppsättningen implementerar det replikerade tillstånds maskin protokollet för att göra data i den fysiska partitionen hög tillgänglig, varaktig och konsekvent. Replikens medlemskap *N* är dynamisk – det håller på att variera mellan *Nmin.* och *nmax.* baserat på fel, administrativa åtgärder och tiden för misslyckade repliker för att återskapa/återställa. Baserat på medlemskap ändras kan replikeringen protokollet också Omkonfigurerar storleken på läsa och skriva beslutsförhet. För att enhetligt distribuera det data flöde som har tilldelats till en specifik fysisk partition, använder vi två idéer: 

- Först är kostnaden för att bearbeta Skriv förfrågningarna på ledaren högre än kostnaden för att tillämpa uppdateringarna på följaren. På motsvarande sätt ledare budgeterade mer systemresurser än följare. 

- För det andra, så långt möjligt Läs kvorum för en viss konsekvensnivå uteslutande består av följande replikerna. Vi undvika att kontakta ansvariga för att betjäna läsningar såvida inte. Vi använder ett antal idéer från forskningen som är gjord på förhållandet mellan [belastning och kapacitet](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i de kvorumbaserade systemen för de [fem konsekvens modeller](consistency-levels.md) som Cosmos DB stöder.  

## <a name="partition-sets"></a>Partitionsuppsättningarna

En grupp med fysiska partitioner, en från var och en av de som kon figurer ATS med Cosmos-databas regionerna, är sammansatt för att hantera samma uppsättning nycklar som replikeras i alla konfigurerade regioner. Denna högre samordning är en partitions *uppsättning* – ett geografiskt distribuerat dynamiskt överlägg med fysiska partitioner som hanterar en specifik uppsättning nycklar. Även om en angiven fysisk partition (en replik uppsättning) är begränsad i ett kluster, kan en partitionsuppsättning spänna över kluster, data Center och geografiska regioner som visas på bilden nedan:  

![Partitionsuppsättningarna](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Du kan tänka dig en partitionsuppsättning som geografiskt spridda ”super-replikuppsättningar”, som består av flera-replikuppsättningar ägande samma uppsättning nycklar. På samma sätt som en replik uppsättning är en partitions uppsättnings medlemskap också dynamiskt – den varierar baserat på implicita hanterings åtgärder för fysiska partitioner för att lägga till/ta bort nya partitioner i/från en angiven partitionsuppsättning (till exempel när du skalar upp data flödet på en behållare, Lägg till/ta bort en region i Cosmos-databasen eller när fel inträffar. Genom att ha var och en av partitionerna (av en partitionsuppsättning) hanterar du medlemskapet för partitionsuppsättningen i sin egen replik uppsättning, är medlemskapet helt decentraliserat och med hög tillgänglighet. Topologin för överlägg mellan fysiska partitioner upprättas också under omkonfiguration av en partitionsuppsättning. Topologin väljs dynamiskt baserat på konsekvens nivå, geografiskt avstånd och tillgänglig nätverks bandbredd mellan käll-och mål-fysiska partitionerna.  

Tjänsten kan du konfigurera Cosmos-databaser med en enda skrivregionen eller flera Skriv-regioner och beroende på valet-partitionsuppsättningarna är konfigurerade för att godkänna skrivningar i exakt en eller alla regioner. Systemet använder ett kapslat samförstånds protokoll på två nivåer – en nivå fungerar inom replikerna för en replik uppsättning av en fysisk partition som accepterar skrivningar, och den andra fungerar på samma nivå som en partitions uppsättning för att ge fullständiga ordnings garantier för alla genomförda skrivningar i partitionsuppsättningen. Den här flera lager, kapslade konsensus är viktigt för implementeringen av våra strikta serviceavtal för hög tillgänglighet, samt implementeringen av konsekvensmodeller som Cosmos DB erbjuder sina kunder.  

## <a name="conflict-resolution"></a>Konfliktlösning

Våra design för update-spridning, konfliktlösning och orsakssamband spåra inspiration från det tidigare arbetet på [klövsjukeepidemin algoritmer](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) och [Bayou](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) system. Kärnor på idéer har texten, överlevt och ange en lämplig referensram för att kommunicera Cosmos-DB systemdesign, har de också genomgått betydande omvandling som vi tillämpade dem Cosmos DB-system. Detta krävdes, eftersom de tidigare systemen inte har utformats än med resurs styrningen eller den skala som Cosmos DB måste fungera med, eller för att tillhandahålla funktioner (till exempel utbegränsad föråldrad konsekvens) och strängare och omfattande service avtal som Cosmos DB levererar till sina kunder.  

Kom ihåg att en partitionsuppsättning distribueras över flera regioner och följer Cosmos DB-databaser (multimaster) replikering-protokollet för att replikera data mellan de fysiska partitioner som består av en viss partitionsuppsättning. Varje fysisk partition (av en partitionsuppsättning) accepterar skrivningar och hanterar läsningar vanligt vis till de klienter som är lokala i den regionen. Skrivningar som godkänns av en fysisk partition inom en region är varaktigt allokerade och har hög tillgänglighet i den fysiska partitionen innan de bekräftas till klienten. Här är preliminär skrivningar och sprids till andra fysiska partitioner i partition-uppsättningen med hjälp av en anti entropi-kanal. Klienter kan begära preliminär eller allokerade skrivningar genom att skicka en rubrik för begäran. Anti entropi spridning (inklusive frekvensen för spridning) är dynamiskt, baserat på topologin för partition-set, regionala närhet fysiska partitioner och konsekvens som konfigurerats nivå. Cosmos DB följer ett schema för primära genomförandet med en dynamiskt valda avgörandet partition inom en partitionsuppsättning. Avgörandet valet är dynamisk och är en del av omkonfiguration av partitionen-set baserat på överlägget topologi. Allokerade skrivningar (inklusive flera row/batchar uppdateringar) garanterat beställas. 

Vi använder kodade vektor klockor (som innehåller region-ID och logiska klockor motsvarande till varje nivå av konsensus vid replikuppsättningen- och partitionsuppsättning, respektive) för orsakssamband spårning och versionen vektorer att identifiera och lösa Uppdateringskonflikter. Topologin och peer val av algoritmen är utformade för att se till att fasta och minimal lagring och minimal nätverksresurser för versionsvektorer. Algoritmen garanterar egenskapen strikt konvergens.  

För Cosmos-databaser som konfigurerats med flera Skriv-regioner, innehåller systemet ett antal flexibla automatisk konflikt lösning principer för utvecklare att välja bland, inklusive: 

- **Senaste-skrivning-WINS (LWW)** , som standard använder en systemdefinierad timestamp-egenskap (som baseras på Time-Synchronize Clock-protokollet). Cosmos DB kan du ange någon annan anpassad numeriska egenskap som ska användas för konfliktlösning.  
- **Programdefinierad (anpassad) konflikt lösnings princip** (uttryckt i sammanslagnings procedurer), som är utformat för programdefinierad semantik för konflikter. De här procedurerna hämta anropas vid identifiering av Skriv-Skriv-konflikt inom ramen för en databastransaktion på serversidan. Systemet ger exakt en garanti för körning av en sammanfognings procedur som en del av åtagande protokollet. Det finns [flera olika lösnings exempel för konflikter](how-to-manage-conflicts.md) som du kan spela med.  

## <a name="consistency-models"></a>Konsekvensmodeller

Oavsett om du konfigurerar Cosmos-databasen med en eller flera Skriv regioner kan du välja bland de fem väldefinierade konsekvens modellerna. Med flera Skriv regioner är följande några viktiga aspekter av konsekvens nivåerna:  

Den begränsade inaktuella inkonsekventa konsekvensen garanterar att alla läsningar ligger inom *n* -prefix eller *T* sekunder från den senaste skrivningen i någon av regionerna. Dessutom garanteras läsning med utjämnad föråldrad konsekvens som enkel och konsekvent prefix. Protokollet anti entropi fungerar på ett sätt som rate-limited och garanterar att ackumuleras inte prefixen och givet på skrivningar har inte tillämpas. Konsekvens i sessioner garanterar enkel och enkel läsning, enkel och enkel skrivning, läsning av dina egna skrivningar, Skriv följande Läs och konsekventa prefix-garantier, globalt. För databaserna som kon figurer ATS med stark konsekvens gäller fördelarna (låg Skriv fördröjning, hög Skriv fördröjning) för flera Skriv regioner, på grund av synkron replikering mellan regioner.

Semantiken för de fem konsekvens modellerna i Cosmos DB beskrivs [här](consistency-levels.md)och matematiskt beskrivs med en tla +-specifikationer på hög nivå [här](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Nästa steg

Lär dig sedan att konfigurera global distribution med hjälp av följande artiklar:

* [Lägg till/ta bort regioner från ditt databaskonto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Så här konfigurerar du klienter för flera värdar](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Så här skapar du en anpassad lösnings princip för konflikt](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

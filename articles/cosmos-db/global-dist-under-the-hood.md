---
title: Global distribution med Azure Cosmos DB- under huven
description: Den här artikeln innehåller teknisk information om global distribution av Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a46a69476a2ad6550bc7b3a533fd09565d461db3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74872136"
---
# <a name="global-data-distribution-with-azure-cosmos-db---under-the-hood"></a>Global datadistribution med Azure Cosmos DB - under huven

Azure Cosmos DB är en grundläggande tjänst i Azure, så den distribueras över alla Azure-regioner över hela världen, inklusive offentliga, suveräna, Försvarsdepartementet (DoD) och myndighetsmoln. Inom ett datacenter distribuerar och hanterar vi Azure Cosmos DB på massiva stämplar av datorer, alla med dedikerad lokal lagring. I ett datacenter distribueras Azure Cosmos DB över många kluster, var och en som eventuellt kör flera generationer av maskinvara. Datorer i ett kluster är vanligtvis spridda över 10-20 feldomäner för hög tillgänglighet inom en region. Följande bild visar Cosmos DB globala distributionssystem topologi:

![Systemtopologi](./media/global-dist-under-the-hood/distributed-system-topology.png)

**Global distribution i Azure Cosmos DB är nyckelfärdig:** När som helst, med några klick eller programmässigt med ett enda API-anrop, kan du lägga till eller ta bort de geografiska områden som är associerade med Cosmos-databasen. En Cosmos-databas består i sin tur av en uppsättning Cosmos-behållare. I Cosmos DB fungerar behållare som logiska enheter för distribution och skalbarhet. Samlingarna, tabellerna och diagrammen som du skapar är (internt) bara Cosmos-behållare. Behållare är helt schemaoberoende och ger ett omfång för en fråga. Data i en Cosmos-behållare indexeras automatiskt vid inmatning. Automatisk indexering gör det möjligt för användare att fråga data utan problem med schema eller indexhantering, särskilt i en globalt distribuerad installation.  

- I en viss region distribueras data i en behållare med hjälp av en partitionsnyckel, som du tillhandahåller och hanteras transparent av de underliggande fysiska partitionerna (*lokal distribution*).  

- Varje fysisk partition replikeras också över geografiska regioner (*global distribution*). 

När en app som använder Cosmos DB elastiskt skalar dataflödet på en Cosmos-behållare eller förbrukar mer lagringsutrymme hanterar Cosmos DB partitionshanteringsåtgärder (split, klon, borttagning) i alla regioner. Cosmos DB är oberoende av skala, distribution eller fel och fortsätter att tillhandahålla en enda systemavbildning av data i behållarna, som är globalt fördelade över valfritt antal regioner.  

Som visas i följande bild fördelas data i en behållare längs två dimensioner – inom en region och mellan regioner över hela världen:  

![fysiska partitioner](./media/global-dist-under-the-hood/distribution-of-resource-partitions.png)

En fysisk partition implementeras av en grupp repliker, en så kallad *replikuppsättning*. Varje dator är värd hundratals repliker som motsvarar olika fysiska partitioner inom en fast uppsättning processer som visas i bilden ovan. Repliker som motsvarar de fysiska partitionerna placeras dynamiskt och belastningsutjämnas över datorerna inom ett kluster och datacenter inom en region.  

En replik tillhör unikt en Azure Cosmos DB-klientorganisation. Varje replik är värd för en instans av Cosmos [DB:s databasmotor](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), som hanterar resurser samt tillhörande index. Cosmos databasmotor fungerar på ett ATOM-record-sequence (ARS) baserat typsystem. Motorn är agnostiker till begreppet ett schema, sudda ut gränsen mellan strukturen och förekomstvärden för poster. Cosmos DB uppnår fullständig schemaagnosticism genom att automatiskt indexera allt vid intag på ett effektivt sätt, vilket gör det möjligt för användare att fråga sina globalt distribuerade data utan att behöva hantera schema eller indexhantering.

Cosmos databasmotor består av komponenter, inklusive implementering av flera samordningsimpaler, språkkörningar, frågeprocessorn och delsystemen lagring och indexering som ansvarar för transaktionslagring och indexering av data. Respektive. För att ge hållbarhet och hög tillgänglighet, behåller databasmotorn sina data och index på SSD och replikerar den bland databasmotorinstanserna i replikuppsättningarna respektive. Större klienter motsvarar högre skala av dataflöde och lagring och har antingen större eller fler repliker eller båda. Varje komponent i systemet är helt asynkron - ingen tråd någonsin block, och varje tråd gör kortlivade arbete utan att ådra sig några onödiga tråd växlar. Hastighetsbegränsande och mottryck är plumbed över hela stacken från antagning kontroll till alla I / O-vägar. Cosmos databasmotor är utformad för att utnyttja finkornig samtidighet och för att leverera hög genomströmning samtidigt som den arbetar inom sparsamma mängder systemresurser.

Cosmos DB:s globala distribution bygger på två viktiga abstraktioner – *replikuppsättningar* och *partitionsuppsättningar*. En replikuppsättning är ett modulärt Lego-block för samordning, och en partitionsuppsättning är en dynamisk överlagring av en eller flera geografiskt distribuerade fysiska partitioner. För att förstå hur global distribution fungerar måste vi förstå dessa två viktiga abstraktioner. 

## <a name="replica-sets"></a>Replikuppsättningar

En fysisk partition materialiseras som en självhanterad och dynamiskt belastningsbalanserad grupp av repliker spridda över flera feldomäner, en så kallad replikuppsättning. Den här uppsättningen implementerar tillsammans det replikerade tillståndsmaskinprotokollet för att göra data i den fysiska partitionen mycket tillgängliga, varaktiga och konsekventa. Replikuppsättningens medlemskap *N* är dynamiskt – det fortsätter att fluktuera mellan *NMin* och *NMax* baserat på fel, administrativa åtgärder och tiden för misslyckade repliker att återskapa/återställa. Baserat på medlemskapsändringarna konfigurerar replikeringsprotokollet också om storleken på läs- och skrivkvorum. Om du vill fördela dataflödet som tilldelas en viss fysisk partition på ett enhetligt sätt använder vi två idéer: 

- För det första är kostnaden för att bearbeta skrivbegäranden på ledaren högre än kostnaden för att tillämpa uppdateringarna på efterföljaren. På motsvarande sätt är ledaren budgeterad mer systemresurser än anhängare. 

- För det andra, så långt det är möjligt, är läskvorum för en viss konsekvensnivå består uteslutande av efterföljaren repliker. Vi undviker att kontakta ledaren för att tjäna läser om det inte behövs. Vi använder ett antal idéer från den forskning som gjorts om förhållandet mellan [last och kapacitet](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) i kvorum-baserade system för de fem [konsekvensmodeller](consistency-levels.md) som Cosmos DB stöder.  

## <a name="partition-sets"></a>Partitionsuppsättningar

En grupp fysiska partitioner, en från var och en av de konfigurerade med Cosmos-databasregionerna, består för att hantera samma uppsättning nycklar som replikeras över alla konfigurerade regioner. Denna primitiva högre samordning kallas *partitionsuppsättning* - ett geografiskt distribuerat dynamiskt överlägg av fysiska partitioner som hanterar en viss uppsättning nycklar. Medan en viss fysisk partition (en replikuppsättning) är begränsad i ett kluster, kan en partitionsuppsättning spänna över kluster, datacenter och geografiska områden som visas i bilden nedan:  

![Partitionsuppsättningar](./media/global-dist-under-the-hood/dynamic-overlay-of-resource-partitions.png)

Du kan tänka dig en partitionsuppsättning som en geografiskt spridd "superreplikuppsättning", som består av flera replikuppsättningar som äger samma uppsättning nycklar. I likhet med en replikuppsättning är en partitionsuppsättnings medlemskap också dynamiskt – det varierar baserat på implicita fysiska partitionshanteringsåtgärder för att lägga till/ta bort nya partitioner till/från en viss partitionsuppsättning (till exempel när du skalar ut dataflödet på en behållare, lägger till/tar bort en region till cosmos-databasen eller när fel inträffar). På grund av att var och en av partitionerna (av en partitionsuppsättning) hanterar partitionsuppsättningens medlemskap i sin egen replikuppsättning, är medlemskapet helt decentraliserat och mycket tillgängligt. Under omkonfigurationen av en partitionsuppsättning upprättas också topologin för överlägget mellan fysiska partitioner. Topologin väljs dynamiskt baserat på konsekvensnivå, geografiskt avstånd och tillgänglig nätverksbandbredd mellan källan och målfysiska partitioner.  

Med tjänsten kan du konfigurera Cosmos-databaserna med antingen en enda skrivregion eller flera skrivregioner, och beroende på valet är partitionsuppsättningar konfigurerade för att acceptera skrivningar i exakt en eller alla regioner. Systemet använder ett protokoll med två nivåer, kapslad konsensus – en nivå fungerar inom replikerna av en replikuppsättning av en fysisk partition som accepterar skrivningar, och den andra fungerar på nivån för en partitionsuppsättning för att ge fullständiga beställningsgarantier för alla de infästa skrivningarna i partitionsuppsättningen. Detta mång-lagerade, kapslade samförstånd är avgörande för genomförandet av våra stränga SLA för hög tillgänglighet, samt genomförandet av konsekvensmodeller, som Cosmos DB erbjuder sina kunder.  

## <a name="conflict-resolution"></a>Konfliktlösning

Vår design för uppdateringsspridning, konfliktlösning och orsakssambandsspårning är inspirerad av det tidigare arbetet med [epidemiska algoritmer](https://www.cs.utexas.edu/~lorenzo/corsi/cs395t/04S/notes/naor98load.pdf) och [Bayou-systemet.](https://zoo.cs.yale.edu/classes/cs422/2013/bib/terry95managing.pdf) Medan kärnorna i idéerna har överlevt och ger en bekväm referensram för att kommunicera Cosmos DB: s systemdesign, har de också genomgått betydande omvandling som vi tillämpat dem på Cosmos DB-systemet. Detta behövdes, eftersom de tidigare systemen varken utformades med resursstyrning eller med den omfattning som Cosmos DB behöver för att fungera, eller för att tillhandahålla kapacitet (t.ex. begränsad föråldringskonsekvens) och den stränga och omfattande SLA som Cosmos DB levererar till sina kunder.  

Kom ihåg att en partitionsuppsättning är fördelad över flera regioner och följer Cosmos DBs (multi-master) replikeringsprotokoll för att replikera data mellan de fysiska partitioner som består av en viss partitionsuppsättning. Varje fysisk partition (av en partitionsuppsättning) accepterar skrivningar och betjänar läsningar vanligtvis till de klienter som är lokala för den regionen. Skrivningar som accepteras av en fysisk partition inom en region är vederbörligen engagerade och görs mycket tillgängliga inom den fysiska partitionen innan de bekräftas till klienten. Dessa är preliminära skrivningar och sprids till andra fysiska partitioner i partitionsuppsättningen med hjälp av en anti-entropy-kanal. Klienter kan begära antingen preliminära eller bekräftade skrivningar genom att skicka ett begärandehuvud. Anti-entropy propagation (inklusive frekvensen av spridning) är dynamisk, baserat på topologin för partitionsuppsättningen, regional närhet av de fysiska partitionerna och konsekvensnivån konfigurerad. I en partitionsuppsättning följer Cosmos DB ett primärt commit-schema med en dynamiskt vald skiljedomarpartition. Skiljedomarevalet är dynamiskt och är en integrerad del av omkonfigureringen av partitionsuppsättningen baserat på överläggets topologi. De bekräftade skrivningar (inklusive flera rader/batchade uppdateringar) är garanterade att beställas. 

Vi använder kodade vektorklockor (som innehåller region-ID och logiska klockor som motsvarar varje nivå av konsensus vid replikuppsättningen respektive partitionsuppsättningen) för orsakssambandsspårning och versionsvektorer för att identifiera och lösa uppdateringskonflikter. Topologin och peer-urvalsalgoritmen är utformade för att säkerställa fast och minimal lagring och minimal nätverksomkostnader för versionsvektorer. Algoritmen garanterar egenskapen strikt konvergens.  

För Cosmos-databaserna som konfigurerats med flera skrivregioner erbjuder systemet ett antal flexibla automatiska konfliktlösningsprinciper för utvecklarna att välja mellan, inklusive: 

- **Senaste-skriv-Wins (LWW),** som som standard använder en systemdefinierad tidsstämpelegenskap (som baseras på tidssynkroniseringsklockan). Cosmos DB kan du också ange alla andra anpassade numeriska egenskaper som ska användas för konfliktlösning.  
- **Programdefinierad (anpassad) konfliktlösningsprincip** (uttryckt via kopplingsprocedurer), som är utformad för programdefinierad semantikavstämning av konflikter. Dessa procedurer anropas vid identifiering av skriv-skrivkonflikter under överinseende av en databastransaktion på serversidan. Systemet ger exakt en gång garanti för genomförandet av ett sammanfogningsförfarande som en del av åtagandeprotokollet. Det finns [flera konfliktlösningsexempel](how-to-manage-conflicts.md) tillgängliga för dig att spela med.  

## <a name="consistency-models"></a>Konsekvens modeller

Oavsett om du konfigurerar Cosmos-databasen med en enda eller flera skrivregioner kan du välja mellan de fem väldefinierade konsekvensmodellerna. Med flera skrivregioner är följande några anmärkningsvärda aspekter av konsekvensnivåerna:  

Den avgränsade föråldringskonsekvensen garanterar att alla läsningar kommer att vara inom *K-prefix* eller *T* sekunder från den senaste skrivningen i någon av regionerna. Dessutom, läser med begränsad föråldring konsekvens är garanterat monoton och med konsekvent prefix garantier. Anti-entropy protokollet fungerar på ett rate-limited sätt och säkerställer att prefixen inte ackumuleras och mottrycket på skrivningar inte behöver tillämpas. Session konsekvens garanterar monotona läsa, monotona skriva, läsa dina egna skriver, skriva följer läsa, och konsekvent prefix garantier, över hela världen. För databaser som konfigurerats med stark konsekvens gäller inte fördelarna (låg skrivfördröjning, hög skrivtillgänglighet) för flera skrivregioner på grund av synkron replikering mellan regioner.

Semantiken hos de fem konsekvensmodellerna i Cosmos DB beskrivs [här](consistency-levels.md), och beskrivs matematiskt med hjälp av en hög nivå TLA + specifikationer [här](https://github.com/Azure/azure-cosmos-tla).

## <a name="next-steps"></a>Nästa steg

Nästa lär dig hur du konfigurerar global distribution med hjälp av följande artiklar:

* [Lägga till/ta bort regioner från ditt databaskonto](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Konfigurera klienter för multi-homing](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Så här skapar du en anpassad konfliktlösningsprincip](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)

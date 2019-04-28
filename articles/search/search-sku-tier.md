---
title: Välj en prisnivå nivå eller SKU för Azure Search-tjänst – Azure Search
description: 'Azure Search kan etableras på dessa SKU: er: Kostnadsfri, Basic och Standard, där Standard finns i olika resurskonfigurationer och kapacitet nivåer.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: b50d0c0ca9a4000cc0c725453a3ef04b4bed9275
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61282518"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Välj en prisnivå för Azure Search

I Azure Search, en [resursen har skapats](search-create-service-portal.md) på en prisnivå nivå eller SKU som har åtgärdats för livslängden för tjänsten. Nivåerna ingår **kostnadsfri**, **grundläggande**, **Standard**, eller **Lagringsoptimerade**.  **Standard** och **Lagringsoptimerade** är tillgängliga i flera konfigurationer och kapaciteter. 

De flesta kunder börjar med den **kostnadsfri** nivå för utvärdering och sedan konverteras till en av de högsta betalda nivåerna för utvecklings-och produktionsdistributionerna. Du kan utföra alla snabbstarter och självstudier på den **kostnadsfri** nivå, inklusive de för resurskrävande kognitiv sökning.

> [!NOTE]
> Tjänstnivåer Lagringsoptimerade finns för närvarande finns som förhandsversion till rabatterade priser för testning och experimentering med målet att samla in feedback. Slutlig prissättning kommer att tillkännages senare när de här nivåerna är allmänt tillgängliga. Vi rekommenderar mot att använda de här nivåerna för program i produktion.

Nivåerna återspeglar egenskaperna för den maskinvara som är värd för den tjänsten (i stället funktioner) och hjälp av:

+ Antal index som du kan skapa
+ Storlek och hastighet partitioner (fysisk lagring)

Även om alla nivåer, inklusive den **kostnadsfri** nivå, ger ofta funktionsparitet, större arbetsbelastningar kan bestämmer krav för högre nivåer. Till exempel [AI-indexering med kognitiva tjänster](cognitive-search-concept-intro.md) har långvariga kunskaper tiden på en kostnadsfri tjänst om inte datauppsättningen råkar vara små.

> [!NOTE] 
> Undantaget till funktionsparitet är [indexerare](search-indexer-overview.md), som inte är tillgängliga på S3HD.
>

Inom en nivå kan du [justera replik-och partition](search-capacity-planning.md) att öka eller minska skala. Du kan börja med en eller två för var och tillfälligt öka din dataresurser för en tung indexeringsarbetsbelastningen. Du kan finjustera resursnivåer inom en nivå ökar flexibiliteten, men även lite komplicerar dina analyser. Du kan behöva experimentera om du vill se om en lägre nivå med högre resurser/repliker erbjuder bättre värde och prestanda än en högre nivå med lägre resurser. Mer information om när och varför skulle du justera kapacitet finns [prestanda och optimering överväganden](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Nivåer för Azure Search

I följande tabell visas de tillgängliga nivåerna. Andra informationskällor nivån omfattar den [prissättningssidan](https://azure.microsoft.com/pricing/details/search/), [och gränser](search-limits-quotas-capacity.md), och portalsidan vid etablering av en tjänst.

|Nivå | Kapacitet |
|-----|-------------|
|Kostnadsfri | Delas med andra prenumeranter. Icke-skalbart, begränsad till 3 index och 50 MB lagringsutrymme. |
|Basic | Dedikerade resurser för produktionsarbetsbelastningar i mindre skala. En partition med 2 GB och upp till tre repliker. |
|Standard 1 (S1) | Från S1 på dig dedikerade datorer med mer lagring och bearbetning av kapacitet på alla nivåer. Partitionsstorlek är 25 GB/partition (högst 300 GB per tjänst) för S1. |
|Standard 2 (S2) | Liknar S1 men med 100 GB/partitioner (max 1,2 TB per tjänst) |
|Standard 3 (S3) | 200 GB/partition (max 2,4 TB per tjänst) |
|Standard 3 Högdensitet (S3 HD) | Högdensitetssampling är en *som är värd för läge* för S3. Den underliggande maskinvaran är optimerad för ett stort antal mindre index, avsett för scenarion med flera klientorganisationer. S3 HD har samma per enhet avgiften som S3 men maskinvaran som är optimerat för snabb läsning på ett stort antal mindre index.|
|Lagringsoptimerade 1 (L1) | 1 TB/partition (max 12 TB per tjänst) |
|Lagringsoptimerade 2 (L2) | 2 TB/partition (högst 24 TB per tjänst) |

> [!NOTE] 
> Lagringsoptimerade-nivåerna erbjuder större lagringskapacitet till en lägre kostnad per TB än Standard-nivåerna. Primär Nackdelen är högre svarstid som bör du kontrollera efter dina specifika programkrav.  Mer information om prestandaöverväganden för den här nivån finns [prestanda och optimering överväganden](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Så fungerar debiteringen

Det finns tre sätt att betala i Aure Search i Azure Search och det är fast och varierande komponenter. Det här avsnittet tittar på varje fakturering komponent i tur och ordning.

### <a name="1-core-service-costs-fixed-and-variable"></a>1. Core-Tjänstekostnader (fast och varierande)

Minimiavgiften är den första sökenheten (1 repliken x 1 partition) för tjänsten, och den mängden har åtgärdats för livslängden för tjänsten eftersom tjänsten inte kan köras på något mindre än den här konfigurationen. 

Utöver minimum och du kan lägga till repliker och partitioner oberoende av varandra. Du kan till exempel lägga till endast repliker eller endast partitioner. Inkrementell ökning av kapacitet via repliker och partitioner utgör komponenten rörlig kostnad. 

Faktureringen bygger på en [formel (repliker x partitioner x rate)](#search-units). Det pris du debiteras beror på prisnivå som du väljer.

I följande skärmbild per priser anges för Free, Basic och S1 (S2, S3, L1 och L2 visas inte). Om du har skapat en **grundläggande**, **Standard**, eller **Lagringsoptimerade** tjänsten, din månatliga kostnad skulle genomsnittlig värdet som visas för *pris-1*och *pris-2* respektive. Enhet kostnader gå upp för varje nivå eftersom beräkningskapacitet kraft och lagring är högre på varje efterföljande nivå. Priserna för Azure Search är publicerade på den [Azure Search sidan med priser](https://azure.microsoft.com/pricing/details/search/).

![Per pris för basenheten](./media/search-sku-tier/per-unit-pricing.png "Per pris för basenheten")

När kostnadsberäkning ut en söklösning, märker du att priser och kapacitet inte är linjär (vilket fördubblar kapacitet som är mer än kostnaden Double-värden). Ett exempel på hur av formeln fungerar finns i [”så här allokerar du repliker och partitioner”](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).


### <a name="2-data-egress-charges-during-indexing"></a>2. Kostnaderna för utgående datatrafik vid indexering

Användning av [Azure Search-indexerare](search-indexer-overview.md) kan resultera i fakturering effekt beroende på var tjänsterna finns. Du kan eliminera kostnaderna för utgående datatrafik helt och hållet om du skapar Azure Search-tjänst i samma region som dina data. Följande punkter är från den [bandbredd sidan med priser](https://azure.microsoft.com/pricing/details/bandwidth/).

+ Microsoft debiterar inte för alla inkommande data till alla tjänster på Azure eller för utgående data från Azure Search.

+ Det finns inga avgifter för Mina data passerar kabeln när alla tjänster som är i samma region i lösningar för flera tjänster.

Gäller avgifterna för utgående data om tjänster finns i olika regioner. Sådana avgifter är inte en del av din Azure Search-faktura sig självt, men de nämns här om du använder data eller AI-berikats indexerare att hämta data från olika regioner, ser du de kostnader som visas i dina kostnader. 

### <a name="3-ai-enriched-indexing-using-cognitive-services"></a>3. AI-utökad indexering med kognitiva tjänster

För [AI-indexering med kognitiva tjänster](cognitive-search-concept-intro.md), bör du vill koppla en fakturerbar resurs för Cognitive Services S0 prisnivån för användningsbaserad betalning bearbetning. Det kostar ingenting ”fast” som är associerade med koppla Cognitive Services. Du betalar bara för den bearbetning som du behöver.

Extrahering av avbildningen under dokumentknäckning är en Azure Search-avgift, som debiteras baserat på antalet avbildningar som extraheras från dina dokument. Textextrahering är för närvarande kostnadsfritt. 

Andra enrichments, till exempel bearbetning av naturligt språk, baseras på [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) debiteras mot en resurs för Cognitive Services, enligt samma taxa som om du har utfört uppgiften med Cognitive Services direkt. Mer information finns i [bifoga en Cognitive Services-resurs med en kompetens](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

### <a name="billing-based-on-search-units"></a>Baserat på sökenheter

För Azure Search, de viktigaste fakturering konceptet att förstå är en *sökenheten* (SU). Eftersom Azure Search är beroende av både repliker och partitioner för indexering och frågor, vara inte det klokt att debiterar per bara någondera. I stället baseras fakturering på en kombination av båda. 

SU är produkten av *repliken* och *partitioner* används av en tjänst: **`(R X P = SU)`**

Varje tjänst börjar med en SU (en replik multiplicerat med en partition) som minst. Den maximala storleken för alla tjänster är 36 su: er, vilket kan ske på flera olika sätt: 6 partitioner x 6 repliker eller 3 partitioner x 12 repliker, att nämna några. Det är vanligt att använda mindre än total kapacitet. Till exempel en 3-replik 3-partition tjänst, som 9 su: er. Du kan granska [det här diagrammet](search-capacity-planning.md#chart) att se giltiga kombinationer på ett ögonblick.

Debiteringen är **per timme per SU**, där varje nivå har en progressivt högre kostnad. På högre nivå har större och snabbare partitioner, bidrar till ett högre övergripande timpris för den nivån. Gällande priser för varje nivå finns på [prisinformation om](https://azure.microsoft.com/pricing/details/search/). 

De flesta kunder använda bara en del av den totala kapaciteten online, hålla resten reserv. När det gäller fakturering är det antalet partitioner och -repliker som du infogar online, beräknade med hjälp av SU-formeln som anger vad du betalar per timme.

### <a name="billing-for-image-extraction-in-cognitive-search"></a>Faktureringen för extrahering av avbildningen i kognitiv sökning

Om du extraherar bilder från filer i en kognitiv sökning indexering pipeline, debiteras du för den åtgärden i din Azure Search-faktura. Parametern som utlöser extrahering av avbildningen är **imageAction** i en [indexerarkonfiguration](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters). Om **imageAction** har angetts till none (standard), det finns inga avgifter för extrahering av avbildningen.

Priser kan ändras, men dokumenteras alltid på den [prisinformation om](https://azure.microsoft.com/pricing/details/search/) för Azure Search. 

### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Faktureringen för inbyggda kunskaper i kognitiv sökning

När du ställer in en berikande pipeline alla [inbyggda kunskaper](cognitive-search-predefined-skills.md) används i pipelinen baseras på machine learning-modeller. Dessa modeller tillhandahålls av Cognitive Services. Användning av dessa modeller under indexering debiteras enligt samma taxa som om du hade begärt resursen direkt.

Anta exempelvis att en pipeline som består av optisk teckenläsning (OCR) mot skannad bild JPEG-filer, där den resulterande texten skickas till ett Azure Search-index för fritt format sökfrågor. Din pipeline för fulltextindexering omfattar en indexerare med den [OCR färdighet](cognitive-search-skill-ocr.md), och att färdighet skulle vara [kopplade till en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md). När du kör indexeraren visas avgifter på fakturan kognitiva resurser för OCR-körning.

## <a name="tips-for-reducing-costs"></a>Tips för att minska kostnaderna

Du kan inte stänga av tjänsten för att sänka kostnader. Dedikerade resurser är operativa 24-7, tilldelat för din exklusiv användning för livslängden för din tjänst. Det enda sättet att sänka en faktura är genom att minska repliker och partitioner till en låg nivå som ger acceptabel prestanda fortfarande och [SLA efterlevnad](https://azure.microsoft.com/support/legal/sla/search/v1_0/). 

En Spak för att minska kostnaderna är att välja en nivå med ett lägre pris per timme. S1 timpris är lägre än S2 eller S3-priser. Om vi antar att du tillhandahåller en tjänst som riktar sig till lägre slutet av din belastningen projektioner om företaget har växt ur tjänsten kan du skapa en andra tjänst för större nivåer, återskapar dina index på den andra tjänsten och ta sedan bort den första mallen för. 

Om du har gjort kapacitetsplanering på lokala servrar, vet du att det är vanligt att ”köpa dig” så att du kan hantera planerade tillväxt. Men med en molnbaserad tjänst att försöka göra kostnadsbesparingar mer aggressivt eftersom du inte låst att en särskild försäljningsorder. Du kan alltid växla till en högre nivåer tjänst om den inte är tillräckligt.

### <a name="capacity-drill-down"></a>Kapacitet nedåt

I Azure Search kapacitet är strukturerad som *repliker* och *partitioner*. 

+ Repliker är instanser av search-tjänst där varje replik är värd för en Utjämning av nätverksbelastning kopia av ett index. Till exempel har en tjänst med 6 repliker 6 exemplar av alla index läses in i tjänsten. 

+ Partitioner lagra index och automatiskt dela sökbara data: två partitioner dela ditt index i en halv finns det tre partitioner i tre delar och så vidare. Vad gäller kapacitet, *partitionera storlek* är den primära särskiljande funktionen på nivåerna.

> [!NOTE]
> Alla **Standard** och **Lagringsoptimerade** nivåerna support [flexibla kombinationer replik och partitioner](search-capacity-planning.md#chart) så att du kan [vikt systemet för hastighet eller Storage](search-performance-optimization.md) genom att ändra balans. **Grundläggande** erbjuder tre repliker för hög tillgänglighet men har endast en partition. **Kostnadsfria** nivåer ger inte dedikerade resurser: databehandling resurser som delas av flera prenumeranter.

### <a name="more-about-service-limits"></a>Mer om tjänstbegränsningar

Tjänsterna värdresurser, till exempel index, indexerare och så vidare. Varje nivå inför [tjänstbegränsningar](search-limits-quotas-capacity.md) på antalet resurser som du kan skapa. Därför är en begränsning på antalet index (och andra objekt) funktionen för andra särskiljande på nivåerna. När du granskar varje alternativ i portalen kan du Observera gränser för antal index. Andra resurser, till exempel indexerare och datakällor kompetens, är knuten till indexet gränser.

## <a name="consumption-patterns"></a>Mönster för förbrukning

De flesta kunder börjar med den **kostnadsfri** tjänsten, som de hålla på obestämd tid och väljer sedan en av de **Standard** eller **Lagringsoptimerade** nivåer för allvarliga utveckling eller produktionsarbetsbelastningar. 

![Azure search-nivåer](./media/search-sku-tier/tiers.png "Azure search prisnivåer")

På valfri ände **grundläggande** och **S3 HD** finns för viktiga men ovanliga förbrukning mönster. **Grundläggande** är för liten produktionsarbetsbelastningar: den erbjuder serviceavtal, dedikerade resurser, hög tillgänglighet, men mycket små lagring, Fyll sammanlagt 2 GB. Den här nivån är utformad för kunder som konsekvent under utnyttjade tillgänglig kapacitet. Vid den bortre änden **S3 HD** tillämpas för arbetsbelastningar för ISV: er, partner, [lösningar för flera innehavare](search-modeling-multitenant-saas-applications.md), eller någon konfiguration där ett stort antal små index. Det är ofta självklart när **grundläggande** eller **S3 HD** nivån är det rätta just, men om du vill vara säker kan du publicera till [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure Stöd för](https://azure.microsoft.com/support/options/) ytterligare vägledning.

Flytta fokus till de vanliga standard-nivåerna **S1 S3** är en följd av höja av kapacitet med förändras och uppnå högre på partitionsstorlek- och maximivärden om hur många index, indexerare och naturlig följd resurser:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partitionsstorlek|  25 GB | 100 GB | 200 GB |  |  |  |  |
| begränsningar för index och indexerare| 50 | 200 | 200 |  |  |  |  |

**S1** är ett vanligt val när dedikerade resurser och flera partitioner blir nödvändigt. Med partitioner av 25 GB för upp till 12 partitioner, begränsa per-tjänsten på **S1** är 300 GB total om du maximerar partitioner över repliker (se [allokera partitioner och -repliker](search-capacity-planning.md#chart) för mer belastningsutjämnade sammansättning.)

Portalen och prissättning sidor lägga fokus i partitionsstorlek och lagring, men för varje nivå, alla beräkningsresurser funktioner (diskkapacitet, hastighet, processorer) växer linjärt med pris. En **S2** repliken är snabbare än **S1**, och **S3** är snabbare än **S2**. **S3** nivåer bryta Allmänt linjär beräkningspriser mönstret med oproportionerligt snabbare i/o. Om du räknar i/o flaskhalsar, en **S3** ger dig mycket mer IOPS än lägre nivåer.

**S3** och **S3 HD** backas upp av identiska hög kapacitet infrastruktur, men var och en når sin maxgräns på olika sätt. **S3** riktar sig mot ett mindre antal mycket stora index. Därför maxlängden är bundet till en resurs (2,4 TB för varje tjänst). **S3 HD** riktar sig mot ett stort antal mycket små index. På 1 000 index **S3 HD** når gränsen i form av index begränsningar. Om du är en **S3 HD** kund som kräver mer än 1 000 index, kontakta Microsoft Support för information om hur du fortsätter.

> [!NOTE]
> Tidigare dokumentet gränser har ersättning men kan inte längre gäller för nya tjänster. Mer information om villkor som dokumentet begränsningar gäller fortfarande finns [tjänstbegränsningar: dokumentera gränser](search-limits-quotas-capacity.md#document-limits).
>

Optimerad lagringsnivåer, **L1 L2**, är perfekta för program med stora mängder datakrav, men ett relativt lågt antal användare där minimerar svarstid inte är högsta prioritet.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partitionsstorlek|  1 TB | 2 TB |  |  |  |  |  |
| begränsningar för index och indexerare| 10 | 10 |  |  |  |  |  |

*L2* har två gånger övergripande lagringskapacitet till en *L1*.  Välj nivå baserat på den maximala mängden data som du tror att du behöver i ditt index.  Den *L1* nivån partitionerar skala upp i steg om 1 TB till högst 12 TB, medan den *L2* ökar med 2 TB per partition upp till 24 TB.

## <a name="evaluate-capacity"></a>Utvärdera kapacitet

Kapacitet och kostnaderna för att driva tjänsten går hand i hand. Nivåerna har gränser på två nivåer (lagring och resurser), så du bör tänka på båda eftersom vilken som du kommer först är effektiva gränsen. 

Affärsbehov kräver vanligtvis antal index som du behöver. Till exempel en global index för en stor databas av dokument eller kanske flera index baserat på region, program eller nischmarknader för företag.

Om du vill fastställa storleken på ett index kan du behöva [bygga en](search-create-index-portal.md). Datastrukturen i Azure Search är främst en [inverterad index](https://en.wikipedia.org/wiki/Inverted_index), som har andra egenskaper än källdata. För ett vägar i inverterad index bestäms av innehåll, inte nödvändigtvis mängden datafeed till den storlek och komplexitet. En stor datakälla med massiv redundans kan resultera i ett index som är mindre än en mindre datauppsättning med mycket varierande innehåll. Det är därför sällan går att härleda Indexstorlek baserat på storleken på den ursprungliga datauppsättningen.

> [!NOTE] 
> Uppskatta framtida behov för index och lagring kan kännas som felsökning, är det värt att göra. Om en nivåkapacitet visar sig vara för lågt, måste du etablerar en ny tjänst på högre nivå och sedan [läsa in dina index](search-howto-reindex.md). Det finns ingen uppgradering på plats av samma tjänst från en SKU till en annan.
>

### <a name="step-1-develop-rough-estimates-using-the-free-tier"></a>Steg 1: Utveckla grov beräkningar med hjälp av den kostnadsfria nivån

En metod för att uppskatta kapacitet är att börja med den **kostnadsfri** nivå. Kom ihåg att den **kostnadsfri** tjänsten erbjuder upp till 3 index, 50 MB lagringsutrymme och 2 minuter att indexera tid. Det kan vara svårt för att uppskatta en planerade Indexstorlek med dessa begränsningar, men i följande exempel illustrerar en metod:

+ [Skapa en kostnadsfri tjänst](search-create-service-portal.md)
+ Förbereda en liten, representativ datauppsättning (antar fem tusen dokument och exempelstorlek för tio procent)
+ [Skapa ett första index](search-create-index-portal.md) och notera storleken på portalen (antar 30 MB)

Under förutsättning att exemplet är både representant och tio procent av hela datakällan, blir ett index med 30 MB ungefär 300 MB om alla dokument indexeras. Som enda verktyg numret preliminär, kan du dubbla det beloppet för att budgetera för två index (utveckling och produktion) kan en totalsumma på 600 MB i lagringskrav. Detta enkelt uppfyller de **grundläggande** tier, så att du vill börja det.

### <a name="step-2-develop-refined-estimates-using-a-billable-tier"></a>Steg 2: Utveckla förfinade beräkningar med hjälp av en faktureringsbar nivå

Vissa kunder föredrar att börja med dedikerade resurser som kan hantera större sampling och bearbetningstider och sedan utveckla realistisk uppskattningar av index antal, storlek och ställa frågor till volymer under utvecklingen. Inledningsvis är en tjänst etableras baserat på en gissning bästa uppskattning och som utvecklingsprojektet utvecklas team kännedom vanligtvis om den befintliga tjänsten är över eller under kapacitet för planerade produktionsarbetsbelastningar. 

1. [Granska tjänstbegränsningar på varje nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) att avgöra om antalet index som du kan ha stöd för lägre nivåer. I den **grundläggande**-**S1**-**S2** nivåer, index-gränserna är 15-50-200 respektive.  Den **Lagringsoptimerade** nivån har en gräns på 10 index eftersom det är designer för ett lågt antal mycket stora index.

1. [Skapa en tjänst på en faktureringsbara nivå](search-create-service-portal.md):

    + Starta ont om **grundläggande** eller **S1** om du är i början av din inlärningskurva.
    + Starta hög på **S2** eller till och med **S3**om storskaliga indexerings- och belastning är självklart.
    + Lagringsoptimerade, på **L1** eller **L2**, om du indexerar en stor mängd data och fråga belastningen är relativt låg, t.ex ett internt affärsprogram.

1. [Skapa ett första index](search-create-index-portal.md) att avgöra hur källdata översätts till ett index. Det här är det enda sättet att uppskatta Indexstorlek.

1. [Övervaka lagring, tjänstbegränsningar, fråga volym och svarstid](search-monitor-usage.md) i portalen. Portalen visar frågor per sekund, begränsade frågor och svarstid för sökning; alla som kan hjälpa dig välja om du har valt rätt nivå. Förutom portal mätvärden, du kan konfigurera djupövervakning, till exempel genomklickning analys, genom att aktivera [söktrafikanalys](search-traffic-analytics.md). 

Index antal och storlek är lika relevant för din analys eftersom maximala har nått via fullständig användning av lagring (partitioner) eller genom att högsta gräns för hur resurser (index, indexerare och så vidare), beroende på vilket som inträffar först. Portalen kan du hålla koll på båda, som visar aktuell användning och gränser sida vid sida på sidan Översikt.

> [!NOTE]
> Krav för lagring kan vara felaktigt luftfyllt om dokument innehåller överflödig data. Vi rekommenderar att dokument innehåller endast de data som du behöver för sökupplevelsen. Binära data är icke-sökbart och ska lagras separat (kanske i en Azure-tabell eller blob storage) med ett fält i indexet för en URL: en referens till externa data. Den maximala storleken för ett enskilt dokument är 16 MB (eller mindre om du är samtidigt som laddar upp flera dokument i en begäran). [Tjänstbegränsningar i Azure Search](search-limits-quotas-capacity.md) innehåller mer information.
>

**Query volume överväganden**

Frågor per sekund (QPS) är ett mått som får tydlighet under inställning av prestanda, men vanligtvis inte är en nivå faktor, såvida inte du förväntar dig hög fråga volymen från början.

Standard-nivåerna kan leverera en balans mellan repliker till partitioner, stöd för frågan ha via ytterligare repliker för att läsa in belastningsutjämning och ytterligare partitioner för parallell bearbetning. Du kan finjustera prestanda när tjänsten har etablerats.

Kunder som förväntar sig stark varaktig ställa frågor till volymer från början bör högre **Standard** nivåer, med mer kraftfulla maskinvara. Du kan sedan koppla partitioner och -repliker eller växla till en lägre nivå-tjänst, om det gick inte att Materialisera volymerna fråga. Läs mer om hur du beräknar frågedataflöde [Azure Search-prestanda och optimering](search-performance-optimization.md).

Lagringsoptimerade smidiga mot stora mängder data-arbetsbelastningar, stöd för mer övergripande index tillgänglig lagring, där frågan svarstidskrav är något Avslappnad nivåer.  Ytterligare repliker bör fortfarande användas för att läsa in belastningsutjämning och ytterligare partitioner för parallell bearbetning. Du kan finjustera prestanda när tjänsten har etablerats.

**Servicenivåavtal**

Den **kostnadsfri** nivå och preview funktioner levereras inte med [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). För alla fakturerbara nivåer gälla serviceavtalen när du etablerar tillräckligt med redundans för din tjänst. Två eller fler repliker krävs för frågan (läsa) serviceavtal (SLA). Tre eller fler repliker krävs för frågor och indexering SLA (skrivskyddad). Antalet partitioner är inte en SLA-beräkningen. 

## <a name="tips-for-tier-evaluation"></a>Tips för nivån utvärdering

+ Lär dig hur du skapar effektiva index och vilka metoder för uppdatering är den minsta impactful. Vi rekommenderar att [söktrafikanalys](search-traffic-analytics.md) för kunskap på frågeaktivitet.

+ Tillåt mätvärden och skapa runt frågor som du kan samla in data om användningsmönster (frågor under kontorstid, indexering vid låg belastning) och använda dessa data för att informera framtida service etablering beslut. När det inte praktiskt vid en per timme eller dag takt, kan du dynamiskt justera partitioner och resurser för att tillgodose planerade ändringar i frågan volymer, eller oplanerade men varaktigt ändringar om nivåer håller tillräckligt lång för att garanterar att vidta åtgärder.

+ Kom ihåg att endast nackdelen under etableringen är att du kan behöva plocka ner en tjänst om de faktiska kraven är större än du beräknad. För att undvika avbrott i tjänsten kan du skapa en ny tjänst i samma prenumeration på en högre nivå och köras sida vid sida förrän alla appar och begäranden rikta den nya slutpunkten.

## <a name="next-steps"></a>Nästa steg

Börja med en **kostnadsfri** nivå och skapa ett första index med en delmängd av dina data för att förstå dess egenskaper. Datastrukturen i Azure Search är ett vägar i inverterad index där storlek och komplexitet ett vägar i inverterad index bestäms av innehållet. Kom ihåg att med hög redundant innehåll tenderar att resultera i ett index som är mindre än mycket oregelbunden innehåll. Det är därför innehåll egenskaper i stället för storleken på den datauppsättning som anger index lagringskrav.

När du har en inledande uppfattning om Indexstorlek, [etablera en fakturerbar tjänst](search-create-service-portal.md) på en av nivåerna som beskrivs i den här artikeln, antingen **grundläggande**, **Standard**, eller **Lagringsoptimerade** nivå. Lätta alla artificiella begränsningar om storlek för data och [återskapa index](search-howto-reindex.md) att inkludera alla data som ska vara sökbart.

[Allokera partitioner och -repliker](search-capacity-planning.md) som behövs för att få de prestanda och skalbarhet som du behöver.

Om prestanda och kapacitet är bra, är du klar. Annars kan återskapa en söktjänst på en annan nivå som mer stämmer överens med dina behov.

> [!NOTE]
> För mer hjälp med dina frågor kan publicera till [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).

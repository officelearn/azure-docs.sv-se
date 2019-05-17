---
title: Välj en prisnivå nivå eller SKU för Azure Search-tjänst – Azure Search
description: 'Azure Search kan etableras i dessa SKU: er: Kostnadsfri, Basic och Standard och Standard finns i olika resurskonfigurationer och kapacitet nivåer.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 00422209302bbcc2139be4f6b490f0bb2816c051
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65539248"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Välj en prisnivå för Azure Search

När du skapar en Azure Search-tjänst, en [resursen har skapats](search-create-service-portal.md) på en prisnivå nivå eller SKU som har åtgärdats för livslängden för tjänsten. Nivåerna ingår kostnadsfri, Basic, Standard och Lagringsoptimerade. Standard och Lagringsoptimerade är tillgängliga med flera konfigurationer och kapaciteter.

De flesta kunder börjar med den kostnadsfria nivån så att de kan utvärdera tjänsten. De sedan uppgradera till en av de högsta nivåerna för utvecklings-och produktionsdistributionerna. Du kan utföra alla snabbstarter och självstudier med hjälp av den kostnadsfria nivån, inklusive som för resurskrävande kognitiv sökning.

> [!NOTE]
> För närvarande tillhandahåller Microsoft tjänstnivåer Lagringsoptimerade i en förhandsversion till rabatterade priser för testning och experimentering, med målet att samla in feedback. Slutlig prissättning kommer att tillkännages senare när de här nivåerna är allmänt tillgängliga. Vi rekommenderar mot att använda de här nivåerna för program i produktion.

Nivåerna återspeglar egenskaperna för den maskinvara som är värd för den tjänsten (i stället funktioner) och hjälp av:

+ Antal index som du kan skapa.
+ Storlek och hastigheten på partitioner (fysisk lagring).

Även om alla nivåerna, inklusive den kostnadsfria nivån ger ofta funktionsparitet, kan större arbetsbelastningar kräver ett behov av på högre nivå. Till exempel [AI indexering med kognitiva tjänster](cognitive-search-concept-intro.md) har långvariga kunskaper tiden på en kostnadsfri tjänst om inte datauppsättningen är liten.

> [!NOTE] 
> Undantaget till funktionsparitet är [indexerare](search-indexer-overview.md), som inte är tillgängliga på S3 HD.
>

Inom en nivå kan du [justera replik-och partition](search-capacity-planning.md) att öka eller minska skala. Du kan börja med en eller två för var och tillfälligt öka din dataresurser för en tung indexeringsarbetsbelastningen. Du kan finjustera resursnivåer inom en nivå ökar flexibiliteten, men även lite komplicerar dina analyser. Du kan behöva experimentera om du vill se om en lägre nivå med mer resurser/repliker erbjuder bättre värde och prestanda än en högre nivå med färre resurser. Mer information om när och varför skulle du justera kapacitet finns [prestanda och optimering överväganden](search-performance-optimization.md).

## <a name="tiers-for-azure-search"></a>Nivåer för Azure Search

I följande tabell visas de tillgängliga nivåerna. Du hittar mer information om de olika nivåerna på den [prissättningssidan](https://azure.microsoft.com/pricing/details/search/)i den [tjänstbegränsningar i Azure Search](search-limits-quotas-capacity.md) artikel och på portalen sidan när du etablerar en tjänst.

|Nivå | Kapacitet |
|-----|-------------|
|Kostnadsfritt | Delas med andra prenumeranter. Inte möjligheter till skalning. Begränsad till tre index och 50 MB lagringsutrymme. |
|Grundläggande | Dedikerade resurser för produktionsarbetsbelastningar i mindre skala. En partition med 2 GB och upp till tre repliker. |
|Standard 1 (S1) | För S1 och senare, dedikerade virtuella datorer med mer lagring och bearbetning av kapacitet på alla nivåer. För S1 är partitionsstorlek 25 GB/partition (med högst 300 GB per tjänst). |
|Standard 2 (S2) | Liknar S1, men med 100 GB-partitioner (och högst 1,2 TB per tjänst). |
|Standard 3 (S3) | 200 GB partitioner (med högst 2,4 TB per tjänst). |
|Standard 3 hög densitet (S3 HD) | Högdensitetssampling är en *som är värd för läge* för S3. Den underliggande maskinvaran är optimerad för ett stort antal mindre index och är avsett för scenarion med flera klientorganisationer. S3 HD har samma per enhet avgiften som S3, men maskinvaran som är optimerat för snabb läsning på ett stort antal mindre index.|
|Lagringsoptimerade 1 (L1) | 1 TB partitioner (med högst 12 TB per tjänst). |
|Lagringsoptimerade 2 (L2) | 2 TB partitioner (med högst 24 TB per tjänst). |

> [!NOTE] 
> Lagringsoptimerade-nivåerna erbjuder större lagringskapacitet till en lägre kostnad per TB än Standard-nivåerna. Primär Nackdelen är högre svarstid som bör du kontrollera efter dina specifika programkrav.  Mer information om överväganden för prestanda för den här nivån finns [prestanda och optimering överväganden](search-performance-optimization.md).
>

## <a name="how-billing-works"></a>Så fungerar debiteringen

Det finns tre sätt att betala i Azure Search det är fast och varierande komponenter. Det här avsnittet beskriver de tre fakturering komponenterna: core tjänstekostnaderna avgifter tillkommer och AI-utökad indexering.

### <a name="core-service-costs-fixed-and-variable"></a>Core-Tjänstekostnader (fast och varierande)

Minsta avgiften gäller för själva tjänsten, den första sökenheten (1 repliken x 1 partition). Med detta minsta har åtgärdats för livslängden för tjänsten eftersom tjänsten inte kan köras på något mindre än den här konfigurationen.

Utöver minimum och du kan lägga till repliker och partitioner oberoende av varandra. Du kan till exempel lägga till endast repliker eller endast partitioner. Inkrementell ökning av kapacitet via repliker och partitioner utgör komponenten rörlig kostnad.

Faktureringen bygger på en [formel (repliker x partitioner x rate)](#search-units). Det pris du debiteras beror på prisnivå som du väljer.

I följande skärmbild anges per enhet priser för Free, Basic och S1. (S2, S3, L1 och L2 inte visas.) Om du skapar en grundläggande tjänst, kommer din månatliga kostnad genomsnittlig värdet som visas för *pris-1*. För en Standard-tjänst, kommer din månatliga kostnad genomsnittlig värdet som visas för *pris-2*. Enhet kostnaderna ökar för varje nivå eftersom beräkningskapacitet kraft och lagring är högre på varje efterföljande nivå. Priser för Azure Search är tillgängligt på den [Azure Search sidan med priser](https://azure.microsoft.com/pricing/details/search/).

![Priser per enhet](./media/search-sku-tier/per-unit-pricing.png "priser Per enhet")

När du gör uppskattningar kostnaden för en söklösning, hålla i åtanke som priser och kapacitet är inte linjär. (En fördubbling kapacitet mer än fördubblar kostnaden.) Ett exempel på hur av formeln fungerar finns i [så här allokerar du repliker och partitioner](search-capacity-planning.md#how-to-allocate-replicas-and-partitions).

#### <a name="billing-based-on-search-units"></a>Baserat på sökenheter

Det viktigaste fakturering konceptet att förstå för Azure Search-åtgärder är den *sökenheten* (SU). Eftersom Azure Search är beroende av både repliker och partitioner för indexering och frågor, vara inte det klokt att debiterar per bara någondera. I stället baseras fakturering på en kombination av båda.

SU är produkten av den *repliker* och *partitioner* används av en tjänst: **(R x P = SU)**.

Varje tjänst börjar med en SU (en replik multiplicerat med en partition) som minst. Den maximala storleken för alla tjänster är 36 su: er. Den här högsta gränsen nås på flera olika sätt: 6 partitioner x 6 repliker eller 3 partitioner x 12 repliker, till exempel. Det är vanligt att använda mindre än total kapacitet (till exempel en 3-replik, 3-partition service som 9 su: er). Se den [kombinationer av Partition och repliken](search-capacity-planning.md#chart) diagram för giltiga kombinationer.

Debiteringen är per timme per SU. Varje nivå har en progressivt högre kostnad. På högre nivå som kommer med större och snabbare partitioner och detta bidrar till ett högre övergripande timpris för den nivån. Du kan visa för varje nivå på den [prisinformation](https://azure.microsoft.com/pricing/details/search/) sidan.

De flesta kunder använda bara en del av den totala kapaciteten online, hålla resten reserv. För fakturering, antalet partitioner och -repliker som du använder online, avgör beräknas genom att SU formeln vad du betalar per timme.

### <a name="data-egress-charges-during-indexing"></a>Kostnaderna för utgående datatrafik vid indexering

Med hjälp av [Azure Search-indexerare](search-indexer-overview.md) kan påverka faktureringen, beroende på platsen för dina tjänster. Du kan eliminera kostnaderna för utgående datatrafik helt och hållet om du skapar Azure Search-tjänst i samma region som dina data. Här är lite information från den [bandbredd sidan med priser](https://azure.microsoft.com/pricing/details/bandwidth/):

+ Microsoft tar inte betalt för alla inkommande data till alla tjänster på Azure eller för utgående data från Azure Search.

+ I multiservice lösningar är gratis för data passerar kabeln när alla tjänster som är i samma region.

Gäller avgifterna för utgående data om tjänster finns i olika regioner. Dessa kostnader är inte en del av din Azure Search-faktura. De är anges om du använder data eller AI-berikats indexerare att hämta data från olika regioner, ser du kostnaderna som visas i dina kostnader.

### <a name="ai-enriched-indexing-with-cognitive-services"></a>AI-utökad indexering med kognitiva tjänster

För [AI indexering med kognitiva tjänster](cognitive-search-concept-intro.md), du bör planera att bifoga en fakturerbara Azure Cognitive Services-resurs i samma region som Azure Search på S0 prisnivån för användningsbaserad betalning bearbetning. Det finns ingen fast kostnad som hör till koppla Cognitive Services. Du betalar bara för den bearbetning som du behöver.

Extrahering av avbildningen under dokumentknäckning är en Azure Search-avgift. Faktureras enligt antalet avbildningar som extraheras från dina dokument. Textextrahering är för närvarande kostnadsfritt.

Andra enrichments som bearbetning av naturligt språk, baseras på [inbyggda kognitiva kunskaper](cognitive-search-predefined-skills.md) och debiteras mot en resurs för Cognitive Services. De faktureras enligt samma taxa som om du har utfört uppgiften med hjälp av Cognitive Services direkt. Mer information finns i [bifoga en Cognitive Services-resurs med en kompetens](cognitive-search-attach-cognitive-services.md).

<a name="search-units"></a>

#### <a name="billing-for-image-extraction-in-cognitive-search"></a>Faktureringen för extrahering av avbildningen i kognitiv sökning

Om du extrahera bilder från filer i en kognitiv sökning indexering pipeline, debiteras du för den åtgärden i din Azure Search-faktura. I en [indexerarkonfiguration](https://docs.microsoft.com/rest/api/searchservice/create-indexer#indexer-parameters), **imageAction** är den parameter som utlöser extrahering av avbildningen. Om **imageAction** är inställd på ”none” (standard), du debiteras inte för extrahering av avbildningen.

Priserna kan komma att ändras. Det är dokumenterade på den [prisinformation](https://azure.microsoft.com/pricing/details/search/) för Azure Search.

#### <a name="billing-for-built-in-skills-in-cognitive-search"></a>Faktureringen för inbyggda kunskaper i kognitiv sökning

När du ställer in en berikande pipeline alla [inbyggda kunskaper](cognitive-search-predefined-skills.md) används i pipelinen baseras på machine learning-modeller. Dessa modeller tillhandahålls av Cognitive Services. Om du använder dessa modeller under indexering, debiteras du enligt samma taxa som du skulle vara om du har begärt resursen direkt.

Anta exempelvis att du har en pipeline som använder optisk teckenläsning (OCR) mot skannade JPEG-filer och den resulterande texten skickas till ett Azure Search-index för fritt format sökfrågor. Din pipeline för fulltextindexering omfattar en indexerare med den [OCR färdighet](cognitive-search-skill-ocr.md), och att färdighet skulle vara [kopplade till en resurs för Cognitive Services](cognitive-search-attach-cognitive-services.md). När du kör indexeraren visas avgifter för OCR körning på fakturan kognitiva resurser.

## <a name="tips-for-reducing-costs"></a>Tips för att minska kostnaderna

Du kan inte stänga av tjänsten för att minska din faktura. Dedikerade resurser är alltid fungerar, tilldelat för din exklusiv användning för livslängden för din tjänst. Det enda sättet att sänka dina kostnader är att minska repliker och partitioner till en nivå som ger acceptabel prestanda fortfarande och [SLA efterlevnad](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

Ett sätt att minska kostnaderna är att välja en nivå med ett lägre pris per timme. S1 timpris är lägre än S2 eller S3-priser. Förutsatt att du etablera din tjänst med lägre slut load-projektioner, om företaget har växt ur tjänsten, du kan skapa en andra tjänst för större nivåer, återskapar dina index på den andra tjänsten och ta bort den första som.

Om du har gjort kapacitetsplanering för lokala servrar, vet du är det vanligt att ”köpa dig” så att du kan hantera planerade tillväxt. Med en molnbaserad tjänst att försöka kostnadsbesparingar mer aggressivt eftersom du inte låst dig till en specifik inköp. Du kan alltid växla till en högre nivåer tjänst om den inte är tillräckliga.

### <a name="capacity"></a>Kapacitet

I Azure Search kapacitet är strukturerad som *repliker* och *partitioner*.

+ Repliker är instanser av söktjänsten. Varje replik är värd för en Utjämning av nätverksbelastning kopia av ett index. Exempelvis kan har en tjänst med sex repliker sex kopior av alla index läses in i tjänsten.

+ Partitioner lagra index och automatiskt dela sökbara data. Två partitioner dela ditt index i två, tre partitioner dela upp den i tre delar och så vidare. Vad gäller kapacitet, *partitionera storlek* är den primära särskiljande funktionen mellan nivåerna.

> [!NOTE]
> Stöd för alla nivåer för Standard- och Lagringsoptimerade [flexibla kombinationer av repliker och partitioner](search-capacity-planning.md#chart) så att du kan [optimera systemet för hastighet eller lagring](search-performance-optimization.md) genom att ändra balans. Basic-nivån ger upp till tre repliker för hög tillgänglighet men har endast en partition. Kostnadsfria nivåer inte ger specialresurser: databehandling resurser som delas av flera prenumeranter.

### <a name="more-about-service-limits"></a>Mer om tjänstbegränsningar

Tjänsterna värdresurser som index och indexerare. Varje nivå inför [tjänstbegränsningar](search-limits-quotas-capacity.md) på antalet resurser som du kan skapa. Så är det maximala antalet index (och andra objekt) den andra särskiljande funktionen mellan nivåerna. När du granskar varje alternativ i portalen kan du Observera gränser för antal index. Andra resurser, t.ex. indexerare och datakällor kompetens, fästs på index gränser.

## <a name="consumption-patterns"></a>Mönster för förbrukning

De flesta kunder börjar med den kostnadsfria tjänsten som de att hålla på obestämd tid, och väljer sedan en av nivåerna Standard- eller Storage optimerat för allvarliga arbetsbelastningar för utveckling eller produktion.

![Azure Search prisnivåer](./media/search-sku-tier/tiers.png "Azure Search prisnivåer")

På det låga och höga upphört att gälla är Basic och S3 HD för viktiga men ovanliga förbrukning mönster. Basic är för liten produktionsarbetsbelastningar. Den erbjuder serviceavtal, dedikerade resurser och hög tillgänglighet, men ger mycket små lagring, Fyll sammanlagt 2 GB. Den här nivån har utformat för kunder som konsekvent underutilize tillgänglig kapacitet. Slutet hög är S3 HD för arbetsbelastningar som är typiskt för ISV: er, partner, [multitenant lösningar](search-modeling-multitenant-saas-applications.md), eller någon konfiguration som anropar för ett stort antal små index. Det är ofta tydligt när grundläggande eller S3 HD är rätt nivå. Om du vill bekräftelse, du kan publicera till [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/) anvisningar.

De vanligaste standardnivåerna S1 via S3, utgör en följd av att höja kapacitet. Det finns förändras och uppnå högre på partitionsstorlek och gränser för antal index, indexerare och naturlig följd resurser:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partitionsstorlek|  25 GB | 100 GB | 200 GB |  |  |  |  |
| begränsningar för index och indexerare| 50 | 200 | 200 |  |  |  |  |

S1 är ett vanligt val för kunder som behöver dedikerade resurser och flera partitioner. S1 erbjuder partitioner 25 GB och upp till 12 partitioner, vilket ger en högst 300 GB per tjänst om du maximerar partitioner över repliker. (Se [allokera partitioner och -repliker](search-capacity-planning.md#chart) för mer belastningsutjämnad allokeringar.)

Portalen och prissättning sidor aktivera partitionsstorlek och lagring, men för varje nivå, alla beräkningsresurser funktioner (diskkapacitet, hastighet, processorer) Allmänt ökar linjärt med pris. En S2-replik är snabbare än S1 och S3 är snabbare än S2. Nivåerna S3 bryter från linjär beräkningspriser mönstret med oproportionerligt snabbare i/o. Om du förväntar dig i/o är flaskhalsen, Kom ihåg att du kan få mycket mer IOPS med S3 än vad du kan få med lägre nivåer.

S3 och S3 HD backas upp av identiska hög kapacitet infrastruktur, men de når sin maximala gränser på olika sätt. S3 riktar sig mot ett mindre antal mycket stora index, så maxlängden är bundet till en resurs (2,4 TB för varje tjänst). S3 HD riktar sig mot ett stort antal mycket små index. S3 HD når gränsen i form av index begränsningar på 1 000 index. Kontakta Microsoft Support om du är S3 HD-kund och du behöver fler än 1 000 index, information om hur du fortsätter.

> [!NOTE]
> Dokumentet gränser har ersättning i taget, men de är inte längre gäller för nya tjänster. Information om villkor där dokumentet begränsningar gäller fortfarande finns i [dokumentera gränser](search-limits-quotas-capacity.md#document-limits).
>

Optimerad lagringsnivåer, L1 och L2, är perfekta för program med stora mängder datakrav men ett relativt lågt antal slutanvändare när minimerar svarstid inte är högsta prioritet.  

|  | L1 | L2 |  |  |  |  |  |
|--|----|----|--|--|--|--|--|
| Partitionsstorlek|  1 TB | 2 TB |  |  |  |  |  |
| begränsningar för index och indexerare| 10 | 10 |  |  |  |  |  |

L2 har två gånger övergripande lagringskapacitet för L1.  Välj nivå baserat på den maximala mängden data som du tror att ditt index måste. L1 nivån partitioner skala upp i steg om 1 TB till högst 12 TB. L2-partitioner ökar med 2 TB per partition upp till 24 TB.

## <a name="evaluating-capacity"></a>Utvärdering av kapacitet

Kapaciteten och kostnaderna för att driva tjänsten är direkt relaterade. Nivåerna har gränser på två nivåer: lagring och resurser. Du bör tänka på båda eftersom beroende på vilken gräns som du kommer först är effektiva gränsen.

Affärsbehov kräver vanligtvis antal index som du behöver. Du kanske till exempel behöver ett globalt index för en stor databas av dokument. Eller så kanske du behöver flera index baserat på region, program eller nischmarknader för företag.

Om du vill fastställa storleken på ett index kan du behöva [bygga en](search-create-index-portal.md). Datastrukturen i Azure Search är främst en [inverterad index](https://en.wikipedia.org/wiki/Inverted_index) struktur, som har andra egenskaper än källdata. För ett vägar i inverterad index bestäms storlek och komplexitet av innehåll, inte nödvändigtvis för mängden data som du går in i den. En stor datakälla med hög redundans kan resultera i ett index som är mindre än en mindre datauppsättning med mycket varierande innehåll. Det är så sällan går att härleda Indexstorlek baserat på storleken på den ursprungliga datauppsättningen.

> [!NOTE] 
> Trots att uppskatta framtida behov för index och lagring kan kännas som felsökning, är det värt att göra. Om en nivåkapacitet visar sig vara för lågt, måste du etablera en ny tjänst på en högre nivå och sedan [läsa in dina index](search-howto-reindex.md). Det finns ingen uppgradering på plats av en tjänst från en SKU till en annan.
>

### <a name="step-1-develop-rough-estimates-by-using-the-free-tier"></a>Steg 1: Utveckla grov beräkningar med hjälp av den kostnadsfria nivån

En metod för att uppskatta kapacitet är att börja med den kostnadsfria nivån. Kom ihåg att den kostnadsfria tjänsten erbjuder upp till tre index, 50 MB lagringsutrymme och 2 minuter att indexera tid. Det kan vara svårt för att uppskatta en planerade Indexstorlek med dessa begränsningar. Här är en metod som du kan vidta:

+ [Skapa en kostnadsfri tjänst](search-create-service-portal.md).
+ Förbereda en liten, representativ datauppsättning (till exempel 5 000 dokument och 10 procent exempelstorlek).
+ [Skapa ett första index](search-create-index-portal.md) och notera storleken på portalen (till exempel 30 MB).

Om exemplet är representant och 10 procent av hela datakällan, blir ett 30 MB index ungefär 300 MB om alla dokument indexeras. Som enda verktyg numret preliminär, kan du dubbla det beloppet för att budgetera för två index (utveckling och produktion). Detta ger dig en summa av 600 MB i lagringskrav. Detta krav uppfylls enkelt med Basic-nivån så att du vill börja det.

### <a name="step-2-develop-refined-estimates-by-using-a-billable-tier"></a>Steg 2: Utveckla förfinade beräkningar med hjälp av en faktureringsbar nivå

Vissa kunder föredrar att börja med dedikerade resurser som kan hantera större sampling och bearbetningstider och sedan utveckla realistisk uppskattningar av index antal, storlek och ställa frågor till volymer under utvecklingen. Inledningsvis är etableras en tjänst baserat på en beräkning av bästa gissning. Som utvecklingsprojektet utvecklas team vanligtvis kännedom om den befintliga tjänsten är över eller under kapacitet för planerade produktionsarbetsbelastningar.

1. [Granska tjänstbegränsningar på varje nivå](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity#index-limits) att avgöra om antalet index som du kan ha stöd för lägre nivåer. På nivåerna Basic, S1 och S2 är index-gränserna 15, 50 och 200, respektive. Lagringsoptimerade-nivån har en gräns på 10 index eftersom den har utformats för att stödja ett lågt antal mycket stora index.

1. [Skapa en tjänst på en faktureringsbara nivå](search-create-service-portal.md):

    + Starta låg, på grundläggande eller S1, om du är i början av din inlärningskurva.
    + Starta hög, S2 eller S3 även, om du vet att du kommer att ha storskaliga indexerings- och belastning.
    + Börja med Storage optimerat på L1 eller L2, om du indexerar en stor mängd data och fråga belastningen är relativt låg, som med ett internt affärsprogram.

1. [Skapa ett första index](search-create-index-portal.md) att avgöra hur källdata översätts till ett index. Det här är det enda sättet att uppskatta Indexstorlek.

1. [Övervaka lagring, tjänstbegränsningar, fråga volym och svarstid](search-monitor-usage.md) i portalen. Portalen visar frågor per sekund, begränsade frågor och svarstid för sökning. Alla dessa värden kan hjälpa dig välja om du har valt rätt nivå. Du kan också konfigurera djupgående övervakning av värden som genomklickning analys genom att aktivera [söktrafikanalys](search-traffic-analytics.md).

Index antal och storlek är lika viktiga för din analys. Det beror på att maximal har nått via fullständig användning av lagring (partitioner) eller genom att högsta gräns för hur resurser (index, indexerare och så vidare), beroende på vilket som inträffar först. Portalen kan du hålla koll på båda, som visar aktuell användning och gränser sida vid sida på sidan Översikt.

> [!NOTE]
> Krav för lagring kan vara pumpade om dokument innehåller överflödig data. Vi rekommenderar att dokument innehåller endast de data som ska användas för sökupplevelsen. Binära data är inte sökbart och ska lagras separat (kanske i en Azure-tabell eller blob storage). Bör sedan att lägga till ett fält i indexet för en URL: en referens till externa data. Den maximala storleken för ett enskilt dokument är 16 MB (eller mindre om du är samtidigt som laddar upp flera dokument i en begäran). Mer information finns i [tjänstbegränsningar i Azure Search](search-limits-quotas-capacity.md).
>

**Query volume överväganden**

Frågor per sekund (QPS) är ett viktigt mått under Prestandajustering, men det är oftast bara ersättning nivå om du förväntar dig hög fråga volym i början.

Standard-nivåerna kan ge en balans mellan repliker och partitioner. Du kan öka fråga arbetet genom att lägga till repliker för Utjämning av nätverksbelastning eller Lägg till partitioner för parallell bearbetning. Du kan sedan Finjustera prestanda när tjänsten har etablerats.

Om du förväntar dig hög varaktigt ställa frågor till volymer från början, bör du överväga högre standardnivån, med mer kraftfulla maskinvara. Du kan sedan koppla partitioner och -repliker eller växla till en lägre nivå-tjänst om volymerna fråga inte inträffar. Läs mer om hur du beräknar frågedataflöde [Azure Search-prestanda och optimering](search-performance-optimization.md).

Lagringsoptimerade-nivåerna är användbara för arbetsbelastningar för stora mängder data, stöd för mer lagring för den totala tillgängliga index för när frågan svarstidskrav är mindre viktiga. Du bör använda ytterligare repliker för load belastningsutjämning och ytterligare partitioner för parallell bearbetning. Du kan sedan Finjustera prestanda när tjänsten har etablerats.

**Serviceavtal**

Kostnadsfri nivå och preview funktionerna inte ge [servicenivå SLA-avtal](https://azure.microsoft.com/support/legal/sla/search/v1_0/). För alla fakturerbara nivåer gälla serviceavtalen när du etablerar tillräckligt med redundans för din tjänst. Du måste ha två eller fler repliker för fråga (läsa) serviceavtal. Du måste ha tre eller fler repliker för frågor och indexering (skrivskyddad) serviceavtal. Antalet partitioner påverkar inte serviceavtal.

## <a name="tips-for-tier-evaluation"></a>Tips för nivån utvärdering

+ Lär dig hur du skapar effektiva index och lär dig vilka metoder för uppdatering har minst påverkan. Använd [söktrafikanalys](search-traffic-analytics.md) insikter på frågeaktivitet.

+ Tillåt mått för att skapa runt frågor och samla in data om användningsmönster (frågor under kontorstid, indexering vid låg belastning). Du kan använda informationen för att informera service etablering beslut. Även om det inte är praktiskt vid en per timme eller dag takt, kan du dynamiskt justera partitioner och resurser för att tillgodose planerade ändringar i frågan volymer. Du kan också anpassa oplanerad men varaktigt ändringar om nivåer håller tillräckligt lång för att garanterar att vidta åtgärder.

+ Kom ihåg att endast Nackdelen med underprovisioning är att du kan behöva plocka ner en tjänst om de faktiska kraven är större än din förutsägelser. För att undvika avbrott i tjänsten kan du skapa en ny tjänst i samma prenumeration på en högre nivå och köras sida vid sida förrän alla appar och begäranden rikta den nya slutpunkten.

## <a name="next-steps"></a>Nästa steg

Börja med en kostnadsfri nivå och skapa ett första index med hjälp av en delmängd av dina data för att förstå dess egenskaper. Datastrukturen i Azure Search är en vägar i inverterad index-struktur. Storlek och komplexitet ett vägar i inverterad index bestäms av innehåll. Kom ihåg att med hög redundant innehåll tenderar att resultera i ett index som är mindre än mycket oregelbunden innehåll. Så att innehåll egenskaper i stället för storleken på datauppsättningen fastställa krav för indexet.

När du har en första uppskattning av dina Indexstorlek [etablera en fakturerbar tjänst](search-create-service-portal.md) på en av nivåerna som beskrivs i den här artikeln: Basic, Standard- eller optimerad lagring. Lätta alla artificiella begränsningar om storlek för data och [återskapa index](search-howto-reindex.md) så att de data som du vill ska vara sökbart.

[Allokera partitioner och -repliker](search-capacity-planning.md) som behövs för att få de prestanda och skalbarhet som du behöver.

Om prestanda och kapacitet är bra, är du klar. Annars kan återskapa en söktjänst på en annan nivå som mer stämmer överens med dina behov.

> [!NOTE]
> Om du har frågor kan publicera till [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).

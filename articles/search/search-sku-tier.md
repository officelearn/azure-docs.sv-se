---
title: Välj en prisnivå nivå eller SKU för Azure Search-tjänst | Microsoft Docs
description: 'Azure Search kan etableras på dessa SKU: er: ledigt, Basic eller Standard, där Standard finns i olika resurskonfigurationer och kapacitet för nivåerna.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: heidist
ms.openlocfilehash: 241d24746d82a359b4bbf4febbbaaf91180dd23e
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210932"
---
# <a name="choose-a-pricing-tier-for-azure-search"></a>Välj en prisnivå för Azure Search

I Azure Search en [service etableras](search-create-service-portal.md) på en specifik prisnivån eller SKU: N. Alternativen är **lediga**, **grundläggande**, eller **Standard**, där **Standard** är tillgänglig i flera konfigurationer och kapacitet. 

Syftet med den här artikeln är att hjälpa dig att välja en nivå. Det förbättrar den [sida med priser](https://azure.microsoft.com/pricing/details/search/) och [Tjänstbegränsningarna](search-limits-quotas-capacity.md) sida med en sammanfattning av fakturering begrepp och användningsmönster som är associerade med olika nivåer. Det rekommenderas också en iterativ metod för att förstå vilken nivå som bäst uppfyller dina behov. 

Nivåer avgöra kapaciteten, inte funktioner. Om en nivåkapaciteten visar sig vara för lågt, behöver du etablera en ny tjänst på högre nivå och sedan [Läs in ditt index](search-howto-reindex.md). Det finns ingen uppgradering på plats av samma tjänst från en-SKU till en annan.

Tillgängliga funktioner är inte en primär nivå faktor. Alla nivåer, inklusive den **lediga** tjänstnivån, erbjuda funktionsparitet med undantag för indexeraren stöd för S3HD. Dock kan begränsningar för indexering och resurs effektivt begränsa omfattningen av användning av funktioner. Till exempel [kognitiva Sök](cognitive-search-concept-intro.md) indexering har långvariga kunskaper tiden för en kostnadsfri tjänst om datauppsättningen råkar vara mycket liten.

> [!TIP]
> De flesta kunder börjar med den **lediga** datornivån för utvärdering och sedan reglera till **Standard** för utveckling. När du har valt en nivå och [etablera en söktjänst](search-create-service-portal.md), kan du [öka replik och partitionen antal](search-capacity-planning.md) för prestandajustering. Mer information om när och varför du vill justera kapacitet finns [prestanda och optimering överväganden](search-performance-optimization.md).
>

## <a name="billing-concepts"></a>Fakturerings-begrepp

Begrepp som du behöver för att förstå för val av inkluderar kapacitet definitioner, tjänstbegränsningarna och tjänsteenheter. 

### <a name="capacity"></a>Kapacitet

Kapacitet är strukturerad som *repliker* och *partitioner*. Repliker är instanser av tjänsten search där varje replik är värd för en belastningsutjämnad kopia av ett index. En tjänst med 6 repliker har till exempel 6 kopior av varje index som lästs in i tjänsten. Partitioner lagra index och delas automatiskt upp sökbara data: två partitioner dela ditt index i halva, tre partitioner i tre delar och så vidare. Vad gäller kapacitet, *partitions storlek* är den primära olika funktionen mellan nivåerna.

> [!NOTE]
> Alla **Standard** nivåer stöd [flexibla kombinationer replik och partitioner](search-capacity-planning.md#chart) så att du kan [vikt systemet för hastighet eller lagring](search-performance-optimization.md) genom att ändra saldot. **Grundläggande** erbjuder tre repliker för hög tillgänglighet men har endast en partition. **Ledigt** nivåer ger inte dedicerade resurser: computing resurser delas av flera gratistjänster.

### <a name="limits"></a>Begränsningar

Services värdresurser, till exempel index, indexerare och så vidare. Varje nivå inför [gränser](search-limits-quotas-capacity.md) på antalet resurser som du kan skapa. Därför är ett tak på antalet index (och andra objekt) andra olika funktionen mellan nivåerna. Observera begränsningar i antal index när du granskar varje alternativ i portalen. Andra resurser, till exempel indexerare datakällor och kunskaper, knuten till indexet gränser.

### <a name="search-units"></a>Sökenheter

Att förstå de viktigaste fakturering konceptet är en *sökenheten* (SU), vilket är fakturering enhet för Azure Search. Eftersom Azure Search är beroende av både repliker och partitioner som ska fungera, vara inte det klokt att fakturera genom en eller den andra. I stället baseras fakturering på en kombination av båda. Formulaically, en SU är produkten för replik- och partitioner som används av en tjänst: (R X P = SU). Minst var tjänsten startar med 1 SU (en replik multiplicerat med en partition), men en mer realistisk modell kan vara en replik 3, 3-partition tjänst debiteras som 9 SUs. 

Även om varje nivå ger progressivt högre kapacitet, kan du ta en del av den totala kapaciteten online, hålla resten reserv. Vad gäller fakturering är det antalet partitioner och repliker som du infogar online, beräknade med hjälp av SU-formel, som avgör vad du betalar.

Fakturering frekvensen är varje timme per SU, med varje nivå har en annan hastighet. Bedömer för varje nivå kan hittas på [prisinformation](https://azure.microsoft.com/pricing/details/search/).

## <a name="consumption-patterns"></a>Förbrukning mönster

De flesta kunder börjar med den **lediga** tjänsten, som de har på obestämd tid och väljer sedan en av de **Standard** nivåer för allvarliga arbetsbelastningar för utveckling och produktion. 

![Azure search nivåer](./media/search-sku-tier/tiers.png "Azure search prisnivåer")

På endera änden **grundläggande** och **S3 HD** finns för viktiga men ovanliga förbrukning mönster. **Grundläggande** är för liten produktionsarbetsbelastningar: den erbjuder SLA särskilda resurser, hög tillgänglighet, men liten lagring, Fyll sammanlagt 2 GB. Det här skiktet är utformad för kunder som konsekvent under utnyttjade tillgänglig kapacitet. Slutet långt **S3 HD** är för arbetsbelastningar typiska för ISV: er, partners, [multitenant lösningar](search-modeling-multitenant-saas-applications.md), eller någon konfiguration för ett stort antal små index. Det är ofta självklart när **grundläggande** eller **S3 HD** nivå är rätt anpassning, men om du vill bekräfta du kan skicka till [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure Stöd för](https://azure.microsoft.com/support/options/) för ytterligare.

Flytta fokus till vanliga standard nivåer, **S1 S3** är en utvecklingen av ökade kapacitet med angripits pekar på partitionsstorlek och maximala storlekar på antal index, indexerare och naturlig följd resurser:

|  | S1 | S2 | S3 |  |  |  |  |
|--|----|----|----|--|--|--|--|
| Partitionsstorlek|  25 GB | 100 GB | 250 GB |  |  |  |  |
| index och indexeraren gränser| 50 | 200 | 200 |  |  |  |  |

**S1** är ett vanligt val när det blir nödvändigt dedicerade resurser och flera partitioner. Med 25 GB partitioner för upp till 12 partitioner, begränsa per-tjänsten på **S1** är 300 GB total om du maximerar partitioner över repliker (se [allokera partitioner och repliker](search-capacity-planning.md#chart) för fler belastningsutjämnade sammansättningar.)

Portalen och prisnivå sidor aktivera partitionsstorlek och lagring, men för varje nivå alla compute-funktioner (diskkapacitet, hastighet, processorer) växer linjärt med pris. En **S2** repliken är snabbare än **S1**, och **S3** är snabbare än **S2**. **S3** nivåer Bryt vanligtvis linjär beräknings-priser mönster med oproportionerligt snabbare i/o. Om du tror att i/o som flaskhals, en **S3** ger dig mycket mer IOPS än lägre nivåer.

**S3** och **S3 HD** backas upp av identiska hög kapacitet infrastruktur, men var och en når sin övre gräns på olika sätt. **S3** riktar sig till ett mindre antal mycket stora index. Därför dess maxgränsen är bundet till en resurs (2,4 TB för varje tjänst). **S3 HD** riktar sig till ett stort antal mycket små index. På 1 000 index **S3 HD** når gränsen i form av indexet begränsningar. Om du är en **S3 HD** kund som kräver mer än 1 000 index, kontakta Microsofts Support för information om hur du fortsätter.

> [!NOTE]
> Tidigare var en faktor dokumentet gränser men inte längre gäller för de flesta Azure Search-tjänster som etablerats efter januari 2018. Mer information om villkor som dokumentet gränser gäller fortfarande finns [gränser: dokumentera gränser](search-limits-quotas-capacity.md#document-limits).
>

## <a name="evaluate-capacity"></a>Utvärdera kapacitet

Gå hand i hand kapacitet och kostnader för att köra tjänsten. Nivåer införa begränsningar på två nivåer, lagring och resurser, så bör du tänka både eftersom vilken som du kommer först är effektiva gränsen. 

Affärsbehov kräver vanligtvis antalet index som du behöver. Till exempel ett globalt index för en stor lagringsplats för dokument, eller kanske flera index baserat på region, program eller business nischer.

För att fastställa storleken på ett index måste du [bygga upp en](search-create-index-portal.md). Datastrukturen i Azure Search är i första hand en [inverterad index](https://en.wikipedia.org/wiki/Inverted_index), som har andra egenskaper än källdata. För ett inverterad index bestäms storleken och komplexiteten av innehåll, inte nödvändigtvis mängden datafeed till den. En omfattande datakälla med massiv redundans kan resultera i ett index som är mindre än en mindre datamängd med hög variabel innehåll.  Det är därför sällan går att härleda Indexstorlek baserat på storleken på den ursprungliga datauppsättningen.

### <a name="preliminary-estimates-using-the-free-tier"></a>Preliminär beräknar med hjälp av den kostnadsfria nivån

En metod för att uppskatta kapacitet är att börja med den **lediga** nivå. Kom ihåg att den **lediga** tjänst erbjuder upp till 3 index, 50 MB lagringsutrymme och 2 minuter för indexering tid. Det kan vara en utmaning för att beräkna en planerade Indexstorlek med dessa villkor, men i följande exempel används en metod:

+ [Skapa en kostnadsfri tjänst](search-create-service-portal.md)
+ Förbereda en liten, representativ datauppsättning (antar fem tusen dokument och tio procent provtagning)
+ [Skapa ett inledande index](search-create-index-portal.md) och notera storleken på portalen (förutsätter 30 MB)

Under förutsättning att provet har både i representant och tio procent av hela datakällan, blir ett 30 MB index ungefär 300 MB om alla dokument som indexeras. Tillsammans med den här preliminära nummer, kanske dubbla beloppet till budgeten för två index (utveckling och produktion) totalt 600 MB i lagringsbehov. Detta enkelt uppfyller den **grundläggande** nivån, utan det skulle startas.

### <a name="advanced-estimates-using-a-billable-tier"></a>Avancerade beräknar med hjälp av en fakturerbar nivå

Vissa kunder föredrar att börja med dedicerade resurser som kan hantera större provtagning och bearbetningstider och sedan utveckla realistiska uppskattningar av indexet antal, storlek och fråga volymer under utvecklingen. Från början, en tjänst har etablerats utifrån en bästa gissning uppskattning och sedan då utvecklingsprojektet utvecklas team vet vanligtvis om befintliga ligger över eller under kapacitet för planerade produktionsarbetsbelastningar. 

1. [Granska tjänstbegränsningarna på varje nivå](https://docs.microsoft.com/en-us/azure/search/search-limits-quotas-capacity#index-limits) att avgöra om lägre nivåer har stöd för antalet index som du behöver. I den **grundläggande**-**S1**- **S2** nivåer, index-gränserna är 15 50 200, respektive.

1. [Skapa en tjänst på en fakturerbar nivå](search-create-service-portal.md):

    + Starta ont om **grundläggande** eller **S1** om du är i början av din inlärningskurva.
    + Starta hög på **S2** eller även **S3**om stora belastningarna för indexering och fråga är självklart.

1. [Skapa ett inledande index](search-create-index-portal.md) att avgöra hur källdata översätter till ett index. Detta är det enda sättet att beräkna Indexstorlek.

1. [Övervaka lagring, tjänstbegränsningarna, fråga volym och svarstid](search-monitor-usage.md) i portalen. Portalen visar frågor per sekund, begränsad frågor och Sök latens; som kan hjälpa dig att avgöra om du är på rätt nivå. Utöver portal statistik och, du kan konfigurera djupövervakning, till exempel klickningsförflyttningsrapport analys genom att aktivera [söka trafik analytics](search-traffic-analytics.md). 

Index antal och storlek är lika relevant för din analys eftersom maximala gränsen nås via fullständig användning av lagring (partitioner) eller övre gräns för resurser (index, indexerare och så vidare), beroende på vilket som inträffar först. Portalen hjälper dig att hålla reda på båda visar aktuell användning och gränsvärden sida vid sida på översiktssidan.

> [!NOTE]
> Lagringskraven kan vara felaktigt luftfyllda om dokument innehåller främmande data. Vi rekommenderar innehåller dokument bara de data som behövs för sökupplevelsen. Binära data är icke-sökbart och ska lagras separat (kanske i ett Azure table eller blob storage) med ett fält i indexet för en URL: en referens till externa data. Den maximala storleken för ett enskilt dokument är 16 MB (eller mindre om du är bulk överför flera dokument i en begäran). [Gränser i Azure Search](search-limits-quotas-capacity.md) har mer information.
>

**Frågan volym överväganden**

Frågor per sekund (QPS) är ett mått som får plats under inställning av prestanda, men vanligtvis är inte en nivå faktor om du förväntar dig mycket hög frågan volym från början.

Alla standard nivåerna kan ge en balans mellan replikerna till partitioner, stöder efterfrågande frågan via ytterligare repliker för att läsa in belastningsutjämning och ytterligare partitioner för parallell bearbetning. Du kan finjustera prestanda när tjänsten har etablerats.

Kunden som förväntar sig att strong upprätthållas frågan volymer från början bör tänka på högre nivåer, backas upp av kraftigare maskinvara. Du kan sedan kopplar partitioner och repliker eller växla till en lägre nivå tjänst även om volymerna fråga inte Materialisera. Mer information om hur du beräknar frågan genomströmning finns [Azure Search-prestanda och optimering](search-performance-optimization.md).


**Servicenivåavtal**

Den **lediga** nivå och förhandsgranska funktioner inte kommer med [serviceavtal (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/). För alla fakturerbar nivåer börjar SLA gälla när du etablerar tillräcklig redundans för din tjänst. Två eller flera repliker krävs för SERVICENIVÅAVTAL för frågan (läsa). Tre eller flera repliker krävs för fråga och indexering SLA (skrivskyddad). Antalet partitioner är inte ett SLA-faktor. 

## <a name="tips-for-tier-evaluation"></a>Tips för nivån utvärdering

+ Lär dig att skapa effektiva index och vilka metoder för uppdatering är den minsta impactful. Vi rekommenderar [söka trafik analytics](search-traffic-analytics.md) för kunskap för aktiviteten fråga.

+ Tillåt mått skapa runt frågor och samla in data runt användningsmönster (frågor under kontorstid, indexering vid låg belastning) och använda dessa data för att informera framtida service etableringsbeslut. Du kan dynamiskt justera partitioner och resurser för att hantera planerade ändringar i frågan volymer, eller oplanerade men varaktigt ändringar om nivåer håller tillräckligt lång som backar upp åtgärda medan inte praktiska på en nivå med timvis eller dagligen.

+ Kom ihåg att endast Nackdelen med av under etableringen är att du kan behöva går sönder ned en tjänst om de faktiska kraven är större än du beräknad. För att undvika avbrott i tjänsten kan du skapa en ny tjänst i samma prenumeration på en högre nivå och köras sida vid sida förrän alla appar och begäranden mål ny slutpunkt.

## <a name="next-steps"></a>Nästa steg

Börja med en **lediga** nivån och skapa ett inledande index med en delmängd av dina data för att förstå dess egenskaper. Datastrukturen i Azure Search är en inverterad index där storlek och komplexitet för ett inverterad index bestäms av innehållet. Kom ihåg att hög redundant innehåll tenderar att resultera i ett index som är mindre än hög oregelbundna innehåll. Därför är det innehåll egenskaper i stället för storleken på den datamängd som bestämmer indexet lagringsbehov.

När du har en inledande uppfattning om Indexstorlek [etablera en fakturerbar tjänst](search-create-service-portal.md) på någon av de nivåer som beskrivs i den här artikeln antingen **grundläggande** eller en **Standard** nivå. Minska konstgjorda villkor i datadelmängder och [återskapa index](search-howto-reindex.md) att inkludera alla data som ska sökas.

[Allokera partitioner och repliker](search-capacity-planning.md) som behövs för att få prestanda och skalning som du behöver.

Om prestanda och kapacitet accepterar, är du klar. Annars kan återskapa en söktjänst på ett annat skikt mer stämmer överens med dina behov.

> [!NOTE]
> Mer hjälp med frågor efter att [StackOverflow](https://stackoverflow.com/questions/tagged/azure-search) eller [kontakta Azure-supporten](https://azure.microsoft.com/support/options/).
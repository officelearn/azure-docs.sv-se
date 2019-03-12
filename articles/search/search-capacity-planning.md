---
title: Skala partitioner och -repliker för frågor och indexering - söka i Azure
description: Justera partition och repliken datorresurser i Azure Search, där varje resurs prissätts i fakturerbara search-enheter.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 03/08/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 69fce34c55007daff48b2463da590ffb9cd59926
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775330"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Skala partitioner och -repliker för fråge- och indexeringsarbetsbelastningar i Azure Search
När du [Välj en prisnivå](search-sku-tier.md) och [etablera en söktjänst](search-create-service-portal.md), nästa steg är att du kan också öka antalet repliker eller partitioner som används av din tjänst. Varje nivå erbjuder ett fast antal faktureringsenheter. Den här artikeln beskriver hur du allokera dessa enheter för att uppnå en optimal konfiguration som balanserar dina krav för frågekörning, indexering och lagring.

Resurskonfigurationen är tillgänglig när du ställer in en tjänst på den [Basic-nivån](https://aka.ms/azuresearchbasic) eller någon av de [Standard-nivåerna](search-limits-quotas-capacity.md). För tjänster på dessa nivåer, kapacitet köps i steg om *Sök enheter* (su) där varje partition och repliken räknas som en SU. 

Med färre SUs-resultat i en proportionellt lägre kostnader. Fakturering har aktiverats för förutsatt att tjänsten har ställts in. Om du tillfälligt inte använder en tjänst, är det enda sättet att undvika fakturering som tar bort tjänsten och sedan återskapa den när du behöver den.

> [!Note]
> Ta bort en tjänst tar bort allt på den. Det finns ingen möjlighet i Azure Search för att säkerhetskopiera och återställa sparas söka efter data. Om du vill distribuera om ett befintligt index på en ny tjänst, bör du köra det program som används för att skapa och läsa in den ursprungligen. 

## <a name="terminology-replicas-and-partitions"></a>Terminologi: repliker och partitioner
Repliker och partitioner är de viktigaste resurserna som stöder en söktjänst.

| Resurs | Definition |
|----------|------------|
|*Partitioner* | Innehåller indexet lagring och i/o för läs-/ skrivåtgärder (till exempel när återskapa eller uppdatera ett index).|
|*Repliker* | Instanser för search-tjänsten används främst för att läsa in saldo frågeåtgärder. Varje replik värd alltid en kopia av ett index. Om du har 12 repliker har du 12 kopior av alla index som har lästs in på tjänsten.|

> [!NOTE]
> Det går inte att direkt hantera eller hantera vilka index som körs på en replik. En kopia av varje indexet för varje replik är en del av arkitekturen för tjänsten.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Så här allokerar du repliker och partitioner
I Azure Search allokeras en tjänst först en minimal nivå på resurser som består av en partition och en replik. För nivåerna som har stöd för det justera du stegvis resurser genom att öka partitioner om du behöver mer lagringsutrymme och i/o, eller lägga till fler repliker för större volymer i frågan eller bättre prestanda. En enskild tjänst måste ha tillräckligt med resurser för att hantera alla arbetsbelastningar (indexering och frågor). Du kan inte dela upp arbetsbelastningar mellan flera tjänster.

Om du vill öka eller ändra allokeringen av repliker och partitioner, bör du använda Azure-portalen. Portalen framtvingar gränser för tillåtna kombinationer som vara under gränsvärden. Om du behöver en skriptbaserade eller kodbaserad etablering metod, den [Azure PowerShell](search-manage-powershell.md) eller [Management REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) är alternativa lösningar.

Sökprogram behöver i allmänhet fler repliker än partitioner, särskilt när tjänståtgärderna är prioriterar frågearbetsbelastningar. I avsnittet om [hög tillgänglighet](#HA) förklarar varför.

1. Logga in på den [Azure-portalen](https://portal.azure.com/) och välj search-tjänst.
2. I **inställningar**öppnar den **skala** att ändra repliker och partitioner. 

   Följande skärmbild visar ett standard-tjänsten med en replik respektive partition. Formeln längst ned anger hur många search-enheter som ska användas (1). Om enhetspriset var $100 (inte en verklig kostnad), är månatliga kostnaden för att köra den här tjänsten $100 i genomsnitt.

   ![Skala sida som visar aktuella värden](media/search-capacity-planning/1-initial-values.png "skala sida som visar aktuella värden")

3. Använd skjutreglaget för att öka eller minska antalet partitioner. Formeln längst ned anger hur många search-enheter som används.

   Det här exemplet fördubblar kapacitet, med två repliker och partitioner var och en. Lägg märke till search enhet count; Nu är det fyra eftersom fakturering formeln är repliker multiplicerat med partitioner (2 × 2). En fördubbling kapacitet fördubblar mer än kostnaden för att köra tjänsten. Om sökningen enhetskostnad var $100, är nu nya månadsfaktura 400 USD.

   Aktuellt per enhet kostnaderna för varje nivå, finns det [prissidan](https://azure.microsoft.com/pricing/details/search/).

   ![Lägg till repliker och partitioner](media/search-capacity-planning/2-add-2-each.png "lägga till repliker och partitioner")

3. Klicka på **spara** att bekräfta ändringarna.

   ![Bekräfta ändringar för skalning och fakturering](media/search-capacity-planning/3-save-confirm.png "bekräfta ändringar för skalning och fakturering")

   Ändringar i kapaciteten ta flera timmar att slutföra. Du kan inte avbryta när processen har startats och det finns ingen övervakning i realtid för replik och partition justeringar. Följande meddelande fortfarande visas när ändringarna är pågår.

   ![Statusmeddelande i portalen](media/search-capacity-planning/4-updating.png "statusmeddelande i portalen")


> [!NOTE]
> När en tjänst har etablerats kan inte uppgraderas till en högre SKU. Du måste skapa en söktjänst på den nya nivån och läsa in dina index. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) hjälp med Tjänstetablering.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinationer av partition och repliken

En grundläggande tjänst kan ha exakt en partition och upp till tre repliker i maximalt begränsa tre SUS. Endast justerbara resursen är repliker. Du behöver minst två repliker för hög tillgänglighet på frågor.

Alla tjänster som standard kan anta följande kombinationer av repliker och partitioner, omfattas av 36 SU-gränsen. 

|   | **1 partition** | **2 partitioner** | **3 partitioner** | **4 partitioner** | **6 partitioner** | **12 partitioner** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replik** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliker** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliker** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliker** |4 SU |8 SU |12 SU |16 SU |24 SU |Gäller inte |
| **5 repliker** |5 SU |10 SU |15 SU |20 SU |30 SU |Gäller inte |
| **6 repliker** |6 SU |12 SU |18 SU |24 SU |36 SU |Gäller inte |
| **12 repliker** |12 SU |24 SU |36 SU |Gäller inte |Saknas |Gäller inte |

SUs, priser och kapacitet beskrivs i detalj på Azure-webbplatsen. Mer information finns i [prisinformation om](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Antal repliker och partitioner jämnt delas upp i 12 (mer specifikt 1, 2, 3, 4, 6, 12). Det beror på att Azure Search dividerar före varje index 12 horisontellt så att de går att sprida lika stora delar för alla partitioner. Om tjänsten har tre partitioner och du skapar ett index, till exempel innehåller varje partition fyra delar av indexet. Hur Azure Search shards ett index är en implementeringsdetalj, kan ändras i framtida utgåvor. Även om talet är 12 idag, bör inte kommer detta antal ska alltid vara 12 i framtiden.
>


<a id="HA"></a>

## <a name="high-availability"></a>Hög tillgänglighet
Eftersom det är enkelt och relativt snabbt att skala upp allmänhet rekommenderar vi att du börjar med en partition och en eller två repliker och sedan skala upp som frågan volymer skapa. Fråga arbetsbelastningar främst för repliker. Om du behöver mer dataflöde eller hög tillgänglighet kan förmodligen du ytterligare repliker.

Allmänna rekommendationer för hög tillgänglighet är:

* Två repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor)
* Tre eller fler repliker för hög tillgänglighet för Läs/Skriv-arbetsbelastningar (frågor plus indexering när enskilda dokument läggs till, uppdateras eller tas bort)

Servicenivåavtal (SLA) för Azure Search riktas på frågeåtgärder och uppdateringar av index som består av att lägga till, uppdatera eller ta bort dokument.

Basic-nivån överkant på en partition och tre repliker. Om du vill ha flexibiliteten svara omedelbart på grund av variationer i efterfrågan på både indexerings- och dataflöde du något av Standard-nivåerna.

### <a name="index-availability-during-a-rebuild"></a>Indexet tillgänglighet under återskapas

Hög tillgänglighet för Azure Search gäller frågor och index-uppdateringar som inte rör när ett index. Om du tar bort ett fält, ändra datatypen eller byta namn på ett fält måste du återskapa indexet. Om du vill återskapa indexet, måste du ta bort indexet, återskapa indexet och läsa in data.

> [!NOTE]
> Du kan lägga till nya fält till ett Azure Search-index utan att återskapa indexet. Värdet för det nya fältet ska vara null för alla dokument redan i indexet.

Om du vill behålla indexet tillgänglighet under en återskapning av en du har en kopia av indexet med ett annat namn på samma tjänst eller en kopia av indexet med samma namn på en annan tjänst och sedan ange omdirigering eller redundanslogiken i din kod.

## <a name="disaster-recovery"></a>Haveriberedskap
Det finns för närvarande ingen inbyggd mekanism för katastrofåterställning. Att lägga till partitioner eller repliker är fel strategin i syfte att uppfylla katastrofåterställningsmål. Den vanligaste metoden är att lägga till redundans på tjänstnivå genom att ställa in en andra söktjänst i en annan region. Precis som med tillgängliga under en återskapning av ett index, måste omdirigering av eller redundanslogiken komma från din kod.

## <a name="increase-query-performance-with-replicas"></a>Öka prestanda för frågor med repliker
Frågesvarstiden är en indikator att ytterligare repliker behövs. I allmänhet är ett första steg mot att förbättra frågeprestanda att lägga till mer av den här resursen. När du lägger till repliker fler kopior av indexet åter är online att stödja större arbetsbelastningar i fråga och läsa in Utjämna begäranden över flera repliker.

Vi kan inte ge hårda uppskattningar på frågor per sekund (QPS): fråga prestanda beror på komplexiteten i frågan och konkurrerande arbetsbelastningar. Även om att lägga till repliker tydligt resulterar i bättre prestanda, resultatet är inte strikt linjär: lägga till tre repliker garanterar inte tre dataflöde.

Vägledning för att uppskatta frågor per sekund för dina arbetsbelastningar finns i [överväganden för prestanda och optimering av Azure Search](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Öka prestanda med partitioner för innehållsindexering
Sök efter program som kräver nästan i realtid datauppdatering behöver proportionellt fler partitioner än repliker. Att lägga till partitioner sprider ut läs-/ skrivåtgärder över ett större antal beräkningsresurser. Du får också mer diskutrymme för att lagra ytterligare index och dokument.

Större index ta längre tid att fråga. Därför kanske du upptäcker att varje inkrementell ökning av partitioner kräver en mindre men proportionell ökning av repliker. Komplexitet frågorna och fråga volymen ska ta med i hur snabbt Frågekörningen stängs.


## <a name="next-steps"></a>Nästa steg

[Välj en prisnivå för Azure Search](search-sku-tier.md)

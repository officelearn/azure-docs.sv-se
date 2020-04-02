---
title: Justera kapaciteten för fråge- och indexarbetsbelastningar
titleSuffix: Azure Cognitive Search
description: Justera partitions- och replikdatorresurser i Azure Cognitive Search, där varje resurs är prissatt i fakturerbara sökenheter.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 270ff3c3e8e4cffbb1f4b1987ee497530d0c0982
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80546265"
---
# <a name="adjust-capacity-in-azure-cognitive-search"></a>Justera kapaciteten i Azure Cognitive Search

Innan [du etablerar en söktjänst](search-create-service-portal.md) och låser i en viss prisnivå, ta några minuter att förstå rollen för repliker och partitioner i en tjänst och hur du kan justera en tjänst för att rymma toppar och dips i resursbehov.

Kapacitet är en funktion för den [nivå du väljer](search-sku-tier.md) (nivåer bestämmer maskinvaruegenskaper) och den kombination av replik och partition som krävs för projicerade arbetsbelastningar. Beroende på nivån och storleken på justeringen kan det ta allt från 15 minuter till flera timmar att lägga till eller minska kapaciteten. 

När du ändrar allokeringen av repliker och partitioner rekommenderar vi att du använder Azure-portalen. Portalen tillämpar gränser för tillåtna kombinationer som håller sig under maxgränserna för en nivå. Men om du behöver en skriptbaserad eller kodbaserad etableringsmetod är [Azure PowerShell](search-manage-powershell.md) eller [REST API för hantering](https://docs.microsoft.com/rest/api/searchmanagement/services) alternativa lösningar.

## <a name="terminology-replicas-and-partitions"></a>Terminologi: repliker och partitioner

|||
|-|-|
|*Partitioner* | Tillhandahåller indexlagring och I/O för läs-/skrivåtgärder (till exempel vid ombyggnad eller uppdatering av ett index). Varje partition har en andel av det totala indexet. Om du allokerar tre partitioner är indexet uppdelat i tredjedelar. |
|*Repliker* | Instanser av söktjänsten, som främst används för att läsa in balansfrågorsåtgärder. Varje replik är en kopia av ett index. Om du allokerar tre repliker har du tre kopior av ett index tillgängligt för service av frågebegäranden.|

## <a name="when-to-add-nodes"></a>När du ska lägga till noder

Inledningsvis allokeras en tjänst en minimal resursnivå som består av en partition och en replik. 

En enda tjänst måste ha tillräckliga resurser för att hantera alla arbetsbelastningar (indexering och frågor). Ingen av arbetsbelastningarna körs i bakgrunden. Du kan schemalägga indexering för tider när frågebegäranden är naturligt mindre frekventa, men tjänsten prioriterar annars inte en aktivitet framför en annan. Dessutom jämnar en viss redundans ut frågeprestanda när tjänster eller noder uppdateras internt.

Som en allmän regel tenderar sökprogram att behöva fler repliker än partitioner, särskilt när tjänståtgärderna är partiska mot frågearbetsbelastningar. Avsnittet om [hög tillgänglighet](#HA) förklarar varför.

> [!NOTE]
> Om du lägger till fler repliker eller partitioner ökar kostnaden för att köra tjänsten och kan införa små variationer i hur resultaten ordnas. Var noga med att kontrollera [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att förstå faktureringskonsekvenserna av att lägga till fler noder. [Diagrammet nedan](#chart) kan hjälpa dig att korsreferera antalet sökenheter som krävs för en viss konfiguration. Mer information om hur ytterligare repliker påverkar frågebearbetning finns i [Beställa resultat](search-pagination-page-layout.md#ordering-results).

## <a name="how-to-allocate-replicas-and-partitions"></a>Så här allokerar du repliker och partitioner

1. Logga in på [Azure-portalen](https://portal.azure.com/) och välj söktjänsten.

1. Öppna sidan **Skala** i **Inställningar**för att ändra repliker och partitioner. 

   Följande skärmbild visar en standardtjänst som etablerats med en replik och partition. Formeln längst ned anger hur många sökenheter som används (1). Om a-priset var $ 100 (inte ett verkligt pris), skulle den månatliga kostnaden för att köra denna tjänst vara $ 100 i genomsnitt.

   ![Skala sida som visar aktuella värden](media/search-capacity-planning/1-initial-values.png "Skala sida som visar aktuella värden")

1. Använd skjutreglaget för att öka eller minska antalet partitioner. Formeln längst ned anger hur många sökenheter som används.

   Det här exemplet fördubblar kapaciteten, med två repliker och partitioner vardera. Lägg märke till antalet sökenheter. Det är nu fyra eftersom faktureringsformeln är repliker multiplicerat med partitioner (2 x 2). En fördubblingskapacitet mer än fördubblar kostnaden för att köra tjänsten. Om sökenheten kostnaden var $ 100, skulle den nya månatliga räkningen nu vara $ 400.

   För de aktuella kostnaderna per enhet för varje nivå besöker du [sidan Prissättning](https://azure.microsoft.com/pricing/details/search/).

   ![Lägga till repliker och partitioner](media/search-capacity-planning/2-add-2-each.png "Lägga till repliker och partitioner")

1. Klicka på **Spara** för att bekräfta ändringarna.

   ![Bekräfta ändringar i skala och fakturering](media/search-capacity-planning/3-save-confirm.png "Bekräfta ändringar i skala och fakturering")

   Kapacitetsändringar tar flera timmar att slutföra. Du kan inte avbryta när processen har startat och det inte finns någon övervakning i realtid för replik- och partitionsjusteringar. Följande meddelande förblir dock synligt medan ändringar pågår.

   ![Statusmeddelande i portalen](media/search-capacity-planning/4-updating.png "Statusmeddelande i portalen")

> [!NOTE]
> När en tjänst har etablerats kan den inte uppgraderas till en högre nivå. Du måste skapa en söktjänst på den nya nivån och läsa in indexen igen. Se [Skapa en Azure Cognitive Search-tjänst i portalen](search-create-service-portal.md) för hjälp med tillhandahållande av tjänster.
>
> Dessutom hanteras partitioner och repliker uteslutande och internt av tjänsten. Det finns inget begrepp om processortillhörighet eller tilldela en arbetsbelastning till en viss nod.
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinationer av partitioner och repliker

En basic-tjänst kan ha exakt en partition och upp till tre repliker, för en maximal gräns på tre SUs. Den enda justerbara resursen är repliker. Du behöver minst två repliker för hög tillgänglighet på frågor.

Alla standard- och lagringsoptimerade söktjänster kan anta följande kombinationer av repliker och partitioner, med förbehåll för 36-SU-gränsen. 

|   | **1 partition** | **2 partitioner** | **3 partitioner** | **4 partitioner** | **6 partitioner** | **12 partitioner** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replik** |1 SU (AVSU) |2 SU (SU) |3 SU (SU) |4 SU (SU) |6 SU (SU) |12 SU (SU) |
| **2 repliker** |2 SU (SU) |4 SU (SU) |6 SU (SU) |8 SU (SU) |12 SU (SU) |24 SU (SU) |
| **3 repliker** |3 SU (SU) |6 SU (SU) |9 SU (SU) |12 SU (SU) |18 SU (AVS) |36 SU (AVSU) |
| **4 repliker** |4 SU (SU) |8 SU (SU) |12 SU (SU) |16 SU (AVS) |24 SU (SU) |Ej tillämpligt |
| **5 repliker** |5 SU (SU) |10 SU (SU) |15 SU (AVS) |20 SU (SU) |30 SU (SU) |Ej tillämpligt |
| **6 repliker** |6 SU (SU) |12 SU (SU) |18 SU (AVS) |24 SU (SU) |36 SU (AVSU) |Ej tillämpligt |
| **12 repliker** |12 SU (SU) |24 SU (SU) |36 SU (AVSU) |Ej tillämpligt |Ej tillämpligt |Ej tillämpligt |

SUs, prissättning och kapacitet förklaras i detalj på Azure-webbplatsen. Mer information finns i [Prisinformation](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Antalet repliker och partitioner delas jämnt in i 12 (specifikt 1, 2, 3, 4, 6, 12). Detta beror på att Azure Cognitive Search förenar varje index i 12 shards så att det kan spridas i lika delar över alla partitioner. Om tjänsten till exempel har tre partitioner och du skapar ett index, innehåller varje partition fyra fragment av indexet. Hur Azure Cognitive Search fragments ett index är en implementeringsdetalj, kan ändras i framtida versioner. Även om antalet är 12 idag, bör du inte förvänta dig att antalet alltid vara 12 i framtiden.
>

<a id="HA"></a>

## <a name="high-availability"></a>Hög tillgänglighet

Eftersom det är enkelt och relativt snabbt att skala upp rekommenderar vi i allmänhet att du börjar med en partition och en eller två repliker och sedan skala upp som frågevolymer bygger. Frågearbetsbelastningar körs främst på repliker. Om du behöver mer dataflöde eller hög tillgänglighet, behöver du förmodligen ytterligare repliker.

Allmänna rekommendationer för hög tillgänglighet är:

* Två repliker för hög tillgänglighet för skrivskyddade arbetsbelastningar (frågor)

* Tre eller flera repliker för hög tillgänglighet för läs-/skrivarbetsbelastningar (frågor plus indexering när enskilda dokument läggs till, uppdateras eller tas bort)

Servicenivåavtal (SLA) för Azure Cognitive Search är inriktade på frågeåtgärder och till indexuppdateringar som består av att lägga till, uppdatera eller ta bort dokument.

Grundläggande nivå toppar ut på en partition och tre repliker. Om du vill ha flexibiliteten att omedelbart svara på fluktuationer i efterfrågan på både indexering och frågedataflöde bör du överväga en av standardnivåerna.  Om du upptäcker att dina lagringskrav växer mycket snabbare än frågedataflödet bör du överväga en av lagringsoptimerade nivåerna.

## <a name="disaster-recovery"></a>Haveriberedskap

För närvarande finns det ingen inbyggd mekanism för katastrofåterställning. Lägga till partitioner eller repliker skulle vara fel strategi för att uppfylla mål för haveriberedskap. Den vanligaste metoden är att lägga till redundans på servicenivå genom att konfigurera en andra söktjänst i en annan region. Precis som med tillgänglighet under en indexrekläggning måste omdirigerings- eller redundanslogiken komma från koden.

## <a name="estimate-replicas"></a>Uppskatta repliker

På en produktionstjänst bör du allokera tre repliker för SLA-syften. Om du upplever långsam frågeprestanda kan du lägga till repliker så att ytterligare kopior av indexet förs online för att stödja större frågearbetsbelastningar och för att läsa in begäranden över flera repliker.

Vi ger inga riktlinjer för hur många repliker som behövs för att hantera frågebelastningar. Frågeprestanda beror på frågans komplexitet och konkurrerande arbetsbelastningar. Även om det tydligt resulterar i bättre prestanda att lägga till repliker är resultatet inte strikt linjärt: att lägga till tre repliker garanterar inte trippeldataflöde.

Vägledning för att uppskatta QPS för din lösning finns i [Skala för prestanda-](search-performance-optimization.md)och [monitorfrågor](search-monitor-queries.md)

## <a name="estimate-partitions"></a>Uppskatta partitioner

Den [nivå du väljer](search-sku-tier.md) bestämmer partitionens storlek och hastighet, och varje nivå är optimerad kring en uppsättning egenskaper som passar olika scenarier. Om du väljer en högre slutnivå kan du behöva färre partitioner än om du går med S1. En av de frågor du måste besvara genom självstyrd testning är om en större och dyrare partition ger bättre prestanda än två billigare partitioner på en tjänst som etablerats på en lägre nivå.

Sökprogram som kräver datauppdatering i nära realtid behöver proportionellt fler partitioner än repliker. Om du lägger till partitioner sprids läs-/skrivåtgärder över ett större antal beräkningsresurser. Det ger dig också mer diskutrymme för att lagra ytterligare index och dokument.

Större index tar längre tid att fråga. Därför kanske du upptäcker att varje inkrementell ökning av partitioner kräver en mindre men proportionell ökning av repliker. Komplexiteten i dina frågor och frågevolymen kommer att faktor i hur snabbt frågekörningen vänds.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj en prisnivå för Azure Cognitive Search](search-sku-tier.md)
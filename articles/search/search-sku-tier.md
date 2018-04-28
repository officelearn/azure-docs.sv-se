---
title: Välj en SKU eller prisnivån för Azure Search | Microsoft Docs
description: 'Azure Search kan etableras på dessa SKU: er: ledigt, Basic eller Standard, där Standard finns i olika resurskonfigurationer och kapacitet för nivåerna.'
services: search
author: HeidiSteen
manager: cgronlun
tags: azure-portal
ms.service: search
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: heidist
ms.openlocfilehash: 6fe41a8c4d184fef4d1bb0a12fed44a49ef8a6da
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="choose-a-sku-or-pricing-tier-for-azure-search"></a>Välj en SKU eller prisnivå för Azure Search
I Azure Search en [service etableras](search-create-service-portal.md) på en specifik prisnivån eller SKU: N. Alternativen är **lediga**, **grundläggande**, eller **Standard**, där **Standard** är tillgänglig i flera konfigurationer och kapacitet.

Syftet med den här artikeln är att hjälpa dig att välja en nivå. Om en nivåkapaciteten visar sig vara för lågt, behöver du etablera en ny tjänst på högre nivå och sedan återaktivera ditt index. Det finns ingen uppgradering på plats av samma tjänst från en-SKU till en annan.

> [!NOTE]
> När du har valt en nivå och [etablera en söktjänst](search-create-service-portal.md), kan du öka replik och partitionen räknar inom tjänsten. Anvisningar finns [skala resursen nivåer för fråga och indexering arbetsbelastningar](search-capacity-planning.md).
>
>

## <a name="how-to-approach-a-pricing-tier-decision"></a>Hur du hanterar prisnivå nivå beslut
I Azure Search anger nivån kapacitet, inte funktionstillgänglighet. I allmänhet är funktioner tillgängliga på varje nivå, inklusive funktioner för förhandsgranskning. Det enda undantaget är inget stöd för indexerare i S3 HD.

> [!TIP]
> Vi rekommenderar att du alltid etablera en **lediga** service (en per prenumeration utan slutdatum) så att dess är tillgängligt för inaktiverad projekt. Använd den **lediga** för testning och utvärdering; skapa en andra fakturerbar tjänst på den **grundläggande** eller **Standard** nivå för produktion eller större arbetsbelastningar för testning.
>
>

Gå hand i hand kapacitet och kostnader för att köra tjänsten. Informationen i den här artikeln hjälper dig att bestämma vilka SKU ger rätt balans, men för någon av den ska vara användbar, behöver du minst grov uppskattning på följande:

* Antalet och storleken på index som du planerar att skapa
* Antalet och storleken på dokument för att ladda upp
* Vissa uppfattning om frågan volym, vad gäller frågor Per andra (QPS). Anvisningar finns [Azure Search-prestanda och optimering](search-performance-optimization.md).

Antal och storlek är viktiga eftersom den maximala gränsen nås via en hård gräns på antalet index per tjänst eller på resurser (lagring eller repliker) som används av tjänsten. Den faktiska gränsen för tjänsten är beroende på vilket som hämtar används först: resurser eller objekt.

Följande steg ska förenkla processen för beräkningar i manuellt:

* **Steg 1** granska SKU beskrivningarna nedan om du vill veta mer om tillgängliga alternativ.
* **Steg 2** svara på frågorna nedan för att få fram en preliminär beslut.
* **Steg 3** fattar ditt beslut genom att granska hårda gränser för lagring och priser.

## <a name="sku-descriptions"></a>Beskrivningar av SKU
Följande tabell innehåller beskrivningar av varje nivå.

| Nivå | Primära scenarier |
| --- | --- |
| **Ledigt** |En delad tjänst utan kostnad, används för utvärdering, undersökningen eller små arbetsbelastningar. Frågan genomflöde och indexering varierar beroende på vem mer använder tjänsten eftersom den delas med andra prenumeranter. Kapacitet är liten (50 MB eller 3 index med upp 10 000 dokument varje). |
| **Basic** |Liten produktionsarbetsbelastningar på dedikerad maskinvara. Hög tillgänglighet. Kapaciteten är upp till 3 repliker och 1 partition (2 GB). |
| **S1** |Standard 1 stöder flexibel kombinationer av partitioner (12) och repliker (12) som används för medelstora produktionsarbetsbelastningar på dedikerad maskinvara. Du kan allokera partitioner och repliker i kombinationer som stöds av ett maximalt antal 36 fakturerbar search-enheter. Partitioner är 25 GB på den här nivån. |
| **S2** |Standard 2 kör större produktionsarbetsbelastningar som använder samma 36 search-enheter som S1 men med större storlek partitioner och repliker. Partitioner är 100 GB vardera på den här nivån. |
| **S3** |Standard 3 körs proportionellt större produktionsarbetsbelastningar på högre kompletta system i konfigurationer av upp till 12 partitioner eller 12 repliker under 36 search-enheter. Partitioner är 200 GB på den här nivån. |
| **S3 HD** |Standard 3 hög densitet är avsedd för ett stort antal mindre index. Du kan ha upp till 3 partitioner på 200 GB.|

> [!NOTE]
> Replik och partitionen maxkapacitet debiteras ut som search-enheter (36 unit maximala per tjänst), vilket medför en effektiv nedre gräns än maximalt innebär på framsidan värde. Om du vill använda maximalt 12 repliker, kan du till exempel ha högst 3 partitioner (12 * 3 = 36 enheter). På samma sätt för att använda maximalt partitioner, minska repliker till 3. Se [skala resursen nivåer för fråga och indexering arbetsbelastningar i Azure Search](search-capacity-planning.md) för diagram på tillåtna kombinationer.
>
>

## <a name="review-limits-per-tier"></a>Granska gränser för nivån
Följande diagram är en delmängd av gränser från [Tjänstbegränsningarna i Azure Search](search-limits-quotas-capacity.md). Den listar faktorerna som sannolikt påverkar SKU beslut. Du kan referera till det här diagrammet när du granskar frågorna nedan.

| Resurs | Kostnadsfri | Basic | S1 | S2 | S3 | S3 HD |
| --- | --- | --- | --- | --- | --- | --- |
| Serviceavtal (SLA) |Nr <sup>1</sup> |Ja |Ja |Ja |Ja |Ja |
| Index gränser |3 |5 |50 |200 |200 |1000 <sup>2</sup> |
| Dokumentet gränser |10 000 totalt |1 miljon per tjänst |15 miljoner per partition |60 miljoner per partition |120 miljoner per partition |1 miljon per index |
| Maximal partitioner |Gäller inte |1 |12 |12 |12 |3 <sup>2</sup> |
| Partitionsstorlek |Totalt antal 50 MB |2 GB per tjänst |25 GB per partition |100 GB per partition (upp till högst 1,2 TB per tjänst) |200 GB per partition (upp till högst 2,4 TB per tjänst) |200 GB (upp till 600 GB per tjänst) |
| Maximal repliker |Gäller inte |3 |12 |12 |12 |12 |

<sup>1</sup> ledigt nivå och förhandsgranska funktioner inte kommer med servicenivåavtal (SLA). För alla fakturerbar nivåer börjar SLA gälla när du etablerar tillräcklig redundans för din tjänst. Två eller flera repliker krävs för SERVICENIVÅAVTAL för frågan (läsa). Tre eller flera repliker krävs för fråga och indexering SLA (skrivskyddad). Antalet partitioner är inte ett SLA-faktor. 

<sup>2</sup> S3 och S3 HD backas upp av identiska hög kapacitet infrastruktur, men var och en når sin maximala gränsen på olika sätt. S3 riktar sig till ett mindre antal mycket stora index. Därför dess maxgränsen är bundet till en resurs (2,4 TB för varje tjänst). S3 HD riktar sig till ett stort antal mycket små index. S3 HD når gränsen i form av indexet begränsningar på 1 000 index. Om du är en S3 HD kund som kräver mer än 1 000 index kan kontakta Microsoft Support för mer information om hur du fortsätter.

## <a name="eliminate-skus-that-dont-meet-requirements"></a>Eliminera SKU: er som inte uppfyller kraven
Följande frågor kan hjälpa dig komma fram till SKU rätt beslut för din arbetsbelastning.

1. Har du **serviceavtalet (SLA)** krav? Du kan använda alla fakturerbar nivå (Basic på dig), men du måste konfigurera din tjänst för redundans. Två eller flera repliker krävs för SERVICENIVÅAVTAL för frågan (läsa). Tre eller flera repliker krävs för fråga och indexering SLA (skrivskyddad). Antalet partitioner är inte ett SLA-faktor.
2. **Hur många indexerar** behöver du? En av de största variabler factoring i beslut SKU är antalet index som stöds av varje SKU. Index support finns på markant olika nivåer i de nedre prisnivåerna. Krav för antalet index kan vara en avgörande faktorn vad gäller SKU beslut.
3. **Hur många dokument** läses in i varje index? Antalet och storleken på dokument som avgör den slutliga storleken för indexet. Under förutsättning att du kan beräkna planerade storleken på indexet, kan du jämföra det numret mot partitionsstorlek per SKU och utökas med antalet partitioner som krävs för att lagra ett index över den storleken.
4. **Vad är förväntade frågebelastningen**? När lagringskraven förstås, Överväg frågan arbetsbelastningar. S2 och båda S3 SKU: er erbjuder nära motsvarande dataflöde, men SLA-krav ska utesluta alla preview SKU: er.
5. Om du funderar på S2 eller S3 nivån avgöra om du behöver [indexerare](search-indexer-overview.md). Indexerare är ännu inte tillgängliga för S3 HD-nivå. Alternativ metod är att använda en push-modell för index uppdateringar, där du kan skriva programkod att skicka en uppsättning data till ett index.

De flesta kunder kan regeln en specifik SKU in eller ut baserat på deras svar på dessa frågor. Om du fortfarande inte vet vilka SKU att gå med, kan du ställa frågor till MSDN- eller StackOverflow forum eller kontakta Azure Support för ytterligare.

## <a name="decision-validation-does-the-sku-offer-sufficient-storage-and-qps"></a>Decision validering: erbjuder SKU: N tillräckligt med lagringsutrymme och QPS?
Kontrollera igen som ett sista steg i [sida med priser](https://azure.microsoft.com/pricing/details/search/) och [per service och per index avsnitt i Tjänstbegränsningarna](search-limits-quotas-capacity.md) att dubbelkolla uppskattningar mot prenumeration och tjänsten gränser.

Om priset eller lagring kraven är utanför intervallet, kanske du vill refactor arbetsbelastningar bland flera mindre tjänster (till exempel). På detaljnivå, kan du ändra designen index ska vara mindre eller använda filter för att effektivisera frågor.

> [!NOTE]
> Lagringskraven kan vara felaktigt luftfyllda om dokument innehåller främmande data. Vi rekommenderar innehåller dokument bara sökbara data eller metadata. Binära data är icke-sökbart och ska lagras separat (kanske i ett Azure table eller blob storage) med ett fält i indexet för en URL: en referens till externa data. Den maximala storleken för ett enskilt dokument är 16 MB (eller mindre om du är bulk överför flera dokument i en begäran). Se [gränser i Azure Search](search-limits-quotas-capacity.md) för mer information.
>
>

## <a name="next-step"></a>Nästa steg
När du vet vilket SKU är rätt anpassning kan fortsätta på med de här stegen:

* [Skapa en söktjänst på portalen](search-create-service-portal.md)
* [Ändra tilldelning av partitioner och repliker att skala din tjänst](search-capacity-planning.md)

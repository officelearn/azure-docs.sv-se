---
title: Skala partitioner och repliker för frågor och indexering – Azure Search
description: Justera partitions-och replik dator resurser i Azure Search, där varje resurs priss ätts i de fakturerbara Sök enheterna.
author: HeidiSteen
manager: nitinme
services: search
ms.service: search
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: c048dcf31d8f434f742d2da9351ef9b46f0a71d4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69650067"
---
# <a name="scale-partitions-and-replicas-for-query-and-indexing-workloads-in-azure-search"></a>Skala partitioner och repliker för att köra frågor och indexera arbets belastningar i Azure Search
När du har [valt en pris nivå](search-sku-tier.md) och [tillhandahåller en Sök tjänst](search-create-service-portal.md), är nästa steg att välja att öka antalet repliker eller partitioner som används av tjänsten. Varje nivå erbjuder ett fast antal fakturerings enheter. I den här artikeln förklaras hur du allokerar dessa enheter för att uppnå en optimal konfiguration som balanserar dina krav för frågekörningen, indexering och lagring.

Resurs konfigurationen är tillgänglig när du konfigurerar en tjänst på Basic- [nivån](https://aka.ms/azuresearchbasic) eller någon av de [optimerade standard-eller lagrings nivåerna](search-limits-quotas-capacity.md). För tjänster på dessa nivåer köps kapaciteten i steg om *Sök enheter* (SUS) där varje partition och replik räknas som en su. 

Att använda färre SUs-resultat i en proportionellt lägre faktura. Faktureringen gäller så länge tjänsten har kon figurer ATS. Om du tillfälligt inte använder en tjänst är det enda sättet att undvika fakturering genom att ta bort tjänsten och sedan återskapa den när du behöver den.

> [!Note]
> När du tar bort en tjänst tas allt på den bort. Det finns ingen funktion i Azure Search för säkerhets kopiering och återställning av sparade Sök data. Om du vill distribuera om ett befintligt index i en ny tjänst bör du köra programmet som används för att skapa och läsa in det ursprungligen. 

## <a name="terminology-replicas-and-partitions"></a>Terminologi: repliker och partitioner
Repliker och partitioner är de primära resurser som återställer en Sök tjänst.

| Resource | Definition |
|----------|------------|
|*Partitioner* | Tillhandahåller index lagring och I/O för Läs-och skriv åtgärder (till exempel när du återskapar eller uppdaterar ett index).|
|*Repliker* | Instanserna av Sök tjänsten används främst för att belastningsutjämna frågor. Varje replik är alltid värd för en kopia av ett index. Om du har 12 repliker får du 12 kopior av varje index som har lästs in i tjänsten.|

> [!NOTE]
> Det finns inget sätt att direkt ändra eller hantera vilka index som körs på en replik. En kopia av varje-index på varje replik är en del av tjänst arkitekturen.
>


## <a name="how-to-allocate-replicas-and-partitions"></a>Så här allokerar du repliker och partitioner
I Azure Search allokeras en tjänst ursprungligen till en minimal resurs nivå som består av en partition och en replik. För nivåer som stöder det kan du stegvis justera beräknings resurser genom att öka partitionerna om du behöver mer lagrings utrymme och I/O, eller lägga till fler repliker för större volymer eller bättre prestanda. En enskild tjänst måste ha tillräckligt med resurser för att hantera alla arbets belastningar (indexering och frågor). Du kan inte dela upp arbets belastningar mellan flera tjänster.

Om du vill öka eller ändra tilldelningen av repliker och partitioner rekommenderar vi att du använder Azure Portal. Portalen tillämpar gränser på tillåtna kombinationer som ligger under max gränsen. Om du behöver en skript-baserad eller kod baserad etablerings metod är [Azure PowerShell](search-manage-powershell.md) eller [hanterings REST API](https://docs.microsoft.com/rest/api/searchmanagement/services) alternativa lösningar.

I allmänhet behöver Sök program fler repliker än partitioner, särskilt när tjänst åtgärderna prioriteras mot arbets belastningar. Avsnittet om [hög tillgänglighet](#HA) förklarar varför.

1. Logga in på [Azure Portal](https://portal.azure.com/) och välj Sök tjänsten.

2. I **Inställningar**öppnar du sidan **skala** för att ändra repliker och partitioner. 

   Följande skärm bild visar en standard tjänst som tillhandahålls med en replik och partition. Formeln längst ned anger hur många Sök enheter som används (1). Om enhets priset var $100 (inte ett verkligt pris) blir månads kostnaden för att köra tjänsten $100 i genomsnitt.

   ![Skala sidan visar aktuella värden](media/search-capacity-planning/1-initial-values.png "Skala sidan visar aktuella värden")

3. Använd skjutreglaget för att öka eller minska antalet partitioner. Formeln längst ned anger hur många Sök enheter som används.

   I det här exemplet dubbleras kapaciteten, med två repliker och partitioner. Observera antalet Sök enheter; nu är det fyra eftersom fakturerings formeln är repliker multiplicerade med partitioner (2 x 2). Dubbla kapaciteter är mer än dubbelt så mycket kostnaden för att köra tjänsten. Om priset för Sök enheten var $100 skulle den nya månads fakturan nu vara $400.

   För aktuella kostnader per enhet för varje nivå går du till [sidan med priser](https://azure.microsoft.com/pricing/details/search/).

   ![Lägg till repliker och partitioner](media/search-capacity-planning/2-add-2-each.png "Lägg till repliker och partitioner")

3. Klicka på **Spara** för att bekräfta ändringarna.

   ![Bekräfta ändringar av skalning och fakturering](media/search-capacity-planning/3-save-confirm.png "Bekräfta ändringar av skalning och fakturering")

   Ändringar i kapaciteten tar flera timmar att slutföra. Det går inte att avbryta när processen har startats och det inte finns någon real tids övervakning för replik-och partitions justeringar. Följande meddelande är dock fortfarande synligt när ändringarna pågår.

   ![Status meddelande i portalen](media/search-capacity-planning/4-updating.png "Status meddelande i portalen")


> [!NOTE]
> När en tjänst har allokerats kan den inte uppgraderas till en högre SKU. Du måste skapa en Sök tjänst på den nya nivån och läsa in index på nytt. Se [skapa en Azure Search-tjänst i portalen](search-create-service-portal.md) för hjälp med tjänst etablering.
>
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>Kombinationer av partition och repliker

En grundläggande tjänst kan ha exakt en partition och upp till tre repliker, för en gräns på tre SUs. Den enda justerbara resursen är repliker. Du behöver minst två repliker för att få hög tillgänglighet för frågor.

Alla standard-och Storage-optimerade Sök tjänster kan utgå från följande kombinationer av repliker och partitioner, beroende på 36-SU-gränsen. 

|   | **1 partition** | **2 partitioner** | **3 partitioner** | **4 partitioner** | **6 partitioner** | **12 partitioner** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 replik** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 repliker** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 repliker** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 repliker** |4 SU |8 SU |12 SU |16 SU |24 SU |Gäller inte |
| **5 repliker** |5 SU |10 SU |15 SU |20 SU |30 SU |Gäller inte |
| **6 repliker** |6 SU |12 SU |18 SU |24 SU |36 SU |Gäller inte |
| **12 repliker** |12 SU |24 SU |36 SU |Gäller inte |Saknas |Gäller inte |

SUs, priser och kapacitet beskrivs i detalj på Azure-webbplatsen. Mer information finns i [pris information](https://azure.microsoft.com/pricing/details/search/).

> [!NOTE]
> Antalet repliker och partitioner delas upp jämnt i 12 (särskilt, 1, 2, 3, 4, 6, 12). Detta beror på att Azure Search i förväg delar upp varje index i 12 Shards så att det kan spridas i lika stora delar av alla partitioner. Om din tjänst till exempel har tre partitioner och du skapar ett index, kommer varje partition att innehålla fyra Shards av indexet. Hur Azure Search Shards ett index är en implementerings information som kan komma att ändras i framtida versioner. Även om talet är 12 i dag, bör du inte vänta att antalet alltid är 12 i framtiden.
>


<a id="HA"></a>

## <a name="high-availability"></a>Hög tillgänglighet
Eftersom det är enkelt och relativt enkelt att skala upp rekommenderar vi vanligt vis att du börjar med en partition och en eller två repliker, och sedan skalar upp som fråge volymer skapas. Fråga arbets belastningar körs främst på repliker. Om du behöver mer data flöde eller hög tillgänglighet behöver du förmodligen ytterligare repliker.

Allmänna rekommendationer för hög tillgänglighet:

* Två repliker för hög tillgänglighet för skrivskyddade arbets belastningar (frågor)

* Tre eller flera repliker för hög tillgänglighet av Läs-och skriv arbets belastningar (frågor plus indexering när enskilda dokument läggs till, uppdateras eller tas bort)

Service avtal (SLA) för Azure Search riktas mot åtgärder och vid index uppdateringar som består av att lägga till, uppdatera eller ta bort dokument.

Basic-nivån ligger utanför en partition och tre repliker. Om du vill att flexibiliteten omedelbart ska svara på fluktuationer i behov för både indexering och frågans data flöde, bör du överväga en av standard nivåerna.  Om du upptäcker att dina lagrings krav ökar mycket snabbare än din fråga genom att använda en av de optimerade lagrings nivåerna.

### <a name="index-availability-during-a-rebuild"></a>Index tillgänglighet under en återskapning

Hög tillgänglighet för Azure Search gäller frågor och index uppdateringar som inte innebär att återskapa ett index. Om du tar bort ett fält, ändrar en datatyp eller byter namn på ett fält måste du återskapa indexet. För att återskapa indexet måste du ta bort indexet, återskapa indexet och läsa in data på nytt.

> [!NOTE]
> Du kan lägga till nya fält i ett Azure Search index utan att återskapa indexet. Värdet för det nya fältet kommer att vara null för alla dokument som redan finns i indexet.

Du måste ha en kopia av indexet med ett annat namn på samma tjänst, eller en kopia av indexet med samma namn på en annan tjänst, och sedan ange omdirigering eller växlings logik i din kod för att bevara indexets tillgänglighet under en ny version.

## <a name="disaster-recovery"></a>Haveriberedskap
För närvarande finns det ingen inbyggd mekanism för haveri beredskap. Att lägga till partitioner eller repliker skulle vara fel strategi för att uppfylla återställnings mål för haveri beredskap. Den vanligaste metoden är att lägga till redundans på service nivå genom att konfigurera en andra Sök tjänst i en annan region. Som med tillgänglighet under ett index återuppbyggnad måste omdirigeringen eller växlings logiken komma från din kod.

## <a name="increase-query-performance-with-replicas"></a>Öka prestanda för frågor med repliker
Svars tid är en indikator som ytterligare repliker behövs. Ett första steg för att förbättra prestanda för frågor är vanligt vis att lägga till mer av den här resursen. När du lägger till repliker, kommer ytterligare kopior av indexet att bli online för att stödja större arbets belastningar och belastningsutjämna begär anden över flera repliker.

Vi kan inte tillhandahålla hårda uppskattningar av frågor per sekund (frågor per sekund): fråga om prestanda beror på frågans komplexitet och konkurrerande arbets belastningar. Även om du lägger till repliker tydligt resulterar i bättre prestanda är resultatet inte strikt linjärt: om du lägger till tre repliker garanterar vi inte tredubbel data flöde.

Vägledning för att uppskatta frågor per sekund för dina arbets belastningar finns i [Azure Search prestanda-och optimerings överväganden](search-performance-optimization.md).

## <a name="increase-indexing-performance-with-partitions"></a>Öka indexerings prestanda med partitioner
Sök efter program som kräver data uppdatering i nära real tid måste ha proportionellt sett fler partitioner än repliker. Att lägga till partitioner sprider Läs-och skriv åtgärder över ett större antal beräknings resurser. Du får också mer disk utrymme för att lagra ytterligare index och dokument.

Större index tar längre tid att fråga. Därför kanske du upptäcker att varje stegvis ökning i partitioner kräver en mindre men proportionell ökning av repliker. Komplexiteten för dina frågor och frågesträngen kommer att kopplas till hur snabbt frågekörningen ska aktive ras runt.


## <a name="next-steps"></a>Nästa steg

[Välj en pris nivå för Azure Search](search-sku-tier.md)

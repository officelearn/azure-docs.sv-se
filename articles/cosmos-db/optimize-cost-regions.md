---
title: Optimera kostnaden för distributioner med flera regioner i Azure Cosmos DB
description: I den här artikeln beskrivs hur du hanterar kostnader för distributioner med flera regioner i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.openlocfilehash: e0a24b52c12bce6a8e016a926dfa64a1e36a7cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753309"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimera kostnaden för flera regioner i Azure Cosmos DB

Du kan lägga till och ta bort regioner i ditt Azure Cosmos-konto när som helst. Dataflödet som du konfigurerar för olika Azure Cosmos-databaser och behållare är reserverat i varje region som är associerad med ditt konto. Om dataflödet etableras per timme, det vill vara summan av RU/s som konfigurerats `T` i alla databaser och behållare för `N`ditt Azure Cosmos-konto är och antalet Azure-regioner som är associerade med ditt databaskonto är , är det totala etablerade dataflödet för ditt Cosmos-konto, för en viss timme lika med:

1. `T x N RU/s`om ditt Azure Cosmos-konto är konfigurerat med en enda skrivregion. 

1. `T x (N+1) RU/s`om ditt Azure Cosmos-konto är konfigurerat med alla regioner som kan bearbeta skrivningar. 

Ett etablerat dataflöde med en enda skrivningsregion kostar 0,008 USD/timme per 100 RU/s och ett etablerat dataflöde med flera skrivbara regioner kostar 0,016 USD/timme per 100 RU/s. Mer information finns på sidan Azure Cosmos DB [Pricing](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Kostnader för flera skrivregioner

I ett flerhuvudsystem ökar `N` de tillgängliga nätenheterna `N` för skrivåtgärder gånger där antalet skrivregioner är. Till skillnad från enstaka regionskrivningar är varje region nu skrivbar och bör stödja konfliktlösning. Mängden arbetsbelastning för författare har ökat. Från kostnadsplanering synvinkel, för `M` att utföra RU / s värde av skrivningar över hela världen, måste du etablera M `RUs` på en behållare eller databasnivå. Du kan sedan lägga till så många regioner som du `M` vill och använda dem för skrivningar för att utföra RU värde av världsomspännande skriver. 

### <a name="example"></a>Exempel

Tänk på att du har en behållare i västra USA som etablerats med dataflöde 10K RU /s och lagrar 1 TB data den här månaden. Anta att du lägger till tre regioner – östra USA, Norra Europa och Östasien, var och en med samma lagring och dataflöde och du vill att du ska skriva till behållarna i alla fyra regionerna från din globalt distribuerade app. Din totala månadsfaktura (förutsatt 31 dagar) i en månad är följande:

|**Objekt**|**Användning (månadsvis)**|**Pris**|**Månadskostnad**|
|----|----|----|----|
|Faktura för dataflöde för behållare i västra USA (flera skrivregioner) |10K RU/s * 24 * 31 |$0.016 per 100 RU/s per timme |1 190,40 $ |
|Bill för ytterligare 3 regioner - Östra USA, Nordeuropa och Östasien (flera skrivregioner) |(3 + 1) * 10K RU/s * 24 * 31 |$0.016 per 100 RU/s per timme |4 761,60 $ |
|Lagringsfaktura för container i västra USA |1 TB (eller 1 024 GB) |$0.25/GB |$256 |
|Lagringsfaktura för ytterligare 3 regioner - Östra USA, Nordeuropa och Östasien |3 * 1 TB (eller 3 072 GB) |$0.25/GB |$768 |
|**Totalt**|||**$6 976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Förbättra dataflödesanvändningen per region

Om du har ineffektiv användning, till exempel en eller flera underutnyttjade eller överutnyttjade regioner, kan du vidta följande åtgärder för att förbättra dataflödesanvändningen:  

1. Se till att du optimerar etablerat dataflöde (RU: er) i skrivregionen först, och sedan göra maximal användning av ru: erna i läsregioner med hjälp av ändringsflödet från läsregionen etc. 

2. Flera skrivregioner läser och skriver kan skalas över alla regioner som är associerade med Azure Cosmos-konto. 

3. Övervaka aktiviteten i dina regioner och du kan lägga till och ta bort regioner på begäran för att skala ditt läs- och skrivdataflöde.

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta att lära dig mer om kostnadsoptimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att förstå din Azure Cosmos DB-faktura](understand-your-bill.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)


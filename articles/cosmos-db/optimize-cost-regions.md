---
title: Optimera kostnader för distributioner över flera regioner i Azure Cosmos DB
description: Den här artikeln förklarar hur du hanterar kostnader för distributioner över flera regioner i Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 012eacb172acfdeb0b82343c484c664a3f75310e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876748"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimera kostnader för flera regioner i Azure Cosmos DB

Du kan lägga till och ta bort regioner till ditt Azure Cosmos-konto när som helst. Dataflödet som du konfigurerar för olika Azure-Cosmos-databaser och behållare är reserverad i varje region som är associerade med ditt konto. Om dataflödet etablerat per timme, som är summan av RU/s som konfigurerats på alla databaser och behållare för ditt Azure Cosmos-konto är `T` och antal Azure-regioner som associeras med ditt databaskonto är `N`, sedan det totala antalet dataflöde för ditt Cosmos-konto för en viss timme är lika med:

1. `T x N RU/s` Om ditt Azure Cosmos-konto har konfigurerats med en enda skrivregionen. 

1. `T x (N+1) RU/s` Om ditt Azure Cosmos-konto har konfigurerats med alla regioner som kan bearbeta skrivningar. 

Etablerat dataflöde med enkel skrivregionen kostar $0.008/ timme per 100 RU/s och etablerat dataflöde med flera skrivbara regioner kostar $0.016 / per timme per 100 RU/s. Mer information finns i Azure Cosmos DB [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Kostnaderna för flera Skriv-regioner

I ett system med flera huvudservrar kan skriva net tillgängliga antalet enheter för programbegäran för åtgärder ökar `N` gånger var `N` är antalet Skriv-regioner. Till skillnad från en enda region skrivningar varje region är nu skrivbar och ska ha stöd för konfliktlösning. Mängden arbetsbelastning för skrivare har ökat. Från kostnaden planera synvinkel, utföra `M` RU/s leverantör av skrivningar i hela världen, måste du etablera M `RUs` på en behållare eller databasen nivå. Du kan sedan lägga till så många regioner som du vill ha och använda dem för skrivningar för att utföra `M` RU leverantör i hela världen skrivningar. 

### <a name="example"></a>Exempel

Överväg att du har en behållare i västra USA etablerad med dataflödet 10 K RU/s och lagrar 1 TB data den här månaden. Vi antar att du lägger till tre regioner – östra USA, Nordeuropa och Östasien, var och en med samma lagringsutrymme och dataflöde och du vill kunna skriva till behållarna i alla fyra regioner från globalt distribuerade appen. Din totala månadsfaktura (förutsatt att 31 dagar) under en månad är följande:

|**Objekt**|**Användning (per månad)**|**Pris**|**Månadskostnad**|
|----|----|----|----|
|Dataflödesfaktura för behållare i västra USA (flera Skriv-regioner) |10 K RU/s * 24 * 31 |$0.016 per 100 RU/s per timme |$1,190.40 |
|Dataflödesfaktura för 3 ytterligare regioner – östra USA, Nordeuropa och Östasien (flera Skriv-regioner) |(3 + 1) * 10 K RU/s * 24 * 31 |$0.016 per 100 RU/s per timme |$4,761.60 |
|Lagringsfaktura för containrar i USA, västra |100 GB |$0.25/ GB |$25 |
|Lagringsfaktura för 3 ytterligare regioner – östra USA, Nordeuropa och Östasien |3 * 1 TB |$0.25/ GB |$75 |
|**Totalt**|||**$6,052** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Förbättra dataflödesutnyttjande på en per region-bas

Om du har ineffektiv användning, till exempel en eller flera outnyttjade eller används över regioner, du kan vidta följande steg för att förbättra dataflödesutnyttjande:  

1. Kontrollera att du optimera etablerat dataflöde (ru: er) i skrivregionen först och sedan utföra maximal användning av antalet enheter för programbegäran i läsregioner med ändringsflödet från Läs region osv. 

2. Flera Skriv-regioner läser och skriver kan skalas i alla regioner som är associerade med Azure Cosmos-konto. 

3. Övervaka aktivitet i din regioner och du kan lägga till och ta bort regioner på begäran för att skala din läsa och skriva dataflöde.

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta till mer information om kostnadsoptimeringar i Azure Cosmos DB i följande artiklar:

* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [förstå din faktura för Azure Cosmos DB](understand-your-bill.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)


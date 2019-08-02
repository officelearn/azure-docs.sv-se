---
title: Optimera kostnaderna för distributioner i flera regioner i Azure Cosmos DB
description: Den här artikeln förklarar hur du hanterar kostnader för distributioner i flera regioner i Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: rimman
ms.openlocfilehash: 233eab1fc49d7ce4cbb1e5b98b67eda9a64aa195
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68667600"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimera kostnader för flera regioner i Azure Cosmos DB

Du kan när som helst lägga till och ta bort regioner till ditt Azure Cosmos-konto. Det data flöde som du konfigurerar för olika Azure Cosmos-databaser och behållare är reserverade i varje region som är kopplad till ditt konto. Om det data flöde som har allokerats per timme, det vill säga summan av ru/s i alla databaser och behållare för ditt Azure Cosmos-konto `T` är och antalet Azure-regioner som är kopplade till ditt databas `N`konto, är det totala etablerade data flöden för ditt Cosmos-konto för en bestämd timme är lika med:

1. `T x N RU/s`Om ditt Azure Cosmos-konto har kon figurer ATS med en enda Skriv region. 

1. `T x (N+1) RU/s`Om ditt Azure Cosmos-konto har kon figurer ATS med alla regioner som kan bearbeta skrivningar. 

Allokerat data flöde med kostnader för enkel skrivnings region $0.008/timme per 100 RU/s och etablerade data flöde med flera skrivbara regioner kostar $0.016/per timme per 100 RU/s. Mer information finns på sidan med Azure Cosmos DB [prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Kostnader för flera Skriv regioner

I ett system med flera huvud servrar ökar `N` de tillgängliga ru: er för Skriv åtgärder de tider där `N` är antalet skriv regioner. Till skillnad från enskilda region skrivningar är varje region nu skrivbar och bör stödja konflikt lösning. Mängden arbets belastning för skrivare har ökat. Från kostnads planerings platsen för att utföra `M` ru/s-värd för skrivningar i hela världen måste du etablera M `RUs` på en container-eller databas nivå. Du kan sedan lägga till så många regioner som du vill och använda dem för skrivningar för att `M` utföra ru-värden i världs omspännande skrivningar. 

### <a name="example"></a>Exempel

Se till att du har en behållare i västra USA som tillhandahålls med data flöde 10 000 RU/s och lagrar 1 TB data den här månaden. Vi antar att du lägger till tre regioner – östra USA, norra Europa och Asien, östra, var och en med samma lagring och data flöde och du vill kunna skriva till behållarna i alla fyra regionerna från din globalt distribuerade app. Den totala månads fakturan (antar 31 dagar) är följande:

|**Objekt**|**Användning (månatlig)**|**Antal**|**Månatlig kostnad**|
|----|----|----|----|
|Data flödes faktura för container i USA, västra (flera Skriv regioner) |10 000 RU/s * 24 * 31 |$0,016 per 100 RU/s per timme |$1 190,40 |
|Data flödes faktura för 3 ytterligare regioner – östra USA, norra Europa och Asien, östra (flera Skriv regioner) |(3 + 1) * 10 000 RU/s * 24 * 31 |$0,016 per 100 RU/s per timme |$4 761,60 |
|Lagringsfaktura för containrar i USA, västra |1 TB (eller 1 024 GB) |$0,25/GB |$256 |
|Lagringsfaktura för 3 ytterligare regioner – östra USA, Nordeuropa och Östasien |3 * 1 TB (eller 3 072 GB) |$0,25/GB |$768 |
|**Totalt**|||**$6 976** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Förbättra data flödes användningen baserat på per region

Om du har ineffektiv användning, till exempel en eller flera underutnyttjade eller överutnyttjade regioner, kan du utföra följande steg för att förbättra data flödes användningen:  

1. Se till att optimera det etablerade data flödet (ru: er) i Skriv regionen först och gör sedan den maximala användningen av ru: er i Läs regioner genom att använda ändra feed från det skrivskyddade området osv. 

2. Läsningar och skrivningar för flera Skriv regioner kan skalas över alla regioner som är associerade med Azure Cosmos-kontot. 

3. Övervaka aktiviteten i dina regioner och du kan lägga till och ta bort regioner på begäran för att skala ditt Skriv-och skriv flöde.

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](optimize-cost-queries.md)


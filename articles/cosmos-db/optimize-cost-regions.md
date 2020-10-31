---
title: Optimera kostnaderna för distributioner i flera regioner i Azure Cosmos DB
description: Den här artikeln förklarar hur du hanterar kostnader för distributioner i flera regioner i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/23/2020
ms.openlocfilehash: 010ca40f4f3aacd6353aecd150e944672cc09066
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097523"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Optimera kostnaden för flera regioner i Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Du kan när som helst lägga till och ta bort regioner till ditt Azure Cosmos-konto. Det data flöde som du konfigurerar för olika Azure Cosmos-databaser och behållare är reserverade i varje region som är kopplad till ditt konto. Om det data flöde som har allokerats per timme, det vill säga summan av RU/s som kon figurer ATS över alla databaser och behållare för ditt Azure Cosmos-konto är `T` och antalet Azure-regioner som är associerade med ditt databas konto är `N` , är det totala etablerade data flödet för ditt Cosmos-konto för en viss timme lika med `T x N RU/s` .

Ett etablerat dataflöde med en enda skrivningsregion kostar 0,008 USD/timme per 100 RU/s och ett etablerat dataflöde med flera skrivbara regioner kostar 0,016 USD/timme per 100 RU/s. Mer information finns på sidan med Azure Cosmos DB [prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="costs-for-multiple-write-regions"></a>Kostnader för flera Skriv regioner

I ett system med flera regioner ökar de tillgängliga ru: er för Skriv åtgärder `N` när `N` är antalet skriv regioner. Till skillnad från enskilda region skrivningar är varje region nu skrivbar och har stöd för konflikt lösning. Från kostnads planerings platsen för att utföra `M` ru/s-värd för skrivningar i hela världen måste du etablera M `RUs` på en container-eller databas nivå. Du kan sedan lägga till så många regioner som du vill och använda dem för skrivningar för att utföra ru-värden `M` i världs omspännande skrivningar.

### <a name="example"></a>Exempel

Se till att du har en behållare i västra USA som kon figurer ATS för skrivningar i en region, som tillhandahålls med data flöde 10 000 RU/s och lagrar 1 TB data den här månaden. Vi antar att du lägger till en region, östra USA, med samma lagring och data flöde och du vill kunna skriva till behållarna i båda regionerna från din app. Den totala månads fakturan (antar 31 dagar) är följande:

|**Objekt**|**Användning (månatlig)**|**Hastighet**|**Månatlig kostnad**|
|----|----|----|----|
|Data flödes faktura för container i USA, västra (enkla Skriv regioner) |10 000 RU/s * 24 timmar * 31 dagar |$0,008 per 100 RU/s per timme |$584,06 |
|Data flödes faktura för container i 2 regioner – västra USA & USA, östra (flera Skriv regioner) |2 * 10 000 RU/s * 24 timmar * 31 dagar|$0,016 per 100 RU/s per timme |$2 336,26 |
|Lagrings faktura för behållare i västra USA |1 TB (eller 1 024 GB) |$0,25/GB |$256 |
|Lagrings faktura för 2 regioner – västra USA & USA, östra |2 * 1 TB (eller 3 072 GB) |$0,25/GB |$768 |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>Förbättra data flödes användningen baserat på per region

Om du har ineffektiv användning, till exempel en eller flera använda Läs regioner, kan du vidta åtgärder för att göra den maximala användningen av ru: er i Läs regioner genom att använda ändra feed från den skrivskyddade regionen eller flytta den till en annan sekundär om överutnyttjad. Du måste se till att optimera det etablerade data flödet (ru: er) i Skriv regionen först. Skriver kostnad mer än läsningar om inte mycket stora frågor så att du kan vara utmanande. Generellt, övervaka förbrukat data flöde i dina regioner och Lägg till eller ta bort regioner på begäran för att skala ditt Läs-och skriv flöde, så att du vet hur lång tid det tar för alla appar som distribueras i samma region.

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](./optimize-cost-reads-writes.md)
---
title: Prismodell för Azure Cosmos DB
description: I den här artikeln beskrivs prismodellen för Azure Cosmos DB och hur den förenklar kostnadshantering och kostnadsplanering.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: 7efae8fb3c00868e2740eac2d4d5bcb3c82f663a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75977544"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Prismodell i Azure Cosmos DB 

Prissättningsmodellen för Azure Cosmos DB gör det enkelt att planera och hantera dina kostnader. Med Azure Cosmos DB betalar du för det etablerade dataflödet och den lagring du förbrukar.

* **Etablerat dataflöde:** Etablerat dataflöde (kallas även reserverat dataflöde) garanterar hög prestanda i valfri skala. Du anger det dataflöde (RU/s) som du behöver och Azure Cosmos DB avsätter de resurser som krävs för att garantera det konfigurerade dataflödet. Du faktureras varje timme för maximalt etablerat dataflöde för en viss timme.

   > [!NOTE]
   > Eftersom den etablerade dataflödesmodellen avsätter resurser till din behållare eller databas debiteras du för det etablerade dataflödet även om du inte kör några arbetsbelastningar.

* **Förbrukad lagring:** Du faktureras ett schablonbelopp för den totala mängden lagringsutrymme (GB) som förbrukas för data och index för en viss timme.

Etablerat dataflöde, angivet som [begäranheter](request-units.md) per sekund (RU/s), gör att du kan läsa från eller skriva data till behållare eller databaser. Du kan [etablera dataflöde i antingen en databas eller en behållare](set-throughput.md). Baserat på dina arbetsbelastningsbehov kan du skala dataflödet uppåt/nedåt när som helst. Azure Cosmos DB-priser är elastisk och står i proportion till det dataflöde som du konfigurerar på en databas eller en behållare. De lägsta värdena för dataflöde och lagring och skalstegen ger ett komplett intervall av pris kontra elasticitetsspektrum till alla segment av kunder, från småskaliga till storskaliga behållare. Varje databas eller en behållare faktureras varje timme för det dataflöde som etablerats i enheterna på 100 RU/s, med minst 400 RU/s, och lagring förbrukas i GB. Till skillnad från etablerat dataflöde faktureras lagring på förbrukningsbasis. Det vill säga, du behöver inte boka någon lagring i förväg. Du faktureras endast för den lagring du förbrukar.

Mer information finns på prissidan för [Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/) och förstå din Azure [Cosmos DB-faktura](understand-your-bill.md).

Prismodellen i Azure Cosmos DB är konsekvent i alla API:er. Mer information finns i [Hur Azure Cosmos DB-prismodell är kostnadseffektiv för kunder](total-cost-ownership.md). Det finns ett minimum dataflöde som krävs på en databas eller en behållare för att säkerställa SLA och du kan öka eller minska etablerat dataflöde med $ 6 för varje 100 RU / s.

För närvarande är minimipriset för både databas och behållarbaserat dataflöde $24/månad (se [azure Cosmos DB-prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/) för den senaste informationen. Om arbetsbelastningen använder flera behållare kan den optimeras för kostnad med hjälp av dataflöde på databasnivå eftersom dataflödet på databasnivå gör att du kan ha valfritt antal behållare i en databas som delar dataflödet mellan behållarna. I följande tabell sammanfattas etablerat dataflöde och kostnader för olika entiteter:

|**Entitet**  | **Minsta & kostnad för dataflöde** |**Skala steg & kostnad** |**Etableringsomfång** |
|---------|---------|---------|-------|
|Databas    | 400 RU /s ($24/månad)    | 100 RU /s ($6/månad)   |Dataflöde är reserverat för databasen och delas av behållare i databasen |
|Container     | 400 RU /s ($24/månad)    | 100 RU /s ($6/månad)  |Dataflöde är reserverat för en specifik behållare |

Som visas i föregående tabell börjar det minsta dataflödet i Azure Cosmos DB till ett pris av $24/månad. Om du börjar med minsta dataflöde och skala upp över tiden för att stödja din produktion arbetsbelastningar, kommer dina kostnader stiga smidigt, i steg om $ 6/month. Prismodellen i Azure Cosmos DB är elastisk och priset ökar eller minskar smidigt när du skalar upp eller ned.

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt 

Azure Cosmos DB erbjuder flera alternativ för utvecklare till det gratis. Dessa alternativ är:

* **Kostnadsfritt Azure-konto**: Azure erbjuder en [kostnadsfri nivå](https://azure.microsoft.com/free/) som ger dig $200 i Azure-krediter för de första 30 dagarna och en begränsad mängd kostnadsfria tjänster i 12 månader. Mer information finns i [Kostnadsfritt Azure-konto](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB är en del av Azure gratis konto. Speciellt för Azure Cosmos DB erbjuder det här kostnadsfria kontot 5 GB lagring och 400 ru:er med etablerat dataflöde för hela året. 

* **Prova Azure Cosmos DB kostnadsfritt:** Azure Cosmos DB erbjuder en tidsbegränsad upplevelse genom att använda prova Azure Cosmos DB för kostnadsfria konton. Du kan skapa ett Azure Cosmos DB-konto, skapa databas och samlingar och köra ett exempelprogram med hjälp av snabbstarter och självstudier. Du kan köra exempelprogrammet utan att prenumerera på ett Azure-konto eller använda ditt kreditkort. [Prova Azure Cosmos DB gratis](https://azure.microsoft.com/try/cosmosdb/) erbjuder Azure Cosmos DB i en månad, med möjlighet att förnya ditt konto valfritt antal gånger.

* **Azure Cosmos DB-emulatorn**: Azure Cosmos DB-emulatorn tillhandahåller en lokal miljö som emulerar Azure Cosmos DB-tjänsten i utvecklingssyfte. Emulator erbjuds utan kostnad och med hög trohet till molntjänsten. Med Azure Cosmos DB-emulatorn kan du utveckla och testa dina program lokalt, utan att skapa en Azure-prenumeration eller ådra dig några kostnader. Du kan utveckla dina program genom att använda emulatorn lokalt innan du går i produktion. När du är nöjd med funktionerna i programmet mot emulatorn kan du växla till att använda Azure Cosmos DB-kontot i molnet och avsevärt spara på kostnaden. Mer information om emulator finns i [Använda Azure Cosmos DB för utveckling och testning](local-emulator.md) av artikel för mer information.

## <a name="pricing-with-reserved-capacity"></a>Prissättning med reserverad kapacitet

Azure Cosmos DB [reserverad kapacitet](cosmos-db-reserved-capacity.md) hjälper dig att spara pengar genom att betala för Azure Cosmos DB-resurser för antingen ett år eller tre år. Du kan avsevärt minska dina kostnader med ett år eller tre år i förskott åtaganden och spara mellan 20-65% rabatter jämfört med den ordinarie prissättningen. Azure Cosmos DB reserverad kapacitet hjälper dig att sänka kostnaderna genom att betala för det etablerade dataflödet (RU/s) under en period av ett år eller tre år och du får rabatt på det etablerade dataflödet. 

Reserverad kapacitet ger rabatt på fakturan, den påverkar inte körningsstatusen för dina Azure Cosmos DB-resurser. Reserverad kapacitet är konsekvent tillgänglig för alla API:er, vilket inkluderar MongoDB-, Cassandra-, SQL-, Gremlin- och Azure-tabeller och alla regioner över hela världen. Du kan läsa mer om reserverad kapacitet i [Prepay för Azure Cosmos DB-resurser med](cosmos-db-reserved-capacity.md) artikel med reserverad kapacitet och köpa reserverad kapacitet från [Azure-portalen](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

Du kan läsa mer om hur du optimerar kostnaderna för dina Azure Cosmos DB-resurser i följande artiklar:

* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att förstå din Azure Cosmos DB-faktura](understand-your-bill.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [att optimera kostnaden för Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Lär dig mer om [reserverad kapacitet för Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* Läs mer om [Azure Cosmos DB Emulator](local-emulator.md)

---
title: Prismodellen för Azure Cosmos DB
description: Den här artikeln förklarar prismodellen för Azure Cosmos DB och hur det förenklar kostnadshantering och planera för kostnaden.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: rimman
ms.openlocfilehash: 44c561386b00ca60dc537360145ea62177b6d5d6
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53263925"
---
# <a name="pricing-model-of-azure-cosmos-db"></a>Prismodellen för Azure Cosmos DB 

Prismodellen för Azure Cosmos DB förenklar kostnaden hantering och planering. Med Azure Cosmos DB betalar du för etablerat dataflöde och lagring du konsumerar.

* **Etablerat dataflöde**: Dataflöde (kallas även för reserverat dataflöde) garanterar hög prestanda i valfri skala. Du kan ange dataflöde (RU/s) att du behöver och Azure Cosmos DB dedikerar resurser som krävs för att garantera konfigurerade dataflödet. Du debiteras per timme för det högsta etablerade dataflödet för en viss timme.

   > [!NOTE]
   > Eftersom modellen etablerade dataflödet som dedikerar resurser till dina behållare eller databasen, debiteras du för det etablerade dataflödet även om du inte köra alla arbetsbelastningar.

* **Förbrukad lagring**: Du debiteras en fast avgift för den totala mängden lagringsutrymme (GB) för data och index för en viss timme.

Etablerat dataflöde som angetts som [programbegäran](request-units.md) per sekund (RU/s), kan du läsa från eller skriva data i behållare eller databaser. Du kan [etablera dataflöde på en databas eller en behållare](set-throughput.md). Utifrån dina arbetsbelastningsbehov kan skala du dataflöde upp/ned när som helst. Priser för Azure Cosmos DB är Elastiskt och är proportionell mot det dataflöde som du konfigurerar på en databas eller en behållare. Minsta värdena för dataflöde och lagring och skala steg ska du ange en fullständig uppsättning pris jämfört med elasticitet spektrumet till alla segment av kunder, från småskaliga för storskaliga behållare. Varje databas eller en behållare debiteras per timme för etablerat i enheter om 100 RU/s, med ett minimum på 400 RU/s, dataflöde och lagringsutrymme i GB. Till skillnad från etablerat dataflöde debiteras lagring på förbrukning. Det vill säga behöver du reservera all lagring i förväg. Du debiteras bara för den lagring som du förbrukar.

Mer information finns i den [Cosmos DB-sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) och [förstå din faktura för Cosmos DB](understand-your-bill.md).

Prismodellen i Azure Cosmos DB är konsekvent för alla API: er. Mer information finns i [hur Cosmos DB prismodellen är ett kostnadseffektivt alternativ för kunder](total-cost-ownership.md). Det finns ett minsta dataflöde som krävs på en databas eller en behållare för att säkerställa att serviceavtalen och du kan öka eller minska det etablerade dataflödet med $6 för varje 100 RU/s.

Det lägsta priset för både databasen och behållarbaserade dataflödet är för närvarande $24/ månad (se den [Cosmos DB-sidan med priser](https://azure.microsoft.com/pricing/details/cosmos-db/) senaste informationen. Om din arbetsbelastning använder flera behållare, kan den optimeras för kostnad genom att använda databasen på dataflöde eftersom databasen på dataflöde gör att du kan ha valfritt antal behållare i en Databasdelning dataflödet mellan behållarna. I följande tabell sammanfattas etablerat dataflöde och kostnaderna för olika enheter:

|**Entitet**  | **Minsta dataflöde & kostnad** |**Skala steg & d** |**Etablering omfång** |
|---------|---------|---------|-------|
|Databas    | 400 RU/s ($24/ månad)    | 100 RU/s ($6/ månad)   |Dataflödet är reserverad för databasen och delas av behållare i databasen |
|Container     | 400 RU/s ($24/ månad)    | 100 RU/s ($6/ månad)  |Dataflödet är reserverad för en specifik behållare |

I föregående tabell visas startar den lägsta dataflöden i Azure Cosmos DB för $24/ månad. Om du startar med minsta dataflöde och skala med tiden för dina produktionsarbetsbelastningar, öka kostnaderna smidigt, i steg om $6/ månad. Prismodellen i Azure Cosmos DB är Elastiskt och det finns en smidig ökning eller minskning av priset när du skalar upp eller ned.

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt 

Azure Cosmos DB erbjuder flera alternativ för utvecklare att det utan kostnad. Dessa alternativ är:

* **Kostnadsfritt Azure-konto**: Azure erbjuder en [kostnadsfria nivån](https://azure.microsoft.com/free/) som ger dig 200 USD i Azure-kredit för de första 30 dagarna och en begränsad mängd kostnadsfria tjänster i 12 månader. Mer information finns i [Kostnadsfritt Azure-konto](../billing/billing-avoid-charges-free-account.md). Azure Cosmos DB är en del av kostnadsfria Azure-konto. Mer specifikt för Azure Cosmos DB erbjuder det här kostnadsfria kontot 5 GB lagringsutrymme och 400 ru: er för etablerat dataflöde för hela året. 

* **Testa Azure Cosmos DB kostnadsfritt**: Azure Cosmos DB erbjuder en tidsbegränsad upplevelse med hjälp av prova Azure Cosmos DB för kostnadsfria konton. Du kan skapa ett Azure Cosmos DB-konto, skapa databas och samlingar och kör ett exempelprogram med hjälp av Snabbstarter och självstudier. Du kan köra exempelprogrammet utan att prenumerera på ett Azure-konto eller använda ditt kreditkort. [Testa Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) erbjuder Azure Cosmos DB för en månad, med möjlighet att förnya ditt konto valfritt antal gånger.

* **Azure Cosmos DB-emulatorn**: Azure Cosmos DB-emulatorn tillhandahåller en lokal miljö som emulerar Azure Cosmos DB-tjänsten för utvecklingsändamål. Emulatorn erbjuds utan kostnad och av hög kvalitet till Molntjänsten. Använder Azure Cosmos DB-emulatorn kan du utveckla och testa dina program lokalt, utan att skapa en Azure-prenumeration och utan kostnad. Du kan utveckla dina program med hjälp av emulatorn lokalt innan du påbörjar produktionen. När du är nöjd med funktioner i programmet mot emulatorn kan du växla till Azure Cosmos DB-konto i molnet och avsevärt spara på kostnaden. Läs mer om emulatorn [med hjälp av Azure Cosmos DB för utveckling och testning](local-emulator.md) nedan för mer information.

## <a name="pricing-with-reserved-capacity"></a>Priser med reserverad kapacitet

Azure Cosmos DB [reserverad kapacitet](cosmos-db-reserved-capacity.md) hjälper dig spara pengar genom att betala förväg för Azure Cosmos DB-resurserna för antingen ett eller tre år. Du kan avsevärt minska dina kostnader med ett eller tre år initiala åtaganden och spara mellan 20-65% rabatt jämfört med vanliga priser. Azure Cosmos DB reserverad kapacitet kan du sänka kostnaderna genom att betala för dataflöden (RU/s) före under en period av ett eller tre år och du får en rabatt på dataflödet som tillhandahållits. 

Reserverad kapacitet ger en rabatt på fakturering och påverkar inte runtime-tillståndet för dina Azure Cosmos DB-resurser. Reserverad kapacitet är tillgänglig konsekvent för alla API: er, bland annat MongoDB, Cassandra, SQL, Gremlin, och Azure-tabeller och alla regioner över hela världen. Du kan läsa mer om reserverad kapacitet i [i förskott för Azure Cosmos DB-resurser med reserverad kapacitet](cosmos-db-reserved-capacity.md) artikel och köpa reserverad kapacitet från den [Azure-portalen](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om hur du optimerar kostnaderna för dina Azure Cosmos DB-resurser i följande artiklar:

* Lär dig mer om [Optimera för utveckling och testning](optimize-dev-test.md)

* Läs mer om [förstå din faktura för Cosmos DB](understand-your-bill.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för frågor](optimize-cost-queries.md)
* Läs mer om [optimera kostnaden för flera regioner Cosmos-konton](optimize-cost-regions.md)
* Lär dig mer om [Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)
* Lär dig mer om [Cosmos DB-emulatorn](local-emulator.md)

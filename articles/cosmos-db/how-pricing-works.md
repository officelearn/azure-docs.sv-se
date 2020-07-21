---
title: Pris modell för Azure Cosmos DB
description: I den här artikeln beskrivs pris sättnings modellen för Azure Cosmos DB och hur det fören klar kostnads hanteringen och kostnads planeringen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/14/2020
ms.openlocfilehash: d36b4fd433af716ebd97d88d05922d94bd74c309
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2020
ms.locfileid: "86523544"
---
# <a name="pricing-model-in-azure-cosmos-db"></a>Prismodell i Azure Cosmos DB

Prissättningsmodellen för Azure Cosmos DB gör det enkelt att planera och hantera dina kostnader. Med Azure Cosmos DB betalar du för det etablerade dataflödet och den lagring du förbrukar.

* **Tillhandahållet data flöde**: ett [allokerat data flöde](how-to-choose-offer.md) (kallas även reserverat data flöde) garanterar höga prestanda i alla skalor. Du anger det data flöde (RU/s) som du behöver och Azure Cosmos DB dedikerar de resurser som krävs för att garantera det konfigurerade data flödet. Du faktureras per timme för maximalt tillhandahållet data flöde i en specifik timme. Du kan etablera data flöde manuellt eller använda [autoskalning](provision-throughput-autoscale.md).

   > [!NOTE]
   > Eftersom den etablerade data flödes modellen dedikerar resurser till din behållare eller databas debiteras du för det etablerade data flödet även om du inte kör några arbets belastningar.

* **Förbrukad lagring**: du debiteras ett fast pris för den totala mängden lagring (GB) som används för data och index för en specifik timme.

Etablerade data flöden, angivna som [begär ande enheter](request-units.md) per sekund (ru/s), gör att du kan läsa från eller skriva data i behållare eller databaser. Du kan [etablera data flöde för antingen en databas eller en behållare](set-throughput.md). Utifrån dina arbets belastnings behov kan du skala data flödet uppåt/nedåt när som helst. Azure Cosmos DB priser är elastiska och är proportionella mot det data flöde som du konfigurerar i en databas eller en behållare. Lägsta data flödes-och lagrings värden och skalnings ökningar ger en fullständig pris uppsättning jämfört med elastiskt spektrum till alla segment av kunder, från små skala till storskaliga behållare. Varje databas eller en behållare debiteras per timme för det data flöde som har allokerats i enheter om 100 RU/s, med minst 400 RU/s och lagring som förbrukas i GB. Till skillnad från etablerade data flöden debiteras lagrings utrymmet enligt förbrukningen. Det vill säga att du inte behöver reservera någon lagring i förväg. Du faktureras bara för den lagring som du använder.

Mer information finns på sidan med [Azure Cosmos DB priser](https://azure.microsoft.com/pricing/details/cosmos-db/) och [förstå din Azure Cosmos DB faktura](understand-your-bill.md).

Pris sättnings modellen i Azure Cosmos DB är konsekvent i alla API: er. Mer information finns i [How Azure Cosmos DB prissättnings modell kostnads effektivt för kunder](total-cost-ownership.md). Det finns ett minsta data flöde som krävs för en databas eller en behållare för att säkerställa service avtal och du kan öka eller minska det etablerade data flödet för varje 100 RU/s.

Om du distribuerar ditt Azure Cosmos DB-konto till en icke-myndighets region i USA, är för närvarande minimi priset för både databas och behållar baserat data flöde cirka $24 per månad. Prissättningen varierar beroende på vilken region du använder, se [sidan Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för den senaste pris informationen. Om din arbets belastning använder flera behållare kan den optimeras för kostnad med data flöde på databas nivå eftersom data flödet på databas nivå gör att du kan ha valfritt antal behållare i en databas som delar data flödet bland behållarna. I följande tabell sammanfattas etablerade data flöde och kostnaderna för olika entiteter:

|**Entitet**  | **Minsta data flöde** |**Skalnings steg** |**Etablerings omfång** |
|---------|---------|---------|-------|
|Databas    | 400 RU/s    | 100 RU/s   |Data flödet är reserverat för databasen och delas av behållare i databasen |
|Container     | 400 RU/s   | 100 RU/s  |Data flödet är reserverat för en angiven behållare |

Som det visas i föregående tabell börjar det lägsta data flödet i Azure Cosmos DB till ett pris på cirka $24 per månad. Om du börjar med det lägsta data flödet och skalar upp över tid för att stödja dina produktions arbets belastningar, kommer kostnaderna att öka smidigt, i steg om cirka $6/månad. Prissättningen varierar beroende på vilken region du använder, se [sidan Azure Cosmos DB prissättning](https://azure.microsoft.com/pricing/details/cosmos-db/) för den senaste pris informationen. Pris sättnings modellen i Azure Cosmos DB är elastisk och det finns en smidig ökning eller minskning av priset när du skalar upp eller ned.

## <a name="try-azure-cosmos-db-for-free"></a>Testa Azure Cosmos DB kostnadsfritt

Azure Cosmos DB erbjuder många alternativ för utvecklare utan kostnad. Dessa alternativ är:

* **Azure Cosmos DB kostnads fri nivå**: Azure Cosmos DB kostnads fri nivå är det enkelt att komma igång, utveckla och testa dina program, eller till och med köra små produktions arbets belastningar utan kostnad. När den kostnads fria nivån har Aktiver ATS för ett konto får du de första 400 RU/s och 5 GB lagrings utrymme i kontot kostnads fritt för kontots livstid. Du kan ha upp till ett konto med en kostnads fri nivå per Azure-prenumeration och måste välja när du skapar kontot. Kom igång genom att [skapa ett nytt konto i Azure Portal med kostnads fri nivå aktive rad](create-cosmosdb-resources-portal.md) eller använda en [arm-mall](manage-sql-with-resource-manager.md#free-tier).

* **Kostnads fritt Azure-konto**: Azure erbjuder en [kostnads fri nivå](https://azure.microsoft.com/free/) som ger dig $200 i Azure-krediter under de första 30 dagarna och en begränsad mängd kostnads fria tjänster i 12 månader. Mer information finns i [Kostnadsfritt Azure-konto](../cost-management-billing/manage/avoid-charges-free-account.md). Azure Cosmos DB är en del av ett kostnads fritt Azure-konto. Specifikt för Azure Cosmos DB erbjuder det här kostnads fria kontot 5 GB lagring och 400 RU/s av etablerade data flöden för hela året.

* **Prova Azure Cosmos DB kostnads fritt**: Azure Cosmos DB erbjuder en tidsbegränsad upplevelse genom att använda Try Azure Cosmos dB för kostnads fria konton. Du kan skapa ett Azure Cosmos DB konto, skapa databas och samlingar och köra ett exempel program med hjälp av snabb starter och självstudier. Du kan köra exempel programmet utan att prenumerera på ett Azure-konto eller använda ditt kredit kort. [Försök Azure Cosmos DB kostnads fria](https://azure.microsoft.com/try/cosmosdb/) erbjudanden Azure Cosmos db i en månad, med möjlighet att förnya ditt konto valfritt antal gånger.

* **Azure Cosmos DB emulator**: Azure Cosmos DB emulator är en lokal miljö som emulerar Azure Cosmos DBS tjänsten i utvecklings syfte. Emulatorn erbjuds utan kostnad och med hög kvalitet på moln tjänsten. Med hjälp av Azure Cosmos DB-emulatorn kan du utveckla och testa dina program lokalt, utan att skapa en Azure-prenumeration eller skapa några kostnader. Du kan utveckla dina program med hjälp av emulatorn lokalt innan du börjar arbeta med produktionen. När du är nöjd med programmets funktioner mot emulatorn kan du växla till att använda det Azure Cosmos DB kontot i molnet och markant Spara pengar. Mer information om emulator finns i [använda Azure Cosmos dB för utvecklings-och testnings](local-emulator.md) artiklar.

## <a name="pricing-with-reserved-capacity"></a>Prissättning med reserverad kapacitet

Azure Cosmos DB [reserverad kapacitet](cosmos-db-reserved-capacity.md) hjälper dig att spara pengar genom förskotts betalning för Azure Cosmos DB resurser i ett år eller tre år. Du kan avsevärt minska dina kostnader med ett års-eller tre års åtagande och spara mellan 20-65% rabatt jämfört med den vanliga prissättningen. Azure Cosmos DB reserverad kapacitet hjälper dig att sänka kostnaderna genom förskotts betalning för det etablerade data flödet (RU/s) under en period på ett år eller tre år och du får rabatt på det data flöde som du har tillhandahållit. 

Reserverad kapacitet ger rabatt på fakturan, den påverkar inte körningsstatusen för dina Azure Cosmos DB-resurser. Reserverad kapacitet är konsekvent för alla API: er, som innehåller MongoDB-, Cassandra-, SQL-, Gremlin-och Azure-tabeller och alla regioner över hela världen. Du kan lära dig mer om reserverad kapacitet i [förskott för Azure Cosmos DB resurser med reserverad kapacitet](cosmos-db-reserved-capacity.md) artikel och köpa reserverad kapacitet från [Azure Portal](https://portal.azure.com/).

## <a name="next-steps"></a>Nästa steg

Du kan lära dig mer om hur du optimerar kostnaderna för Azure Cosmos DB resurser i följande artiklar:

* Lär dig mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Lär dig mer om hur [du optimerar kostnaden för frågor](optimize-cost-queries.md)
* Lär dig mer om hur [du optimerar kostnaden för Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Lär dig mer om [Azure Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)
* Lär dig mer om [Azure Cosmos DB emulator](local-emulator.md)

---
title: Vanliga frågor om data flöde i Azure Cosmos DB autopilot-läge
description: Vanliga frågor och svar om autopilot-läge för Azure Cosmos DB databaser och behållare
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483315"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Vanliga frågor om tillhandahållet data flöde i Azure Cosmos DB autopilot-läge (för hands version)
Med autopilot-läge kommer Azure Cosmos DB automatiskt att hantera och skala RU/s för din behållare eller databas baserat på användning. Den här artikeln besvarar vanliga frågor om autopilot-läge. 

## <a name="frequently-asked-questions"></a>Vanliga frågor

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Stöds autopilot-läge för alla API: er?
Ja, autopilot-läget stöds för alla API: er: Core (SQL), Gremlin, Table, Cassandra och API för MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>Stöds autopilot-läge för flera huvud konton?
Ja, autopilot-läget stöds för flera huvud konton. Det högsta antalet RU/s är tillgängliga i varje region som läggs till i Cosmos-kontot. 

### <a name="what-is-the-pricing-for-autopilot"></a>Vad är prissättningen för autopilot?
Mer information finns på [sidan](https://azure.microsoft.com/pricing/details/cosmos-db/) med Azure Cosmos DB priser. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Hur gör jag för att aktivera autopilot för mina behållare eller databaser?
Autopilot-läge kan aktive ras på nya behållare och databaser som skapats med hjälp av Azure Portal. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Finns det CLI-eller SDK-stöd för att skapa behållare eller databaser med autopilot-läge?
I för hands versionen kan du för närvarande bara skapa resurser med autopilot-läget från Azure Portal. Stöd för CLI och SDK är inte tillgängligt ännu.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Kan jag aktivera autopilot på en befintlig behållare eller databas?
För närvarande kan du aktivera autopilot på nya behållare och databaser när du skapar dem. Stöd för att aktivera autopilot-läge på befintliga behållare och databaser är ännu inte tillgängligt. Du kan migrera befintliga behållare till en ny behållare med [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) eller [ändra feed](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Kan jag inaktivera autopilot-läget på en behållare eller databas?
Ja, du kan stänga av autopiloten genom att växla till alternativet Manuell för det etablerade data flödet. När du växlar från autopilot-läget till manuellt läge i för hands versionen kan du inte aktivera autopilot igen för samma resurs. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>Stöds autopilot-läge för delade data flödes databaser?
Ja, autopilot-läge stöds för delade data flödes databaser. Om du vill aktivera den här funktionen väljer du autopilot-läge och alternativet **etablera data flöde** när du skapar databasen. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Vad är antalet tillåtna samlingar per delad data flödes databas när autopilot har Aktiver ATS?
För delade data flödes databaser med autopilot-läge aktiverat är antalet tillåtna samlingar: MIN (25, Max RU/s databas/1000). Om till exempel det maximala data flödet i databasen är 20 000 RU/s kan databasen ha MIN (25, 20 000 RU/s/1000) = 20 samlingar. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Vad är lagrings gränsen kopplad till varje Max RU/s-alternativ?  
Lagrings gränsen i GB för varje högsta RU/s är: Max RU/s av databas eller container/100. Om t. ex. Max RU/s är 20 000 RU/s kan resursen stödja 200 GB lagrings utrymme. Mer information om tillgängliga Max RU/s-och lagrings alternativ finns i artikeln om [begränsningar för autopilot](provision-throughput-autopilot.md#autopilot-limits) . 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Vad händer om jag överskrider lagrings gränsen som är kopplad till mitt maximala data flöde?
Om lagrings gränsen som är kopplad till databasens eller behållarens maximala data flöde överskrids, ökar Azure Cosmos DB automatiskt det maximala data flödet till den näst högsta nivån som har stöd för den lagrings nivån. Anta till exempel att en databas eller behållare har tillhandahållits med alternativet 4000 RU/s max data flöde, som har en lagrings gräns på 50 GB. Om lagringen av resursen ökar till 100 GB ökas antalet RU/s i databasen eller containern automatiskt till 20 000 RU/s, som har stöd för upp till 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Hur snabbt kan autopilot skalas upp och ned baserat på toppar i trafik?
Autopiloten skalar omedelbart ned eller skalar ned RU/s inom det lägsta och högsta intervallet RU/s, baserat på inkommande trafik. Faktureringen görs med en precision på 1 timme, där du debiteras för flest RU/s inom en viss timme. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Kan jag ange ett anpassat Max värde för data flöde (RU/s) för autopilot-läge?
Under för hands versionen kan du för närvarande välja mellan [fyra alternativ](provision-throughput-autopilot.md#autopilot-limits) för maximalt data flöde (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Kan jag öka Max RU/s (flytta till en högre nivå) på databasen eller behållaren? 
Ja. Från alternativet **skala & inställningar** för din behållare eller **skalnings** alternativ för din databas, kan du välja en högre max ru/s för autopilot. Det här är en asynkron skalnings åtgärd som kan ta en stund att slutföra (vanligt vis 4-6 timmar, beroende på vilka RU/s som valts) som tjänsten tillhandahåller fler resurser för att stödja större skala. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Kan jag minska Max RU/s (flytta till en lägre nivå) på databasen eller behållaren?
Ja. Så länge den aktuella lagringen av databasen eller behållaren är under [lagrings gränsen](#what-is-the-storage-limit-associated-with-each-max-rus-option) som är kopplad till den högsta ru/s-nivå som du vill skala ned till, kan du minska Max ru/s till den nivån. Om du till exempel har valt 20 000 RU/s som Max RU/s kan du skala upp max RU/s till 4000 RU/s om du har mindre än 50 GB lagrings utrymme (lagrings gränsen som är kopplad till 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Vad är mappningen mellan de högsta RU/s och fysiska partitionerna?
Första gången du väljer Max RU/s är Azure Cosmos DB att etablera: Max RU/s/10 000 RU/s = antal fysiska partitioner. Varje [fysisk partition](partition-data.md#physical-partitions) har stöd för upp till 10 000 ru/s och 50 GB lagring. När lagrings storleken ökar, delar Azure Cosmos DB automatiskt partitionerna för att lägga till fler fysiska partitioner för att hantera lagrings ökningen eller öka den högsta nivån för RU/s om lagringen [överskrider den associerade gränsen](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Max RU/s för databasen eller containern är jämnt fördelad över alla fysiska partitioner. Det totala data flödet som en enskild fysisk partition kan skalas till är alltså: Max RU/s av databas eller behållare/# fysiska partitioner. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Vad händer om inkommande begär Anden överskrider max RU/s av databasen eller behållaren?
Om den totala förbrukade RU/s överskrider max RU/s av behållaren eller databasen, begränsas begär Anden som överstiger antalet RU/s och returnerar en status kod för 429. Begär Anden som resulterar i över 100% normaliserad användning kommer också att begränsas. Normaliserad användning definieras som Max för RU/s-användningen för alla fysiska partitioner. Anta till exempel att ditt maximala data flöde är 20 000 RU/s och att du har två fysiska partitioner, P_1 och P_2, som var och en kan skalas till 10 000 RU/s. Om P_1 har använt 6000 ru: er och P_2 8000 ru: er är den normaliserade användningen MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Azure Cosmos DB klient-SDK: er och data import verktyg (Azure Data Factory, Mass utförar-bibliotek) försöker automatiskt igen på 429s, så tillfälliga 429s är fina. Ett varaktigt stort antal 429s kan tyda på att du behöver öka Max RU/s eller granska din partitionerings strategi för en [aktiv partition](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Är det fortfarande möjligt att se 429s (begränsning/hastighets begränsning) när autopilot är aktiverat? 
Ja. Det är möjligt att se 429s i två scenarier. Först när den totala förbrukade RU/s överskrider max RU/s för containern eller databasen, kommer tjänsten att begränsa begär Anden enligt detta. 

För det andra, om det finns en aktiv partition, dvs. ett nyckel värde för logisk partition som har en oproportionerligt högre mängd begär Anden jämfört med andra nyckel värden, är det möjligt att den underliggande fysiska partitionen överskrider dess RU/s-budget. Som bästa praxis bör du [välja en bra partitionsnyckel](partitioning-overview.md#choose-partitionkey) som resulterar i en jämn fördelning av både lagring och data flöde. 

Om du till exempel väljer alternativet 20 000 RU/s max data flöde och har 200 GB lagrings utrymme, med fyra fysiska partitioner, kan varje fysisk partition skalas upp till 5000 RU/s. Om det fanns en hot partition på en viss logisk partitionsnyckel visas 429s när den underliggande fysiska partitionen som den finns på överskrider 5000 RU/s, dvs. överskrider 100% normaliserad användning.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [aktiverar autopilot på en Azure Cosmos-behållare eller-databas](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Lär dig mer om [fördelarna med autopilot](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Läs mer om [logiska och fysiska partitioner](partition-data.md).

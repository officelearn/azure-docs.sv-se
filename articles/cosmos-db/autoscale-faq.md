---
title: Vanliga frågor om autoskalning av allokerat data flöde i Azure Cosmos DB
description: Vanliga frågor om autoskalning av allokerat data flöde för Azure Cosmos DB databaser och behållare
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/28/2020
ms.openlocfilehash: fa4c2708f34a377a17914c7e6e5abdd709cbb5b1
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791790"
---
# <a name="frequently-asked-questions-about-autoscale-provisioned-throughput-in-azure-cosmos-db"></a>Vanliga frågor om autoskalning av allokerat data flöde i Azure Cosmos DB

Med autoskalning av allokerat data flöde kommer Azure Cosmos DB automatiskt att hantera och skala RU/s för din behållare eller databas baserat på användning. Den här artikeln besvarar vanliga frågor om autoskalning.

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="is-autoscale-supported-for-all-apis"></a>Stöds autoskalning för alla API: er?
Ja, autoskalning stöds för alla API: er: Core (SQL), Gremlin, Table, Cassandra och API för MongoDB.

### <a name="is-autoscale-supported-for-multi-master-accounts"></a>Stöds autoskalning för flera huvud konton?
Ja, autoskalning stöds för flera huvud konton. Det högsta antalet RU/s är tillgängliga i varje region som läggs till i Cosmos-kontot. 

### <a name="what-is-the-pricing-for-autoscale-"></a>Vad är prissättningen för autoskalning?
Mer information finns på [sidan](https://azure.microsoft.com/pricing/details/cosmos-db/) med Azure Cosmos DB priser. 

### <a name="how-do-i-enable-autoscale-for-my-containers-or-databases"></a>Hur gör jag för att aktivera autoskalning för mina behållare eller databaser?
Autoskalning kan aktive ras för nya behållare och databaser som skapats med hjälp av Azure Portal.

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autoscale-provisioned-throughput"></a>Finns det CLI-eller SDK-stöd för att skapa behållare eller databaser med autoskalning av allokerat data flöde?
För närvarande kan du bara skapa resurser med autoskalning av allokerat data flöde från Azure Portal. Stöd för CLI och SDK är inte tillgängligt ännu.

### <a name="can-i-enable-autoscale-on-an-existing-container-or-a-database"></a>Kan jag aktivera autoskalning på en befintlig behållare eller databas?
För närvarande kan du aktivera autoskalning på nya behållare och databaser när du skapar dem. Stöd för att aktivera autoskalning på befintliga behållare och databaser är ännu inte tillgängligt. Du kan migrera befintliga behållare till en ny behållare med [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) eller [ändra feed](change-feed.md). 

### <a name="can-i-turn-off-autoscale-on-a-container-or-database"></a>Kan jag inaktivera autoskalning på en behållare eller databas?
Ja, du kan inaktivera autoskalning genom att växla till alternativet Standard (manuell) för det etablerade data flödet. I den aktuella versionen kan du, efter att ha växlat från autoskalning till standard-allokerat data flöde, inte aktivera autoskalning igen för samma resurs. 

### <a name="is-autoscale-supported-for-shared-throughput-databases"></a>Stöds autoskalning för delade data flödes databaser?
Ja, autoskalning stöds för delade data flödes databaser. Om du vill aktivera den här funktionen väljer du autoskalning och alternativet **etablera data flöde** när du skapar databasen. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autoscale-is-enabled"></a>Vad är antalet tillåtna samlingar per delad data flödes databas när autoskalning är aktiverat?
För delade data flödes databaser med autoskalning aktiverat är antalet tillåtna samlingar: MIN (25, Max RU/s databas/1000). Om till exempel det maximala data flödet i databasen är 20 000 RU/s kan databasen ha MIN (25, 20 000 RU/s/1000) = 20 samlingar. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Vad är lagrings gränsen kopplad till varje Max RU/s-alternativ?  
Lagrings gränsen i GB för varje högsta RU/s är: Max RU/s av databas eller container/100. Om t. ex. Max RU/s är 20 000 RU/s kan resursen stödja 200 GB lagrings utrymme. Se artikeln om [begränsningar för autoskalning](provision-throughput-autoscale.md#autoscale-limits) för de tillgängliga Max ru/s-och lagrings alternativen. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Vad händer om jag överskrider lagrings gränsen som är kopplad till mitt maximala data flöde?
Om lagrings gränsen som är kopplad till databasens eller behållarens maximala data flöde överskrids, ökar Azure Cosmos DB automatiskt det maximala data flödet till den näst högsta nivån som har stöd för den lagrings nivån. Anta till exempel att en databas eller behållare har tillhandahållits med alternativet 4000 RU/s max data flöde, som har en lagrings gräns på 50 GB. Om lagringen av resursen ökar till 100 GB ökas antalet RU/s i databasen eller containern automatiskt till 20 000 RU/s, som har stöd för upp till 200 GB. 

### <a name="how-quickly-will-autoscale-up-and-down-based-on-spikes-in-traffic"></a>Hur snabbt skalas upp och ned automatiskt baserat på toppar i trafik?
Med autoskalning kan du omedelbart skala upp eller ned RU/s inom det lägsta och högsta intervallet RU/s, baserat på inkommande trafik. Faktureringen görs med en precision på 1 timme, där du debiteras för flest RU/s inom en viss timme.

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autoscale"></a>Kan jag ange ett anpassat Max värde för data flöde (RU/s) för autoskalning?
För närvarande kan du välja mellan [fyra alternativ](provision-throughput-autoscale.md#autoscale-limits) för maximalt data flöde (ru/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Kan jag öka Max RU/s (flytta till en högre nivå) på databasen eller behållaren? 
Ja. Från alternativet **skala & inställningar** för din behållare eller **skalnings** alternativ för din databas, kan du välja en högre max ru/s för autoskalning. Det här är en asynkron skalnings åtgärd som kan ta en stund att slutföra (vanligt vis 4-6 timmar, beroende på vilka RU/s som valts) som tjänsten tillhandahåller fler resurser för att stödja större skala. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Kan jag minska Max RU/s (flytta till en lägre nivå) på databasen eller behållaren?
Ja. Så länge den aktuella lagringen av databasen eller behållaren är under [lagrings gränsen](#what-is-the-storage-limit-associated-with-each-max-rus-option) som är kopplad till den högsta ru/s-nivå som du vill skala ned till, kan du minska Max ru/s till den nivån. Om du till exempel har valt 20 000 RU/s som Max RU/s kan du skala upp max RU/s till 4000 RU/s om du har mindre än 50 GB lagrings utrymme (lagrings gränsen som är kopplad till 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Vad är mappningen mellan de högsta RU/s och fysiska partitionerna?
Första gången du väljer Max RU/s är Azure Cosmos DB att etablera: Max RU/s/10 000 RU/s = antal fysiska partitioner. Varje [fysisk partition](partition-data.md#physical-partitions) har stöd för upp till 10 000 ru/s och 50 GB lagring. När lagrings storleken ökar, delar Azure Cosmos DB automatiskt partitionerna för att lägga till fler fysiska partitioner för att hantera lagrings ökningen eller öka den högsta nivån för RU/s om lagringen [överskrider den associerade gränsen](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Max RU/s för databasen eller containern är jämnt fördelad över alla fysiska partitioner. Det totala data flödet som en enskild fysisk partition kan skalas till är alltså: Max RU/s av databas eller behållare/# fysiska partitioner. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Vad händer om inkommande begär Anden överskrider max RU/s av databasen eller behållaren?
Om den totala förbrukade RU/s överskrider max RU/s av behållaren eller databasen, begränsas begär Anden som överstiger antalet RU/s och returnerar en status kod för 429. Begär Anden som resulterar i över 100% normaliserad användning kommer också att begränsas. Normaliserad användning definieras som Max för RU/s-användningen för alla fysiska partitioner. Anta till exempel att ditt maximala data flöde är 20 000 RU/s och att du har två fysiska partitioner, P_1 och P_2, som var och en kan skalas till 10 000 RU/s. Om P_1 har använt 6000 ru: er och P_2 8000 ru: er är den normaliserade användningen MAX (6000 RU/10 000 RU, 8000 RU/10 000 RU) = 0,8.

> [!NOTE]
> Azure Cosmos DB klient-SDK: er och data import verktyg (Azure Data Factory, Mass utförar-bibliotek) försöker automatiskt igen på 429s, så tillfälliga 429s är fina. Ett varaktigt stort antal 429s kan tyda på att du behöver öka Max RU/s eller granska din partitionerings strategi för en [aktiv partition](#autoscale-rate-limiting).

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autoscale-is-enabled"></a><a id="autoscale-rate-limiting"></a>Är det fortfarande möjligt att se 429s (begränsning/hastighets begränsning) när autoskalning är aktiverat? 
Ja. Det är möjligt att se 429s i två scenarier. Först när den totala förbrukade RU/s överskrider max RU/s för containern eller databasen, kommer tjänsten att begränsa begär Anden enligt detta. 

För det andra, om det finns en aktiv partition, dvs. ett nyckel värde för logisk partition som har en oproportionerligt högre mängd begär Anden jämfört med andra nyckel värden, är det möjligt att den underliggande fysiska partitionen överskrider dess RU/s-budget. Som bästa praxis bör du [välja en bra partitionsnyckel](partitioning-overview.md#choose-partitionkey) som resulterar i en jämn fördelning av både lagring och data flöde. 

Om du till exempel väljer alternativet 20 000 RU/s max data flöde och har 200 GB lagrings utrymme, med fyra fysiska partitioner, kan varje fysisk partition skalas upp till 5000 RU/s. Om det fanns en hot partition på en viss logisk partitionsnyckel visas 429s när den underliggande fysiska partitionen som den finns på överskrider 5000 RU/s, dvs. överskrider 100% normaliserad användning.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [aktiverar autoskalning på en Azure Cosmos-behållare eller-databas](provision-throughput-autoscale.md#create-db-container-autoscale).
* Lär dig mer om [fördelarna med etablerad data flöde med autoskalning ](provision-throughput-autoscale.md#autoscale-benefits).
* Läs mer om [logiska och fysiska partitioner](partition-data.md).

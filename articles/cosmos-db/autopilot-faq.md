---
title: Vanliga frågor och svar om dataflöde i Azure Cosmos DB-autopilotläge
description: Vanliga frågor och svar om autopilotläge för Azure Cosmos DB-databaser och behållare
author: deborahc
ms.author: dech
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/16/2019
ms.openlocfilehash: d2ad585cabefb9ea78b88e748e422c745c11d03f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75483315"
---
# <a name="frequently-asked-questions-about-provisioned-throughput-in-azure-cosmos-db-autopilot-mode-preview"></a>Vanliga frågor och svar om etablerat dataflöde i Azure Cosmos DB-autopilotläge (förhandsversion)
Med autopilotläge hanterar och skalar Azure Cosmos DB automatiskt RU/s i din behållare eller databas baserat på användning. Den här artikeln besvarar vanliga frågor om autopilotläge. 

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar

### <a name="is-autopilot-mode-supported-for-all-apis"></a>Stöds autopilotläget för alla API:er?
Ja, autopilotläge stöds för alla API:er: Core (SQL), Gremlin, Table, Cassandra och API för MongoDB.

### <a name="is-autopilot-mode-supported-for-multi-master-accounts"></a>Stöds autopilotläge för multi-master-konton?
Ja, autopilotläge stöds för multi-master-konton. Max RU/s är tillgängliga i varje region som läggs till i Cosmos-kontot. 

### <a name="what-is-the-pricing-for-autopilot"></a>Vad är prissättningen för autopilot?
Mer information finns på [prissidan](https://azure.microsoft.com/pricing/details/cosmos-db/) för Azure Cosmos DB. 

### <a name="how-do-i-enable-autopilot-for-my-containers-or-databases"></a>Hur aktiverar jag autopilot för mina behållare eller databaser?
Autopilotläge kan aktiveras på nya behållare och databaser som skapats med Azure-portalen. 

### <a name="is-there-cli-or-sdk-support-to-create-containers-or-databases-with-autopilot-mode"></a>Finns det CLI eller SDK stöd för att skapa behållare eller databaser med autopilotläge?
För närvarande kan du i förhandsversionen bara skapa resurser med autopilotläge från Azure-portalen. Stöd för CLI och SDK är ännu inte tillgängligt.

### <a name="can-i-enable-autopilot-on-an-existing-container-or-a-database"></a>Kan jag aktivera autopilot på en befintlig behållare eller en databas?
För närvarande kan du aktivera autopilot på nya behållare och databaser när du skapar dem. Stöd för att aktivera autopilotläge på befintliga behållare och databaser är ännu inte tillgängligt. Du kan migrera befintliga behållare till en ny behållare med [Azure Data Factory](../data-factory/connector-azure-cosmos-db.md) eller ändra [feed](change-feed.md). 

### <a name="can-i-turn-off-autopilot-mode-on-a-container-or-database"></a>Kan jag stänga av autopilotläget på en behållare eller databas?
Ja, du kan stänga av autopiloten genom att byta till alternativet "Manuell" för det etablerade dataflödet. I förhandsversionen, efter byte från autopilotläge till manuellt läge, kan du inte aktivera autopilot igen för samma resurs. 

### <a name="is-autopilot-mode-supported-for-shared-throughput-databases"></a>Stöds autopilotläge för databaser med delat dataflöde?
Ja, autopilotläge stöds för delade dataflödesdatabaser. Om du vill aktivera den här funktionen väljer du autopilotläge och alternativet **Etablera dataflöde** när du skapar databasen. 

### <a name="what-is-the-number-of-allowed-collections-per-shared-throughput-database-when-autopilot-is-enabled"></a>Vad är antalet tillåtna samlingar per databas med delat dataflöde när autopiloten är aktiverad?
För delade dataflödesdatabaser med autopilotläge aktiverat är antalet tillåtna samlingar: MIN(25, Max RU/s för databas / 1000). Om databasen till exempel är 20 000 RU/s kan databasen ha MIN(25, 20 000 RU/s / 1000) = 20 samlingar. 


### <a name="what-is-the-storage-limit-associated-with-each-max-rus-option"></a>Vad är lagringsgränsen kopplad till varje max RU/s-alternativ?  
Lagringsgränsen i S för varje max RU/s är: Max RU/s av databas eller behållare / 100. Om max RU/s till exempel är 20 000 RU/s kan resursen stödja 200 GB lagringsutrymme. Se [autopilotgränsartikeln](provision-throughput-autopilot.md#autopilot-limits) för tillgängliga max RU/s och lagringsalternativ. 

### <a name="what-happens-if-i-exceed-the-storage-limit-associated-with-my-max-throughput"></a>Vad händer om jag överskrider lagringsgränsen som är associerad med mitt maxdataflöde?
Om lagringsgränsen som är associerad med databasens eller behållarens maximala dataflöde överskrids, ökar Azure Cosmos DB automatiskt det maximala dataflödet till den näst högsta nivån som kan stödja den lagringsnivån. Anta till exempel att en databas eller behållare är etablerad med alternativet 4000 RU/s max dataflöde, som har en lagringsgräns på 50 GB. Om lagringen av resursen ökar till 100 GB ökas databasens eller behållarens max RU/s automatiskt till 20 000 RU/s, vilket kan stödja upp till 200 GB. 

### <a name="how-quickly-will-autopilot-scale-up-and-down-based-on-spikes-in-traffic"></a>Hur snabbt kommer autopilot skala upp och ner baserat på toppar i trafiken?
Autopiloten skalar omedelbart upp eller skalar ned RU/s inom det lägsta och högsta RU/s-området, baserat på inkommande trafik. Fakturering sker på en 1-timmars granularitet, där du debiteras för den högsta RU /s i en viss timme. 

### <a name="can-i-specify-a-custom-max-throughput-rus-value-for-autopilot-mode"></a>Kan jag ange ett anpassat maxflödesvärde (RU/s) för autopilotläge?
Under förhandsversionen kan du välja mellan [fyra alternativ](provision-throughput-autopilot.md#autopilot-limits) för maximalt dataflöde (RU/s).

### <a name="can-i-increase-the-max-rus-move-to-a-higher-tier-on-the-database-or-container"></a>Kan jag öka max RU /s (flytta till en högre nivå) på databasen eller behållaren? 
Ja. I alternativet **Skala & inställningar** för din behållare eller Alternativet **Skala** för databasen kan du välja en högre max RU/s för autopilot. Detta är en asynkron uppskalningsåtgärd som kan ta någon gång att slutföra (vanligtvis 4-6 timmar, beroende på vilka RU/s som valts) eftersom tjänsten avser fler resurser för att stödja den högre skalan. 

### <a name="can-i-reduce-the-max-rus-move-to-a-lower-tier-on-the-database-or-container"></a>Kan jag minska max RU /s (flytta till en lägre nivå) på databasen eller behållaren?
Ja. Så länge den aktuella lagringen av databasen eller behållaren ligger under [lagringsgränsen](#what-is-the-storage-limit-associated-with-each-max-rus-option) som är associerad med den max RU/s-nivå som du vill skala ned till, kan du minska max RU/s till den nivån. Om du till exempel har valt 20 000 RU/s som max RU/s kan du skala ned max RU/s till 4000 RU/s om du har mindre än 50 GB lagringsutrymme (lagringsgränsen i samband med 4000 RU/s).

### <a name="what-is-the-mapping-between-the-max-rus-and-physical-partitions"></a>Vad är mappningen mellan max RU/s och fysiska partitioner?
När du först väljer max RU/s etablerar Azure Cosmos DB: Max RU/s / 10 000 RU/s = # för fysiska partitioner. Varje [fysisk partition](partition-data.md#physical-partitions) kan stödja upp till 10 000 RU/s och 50 GB lagringsutrymme. När lagringsstorleken växer delar Azure Cosmos DB automatiskt upp partitionerna för att lägga till fler fysiska partitioner för att hantera lagringsökningen, eller öka den maximala RU/s-nivån om [lagringen överskrider den associerade gränsen](#what-is-the-storage-limit-associated-with-each-max-rus-option). 

Databasens eller behållarens Max RU/s är jämnt fördelat över alla fysiska partitioner. Så, det totala dataflödet en enskild fysisk partition kan skala till är: Max RU / s av databas eller behållare / # fysiska partitioner. 

### <a name="what-happens-if-incoming-requests-exceed-the-max-rus-of-the-database-or-container"></a>Vad händer om inkommande begäranden överskrider databasens eller behållarens max RU/s?
Om den totala förbrukade RU/s överskrider behållarens eller databasens max RU/s, begränsas begäranden som överskrider max RU/s och returnerar en statuskod på 429. Begäranden som resulterar i över 100 % normaliserad användning begränsas också. Normaliserad användning definieras som max för RU/s-användningen för alla fysiska partitioner. Anta till exempel att maxflödet är 20 000 RU/s och du har två fysiska partitioner, P_1 och P_2, som var och en kan skala till 10 000 RU/s. I en viss sekund, om P_1 har använt 6000 RU, och P_2 8000 RU, är det normaliserade utnyttjandet MAX(6000 RU / 10.000 RU, 8000 RU / 10.000 RU) = 0,8.

> [!NOTE]
> Azure Cosmos DB-klientenSdK:er och dataimportverktyg (Azure Data Factory, bulk executor library) försöker automatiskt på 429:or, så enstaka 429:or är bra. Ett ihållande stort antal 429s kan tyda på att du behöver öka max RU /s eller granska din partitioneringsstrategi för en [het partition.](#is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled)

### <a name="is-it-still-possible-to-see-429s-throttlingrate-limiting-when-autopilot-is-enabled"></a>Är det fortfarande möjligt att se 429s (begränsning / hastighetsbegränsning) när autopilot är aktiverat? 
Ja. Det är möjligt att se 429s i två scenarier. För det första, när den totala förbrukade RU/s överstiger behållarens eller databasens max RU/s, kommer tjänsten att begränsa begäranden i enlighet med detta. 

För det andra, om det finns en het partition, dvs ett logiskt partitionsnyckelvärde som har en oproportionerligt högre mängd begäranden jämfört med andra partitionsnyckelvärden, är det möjligt för den underliggande fysiska partitionen att överskrida sin RU/s budget. Om du vill undvika heta partitioner bör du välja [en bra partitionsnyckel](partitioning-overview.md#choose-partitionkey) som resulterar i en jämn fördelning av både lagring och dataflöde. 

Om du till exempel väljer alternativet 20 000 RU/s maxdataflöde och har 200 GB lagringsutrymme, med fyra fysiska partitioner, kan varje fysisk partition skalas upp till 5000 RU/s. Om det fanns en het partition på en viss logisk partitionsnyckel ser du 429:er när den underliggande fysiska partitionen som den finns i överstiger 5000 RU/s, dvs.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [aktiverar autopilot på en Azure Cosmos-behållare eller databas](provision-throughput-autopilot.md#create-a-database-or-a-container-with-autopilot-mode).
* Läs mer om [fördelarna med autopilot](provision-throughput-autopilot.md#benefits-of-autopilot-mode).
* Läs mer om [logiska och fysiska partitioner](partition-data.md).

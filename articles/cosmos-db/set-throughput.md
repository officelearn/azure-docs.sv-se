---
title: Etablera dataflöde för Azure Cosmos DB
description: Lär dig hur du ställer in dataflöde för Azure Cosmos DB-behållare och databaser.
author: aliuy
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: andrl
ms.openlocfilehash: a97032344b904442ed3606c6297251578c3b4ff7
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2018
ms.locfileid: "52263901"
---
# <a name="provision-throughput-on-azure-cosmos-containers-and-databases"></a>Etablera dataflöde för Azure Cosmos-behållare och databaser

En Azure Cosmos-databas är en enhet för hantering för en uppsättning behållare. En databas består av en uppsättning schemaoberoende behållare. En Azure Cosmos-behållare är Faktureringsenhet skalbarhet för både dataflöde och lagring. En behållare är horisontellt partitionerade över en uppsättning datorer i en Azure-region och distribueras över alla Azure-regioner som är associerade med ditt Azure Cosmos-konto.

Azure Cosmos DB kan du konfigurera dataflöde på två Precision - **Azure Cosmos-behållare** och **Azure Cosmos-databaser**.

# <a name="setting-throughput-on-a-azure-cosmos-container"></a>Ange dataflöde för en Azure Cosmos-behållare  

Dataflödet som tillhandahållits för en Azure Cosmos-behållare enbart för behållaren. Behållaren tar emot det etablerade dataflödet som hela tiden. Dataflöde i en behållare har inget serviceavtal med ekonomisk uppbackning av serviceavtal. För att konfigurera dataflödet för en behållare, se [hur du etablera dataflöde för en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

Ange dataflöde för en behållare är det vanliga alternativet. Medan du kan Elastiskt skala dataflöde för en behållare genom att etablera valfritt antal dataflöde (ru: er), kan du selektivt ange genomströmning för logiska partitioner. När de arbetsbelastningar som körs på en logisk partition förbrukar mer än det dataflöde som tilldelades till den specifika logisk partitionen, får din verksamhet rate-limited. När hastighetsbegränsande uppstår kan du antingen öka genomflödet för hela behållaren eller försök igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Vi rekommenderar att du konfigurerar dataflöde med behållare precision när du vill att garanterade prestanda för behållaren.

Etablerat en Azure Cosmos-behållaren dataflöde är jämnt fördelade för alla logiska partitioner i behållaren. Eftersom en eller flera logiska partitioner för en behållare är värd för en resurspartition, de fysiska partitionerna tillhör exklusivt behållaren och stöd för dataflödet som tillhandahållits för behållaren. Följande bild visar hur en resurspartition är värd för en eller flera logiska partitioner för en behållare:

![Resurspartition](./media/set-throughput/resource-partition.png)

# <a name="setting-throughput-on-a-azure-cosmos-database"></a>Ställa in dataflöde på en Azure Cosmos-databas

När du etablerar dataflöde på en Azure Cosmos-databas kan delas dataflödet mellan alla behållare i databasen, såvida inte du har angett ett dataflöde på specifika behållare. Dela databasen dataflödet mellan dess behållare är detsamma som värd för en databas på ett kluster med datorer. Eftersom alla behållare i en databas delar resurserna som är tillgängliga på en dator, naturligt ger inte förutsägbar prestanda på en specifik behållare. För att konfigurera dataflöde på en databas, se [så här konfigurerar du etablerat dataflöde på en Azure Cosmos-databas](how-to-provision-database-throughput.md).

Inställningen dataflöde på en Azure Cosmos-databas garanterar att du får det etablerade dataflödet som hela tiden. Eftersom alla behållare på databas-resursen det etablerade dataflödet tillhandahåller Azure Cosmos DB alla förutsägbart dataflöde garantier för en viss behållare i databasen. Del av vilket dataflöde som kan ta emot en viss behållare är beroende av:

* Antal behållare
* Valet av partitionsnycklar för olika behållare och
* Distribution av arbetsbelastning i olika logiska partitioner av behållarna. 

Vi rekommenderar att du konfigurerar dataflödet för en databas när du vill dela dataflödet över flera behållare, men inte vill att dedikera dataflödet till en viss behållare. Här följer några exempel där det är att föredra att etablera dataflöde på databasnivå:

* Dela dataflöde för en databas i en behållare är användbart för ett program med flera innehavare. Varje användare kan representeras av en distinkt Azure Cosmos-behållare.

* Dela dataflöde för en databas i en behållare är användbart när du migrerar en NoSQL-databas (till exempel MongoDB, Cassandra) som finns från ett kluster av virtuella datorer eller från lokala fysiska servrar till Azure Cosmos DB. Du kan föreställa dig det etablerade dataflödet som konfigurerats på din Azure Cosmos-databas som en logiska motsvarigheten (men mer kostnadseffektiv och elastiska) med beräkningskapaciteten för din MongoDB- eller Cassandra-kluster.  

Vid en given tidpunkt tid fördelas det dataflöde som allokeras till en behållare i en databas för alla logiska partitioner för behållaren. När du har en behållare som delar etablerat dataflöde på en databas, kan du selektivt använder dataflödet till en specifik behållare eller en logisk partition. Om arbetsbelastningen för en logisk partition förbrukar mer än det dataflöde som tilldelas en specifik logisk partition, kommer din verksamhet att rate-limited. När hastighetsbegränsande uppstår kan du antingen öka genomflödet för hela behållaren eller försök igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Flera logiska partitioner som delar dataflödet som tillhandahållits till en databas kan finnas på en enskild resurs-partition. När en enskild logisk partition för en behållare är alltid begränsade inom en resurspartition, ”L” logiska partitioner mellan ”C”-behållare som delar det etablerade dataflödet för en databas mappade och finns i ”R” fysiska partitioner. Följande bild visar hur en resurspartition kan vara värd för en eller flera logiska partitioner som hör till olika behållare i en databas:

![Resurspartition](./media/set-throughput/resource-partition2.png)

## <a name="setting-throughput-on-a-azure-cosmos-database-and-a-container"></a>Ange dataflöde för en Azure Cosmos-databas och en behållare

Du kan kombinera de två modellerna, tillåts etablera dataflöde på både databasen och behållaren. I följande exempel visar hur du etablera dataflöde för en Azure Cosmos-databas och en behållare:

* Du kan skapa en Azure Cosmos-databas med namnet ”Z” med etablerat dataflöde på ”K” ru: er. 
* Därefter skapar fem behållare med namnet A, B, C, D och E i databasen.
* Du kan uttryckligen konfigurera ”P” ru: er för etablerat dataflöde för behållaren ”B”.
* Dataflödet ”K” ru: er delas mellan de fyra behållarna – A, C, D och E. Den exakta mängden dataflöde tillgängliga till A, C, D eller E varierar och det finns inget serviceavtal för dataflöden i varje behållare.
* Behållaren ”B” garanteras att hämta dataflödet ”P” ru: er hela tiden och den understöds av serviceavtal.

## <a name="comparison-of-models"></a>Jämförelse av modeller

|**Kvot**  |**Dataflödet som etablerats på en databas**  |**Dataflödet som etableras i en behållare**|
|---------|---------|---------|
|Minsta ru: er |400 |400|
|Minsta ru: er per behållare|100|400|
|Minsta ru: er som krävs för att använda 1 GB lagringsutrymme|40|40|
|Maximal ru: er|Obegränsade för databasen|Obegränsade behållare|
|RU: er tilldelade/tillgänglig för en specifik behållare|Inga garantier. RU: er som har tilldelats en viss behållare beror på egenskaperna, till exempel - val av partitionsnycklar behållare som delar dataflöde, distribution av arbetsbelastning, antal behållare. |Alla ru: er som har konfigurerats på behållaren är enbart för behållaren.|
|Maximalt lagringsutrymme för en behållare|Obegränsat|Obegränsat|
|Högsta dataflöde per logisk partition för en behållare|10K ru: er|10K ru: er|
|Maximalt lagringsutrymme (data + index) per logisk partition för en behållare|10 GB|10 GB|

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md)
* Lär dig [hur du etablera dataflöde för en Azure Cosmos-behållare](how-to-provision-container-throughput.md)
* Lär dig [hur du etablera dataflöde för en Azure Cosmos-databas](how-to-provision-database-throughput.md)


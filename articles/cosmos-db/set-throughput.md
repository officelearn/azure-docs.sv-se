---
title: Etablera dataflöde för Azure Cosmos-behållare och databaser
description: Lär dig hur du ställer in dataflöde för Azure Cosmos-behållare och databaser.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/31/2019
ms.author: rimman
ms.openlocfilehash: 1f8bec6fbf0bce9a3ac272231058a96a5d9e84cc
ms.sourcegitcommit: 09bb15a76ceaad58517c8fa3b53e1d8fec5f3db7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58762505"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Etablera dataflöde på containrar och databaser

En Azure Cosmos-databas är en enhet för hantering för en uppsättning behållare. En databas består av en uppsättning schemaoberoende behållare. En Azure Cosmos-behållare är Faktureringsenhet skalbarhet för både dataflöde och lagring. En behållare är horisontellt partitionerade över en uppsättning datorer i en Azure-region och distribueras över alla Azure-regioner som är associerade med ditt Azure Cosmos-konto.

Du kan etablera dataflöde på två Precision med Azure Cosmos DB:
 
- Azure Cosmos-behållare
- Azure Cosmos-databaser

## <a name="set-throughput-on-a-container"></a>Ange dataflöde för en behållare  

Dataflödet som tillhandahållits i ett Azure Cosmos-behållare enbart för den behållaren. Behållaren tar emot det etablerade dataflödet som hela tiden. Dataflöde i en behållare har inget serviceavtal med ekonomisk uppbackning av serviceavtal. Läs hur du konfigurerar dataflöde på en behållare i [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

Inställningen dataflöde i en behållare är det vanligaste alternativet. Du kan Elastiskt skala dataflöde för en behållare genom att etablera valfritt antal dataflöde med hjälp av [programbegäran (ru)](request-units.md). 

Dataflöde som etablerats på en Azure Cosmos-behållare är jämnt fördelade över alla logiska partitioner i behållaren. Du kan inte selektivt ange genomströmning för logiska partitioner. Eftersom en eller flera logiska partitioner för en behållare är värd för en fysisk partition, de fysiska partitionerna tillhör exklusivt behållaren och stöd för dataflödet som tillhandahållits för behållaren. 

Om arbetsbelastningen som körs på en logisk partition förbrukar mer än det dataflöde som tilldelades till den logisk partitionen, få din verksamhet rate-limited. När hastighetsbegränsande uppstår kan du antingen öka det etablerade dataflödet för hela behållaren eller försök igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Vi rekommenderar att du konfigurerar dataflöde med behållare precision när du vill att garanterade prestanda för behållaren.

Följande bild visar hur en fysisk partition är värd för en eller flera logiska partitioner för en behållare:

![Fysisk partition](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ange dataflöde på en databas

När du etablerar dataflöde i en Azure Cosmos-databas kan delas dataflödet mellan alla behållare i databasen. Ett undantag är om du har angett ett dataflöde på specifika behållare i databasen. Dela det etablerade dataflödet på databasnivå mellan dess behållare är detsamma som värd för en databas på ett kluster med datorer. Eftersom alla behållare i en databas delar resurserna som är tillgängliga på en dator, naturligt ger inte förutsägbar prestanda på en specifik behållare. Läs hur du konfigurerar etablerat dataflöde på en databas i [konfigurera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

Inställningen dataflöde i en Azure Cosmos-databas garanterar att du får det etablerade dataflödet för den här databasen hela tiden. Eftersom alla behållare i databasen delar det etablerade dataflödet, ger Azure Cosmos DB inte någon förutsägbart dataflöde garantier för en viss behållare i databasen. Del av vilket dataflöde som kan ta emot en viss behållare är beroende av:

* Antal behållare.
* Val av partitionsnycklar för olika behållare.
* Distribution av arbetsbelastning i olika logiska partitioner av behållarna. 

Vi rekommenderar att du konfigurerar dataflödet för en databas när du vill dela dataflödet över flera behållare, men inte vill att dedikera dataflödet till en viss behållare. 

Följande exempel visar där det har lämpligt att etablera dataflöde på databasnivå:

* Det är användbart för ett program för flera delar dataflöde för en databas på en uppsättning behållare. Varje användare kan representeras av en distinkt Azure Cosmos-behållare.

* Dela dataflöde för en databas i en behållare är användbart när du migrerar en NoSQL-databas, t.ex MongoDB eller Cassandra, finns i ett kluster av virtuella datorer eller från lokala fysiska servrar till Azure Cosmos DB. Tänk på det etablerade dataflödet konfigurerade på din Azure Cosmos-databas som logisk motsvarande, men mer kostnadseffektiv och elastiska med beräkningskapaciteten för din MongoDB- eller Cassandra-kluster.  

Alla behållare som skapas i en databas med etablerat dataflöde måste skapas med en [partitionsnyckel](partition-data.md). Vid en given tidpunkt tid fördelas det dataflöde som allokeras till en behållare i en databas för alla logiska partitioner för behållaren. När du har en behållare som delar etablerade dataflödet som konfigurerats på en databas, kan du selektivt använder dataflödet till en specifik behållare eller en logisk partition. 

Om arbetsbelastningen för en logisk partition förbrukar mer än det dataflöde som tilldelas en specifik logisk partition, är dina åtgärder rate-limited. När hastighetsbegränsande uppstår kan du antingen öka genomflödet för hela databasen eller försök igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Flera logiska partitioner som hör till olika behållare som delar dataflödet som tillhandahållits till en databas kan finnas på en enda fysisk partition. När en enskild logisk partition för en behållare är alltid begränsade inom en fysisk partition *”L”* logiska partitioner över *”C”* behållare som delar det etablerade dataflödet för en databas kan vara mappad och finns på *”R”* fysiska partitioner. 

Följande bild visar hur en fysisk partition kan vara värd för en eller flera logiska partitioner som hör till olika behållare i en databas:

![Fysisk partition](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ange dataflöde på en databas och en behållare

Du kan kombinera de två modellerna. Etablering dataflöde på både databasen och behållaren tillåts. I följande exempel visar hur du etablera dataflöde för en Azure Cosmos-databas och en behållare:

* Du kan skapa ett Azure Cosmos-databas med namnet *Z* med etablerat dataflöde på *”K”* ru: er. 
* Därefter skapar fem behållare med namnet *A*, *B*, *C*, *D*, och *E* i databasen.
* Du kan uttryckligen konfigurera *”P”* ru: er för dataflöde i behållaren med namnet *B*.
* Den *”K”* RUs dataflöde delas mellan de fyra behållarna *A*, *C*, *D*, och *E*. Den exakta mängden dataflödet är tillgängligt för *A*, *C*, *D*, eller *E* varierar. Det finns inga serviceavtal för dataflöden i varje behållare.
* Behållare med namnet *B* garanteras att hämta den *”P”* RUs dataflöde hela tiden. Den understöds av serviceavtal.

## <a name="update-throughput-on-a-database-or-a-container"></a>Uppdatera dataflöde på en databas eller en behållare

När du har skapat en Azure Cosmos-behållare eller en databas kan du uppdatera det etablerade dataflödet. Det finns ingen gräns för det högsta etablerade dataflödet som du kan konfigurera i databasen eller behållaren. Det minsta etablerade dataflödet beror på följande faktorer: 

* Den maximala Datastorleken som du lagrar någonsin i behållaren
* Den största möjliga dataflöde som du etablerar någonsin på behållaren
* Det maximala antalet Azure Cosmos-behållare som du vilja skapa i en databas med delade dataflöde. 

Du kan hämta den minsta genomströmningen i en behållare eller en databas via programmering med hjälp av SDK: erna eller visa värdet i Azure-portalen. När du använder .NET SDK, den [DocumentClient.ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) metod kan du skala dataflöde värdet. När du använder Java-SDK i [RequestOptions.setOfferThroughput](sql-api-java-samples.md#offer-examples) metod kan du skala dataflöde värdet. 

När du använder .NET SDK, den [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) metod kan du hämta den minsta genomströmningen i en behållare eller en databas. 

Du kan skala det etablerade dataflödet i en behållare eller en databas när som helst. Du kan köra skala ned igen efter den inaktiva i 4 timmar. Inaktiv tid definieras som tid period när det inte fanns inget erbjudande Ersätt-åtgärder (vilket innefattar skala upp och skala ned) i en behållare eller en databas. 

## <a name="comparison-of-models"></a>Jämförelse av modeller

|**Parametern**  |**Dataflödet som etablerats på en databas**  |**Dataflödet som etableras i en behållare**|
|---------|---------|---------|
|Minsta ru: er |400 (varje ytterligare behållare kräver minst 100 ru: er per sekund efter de första fyra behållarna.) |400|
|Minsta ru: er per behållare|100|400|
|Minsta ru: er som krävs för att använda 1 GB lagringsutrymme|40|40|
|Maximal ru: er|Obegränsade för databasen.|Obegränsade behållare.|
|RU: er tilldelade eller tillgängligt för en specifik behållare|Inga garantier. RU: er som har tilldelats en viss behållare beror på egenskaperna. Egenskaper kan vara valet av partitionsnycklar behållare som delar dataflödet, distribution av arbetsbelastningen och antalet behållare. |Alla ru: er som har konfigurerats på behållaren är enbart för behållaren.|
|Maximalt lagringsutrymme för en behållare|Obegränsad.|Obegränsad.|
|Högsta dataflöde per logisk partition för en behållare|10K ru: er|10K ru: er|
|Maximalt lagringsutrymme (data + index) per logisk partition för en behållare|10 GB|10 GB|

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig hur du [etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablera dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).


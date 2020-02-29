---
title: Etablera dataflöde för Azure Cosmos-behållare och databaser
description: Lär dig hur du ställer in tillhandahållet data flöde för dina Azure Cosmos-behållare och-databaser.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: 31ad7a9d1108adc9071812454419252a813cb93e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/29/2020
ms.locfileid: "78194877"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Etablera dataflöde på containrar och databaser

En Azure Cosmos-databas är en enhet för hantering för en uppsättning behållare. En databas består av en uppsättning schemaoberoende behållare. En Azure Cosmos-behållare är Faktureringsenhet skalbarhet för både dataflöde och lagring. En behållare är horisontellt partitionerade över en uppsättning datorer i en Azure-region och distribueras över alla Azure-regioner som är associerade med ditt Azure Cosmos-konto.

Med Azure Cosmos DB kan du etablera data flöde med två granularitet:
 
- Azure Cosmos-containrar
- Azure Cosmos-databaser

## <a name="set-throughput-on-a-container"></a>Ange data flöde i en behållare  

Det data flöde som har allokerats på en Azure Cosmos-behållare är exklusivt reserverat för den behållaren. Behållaren tar emot det etablerade dataflödet som hela tiden. Dataflöde i en behållare har inget serviceavtal med ekonomisk uppbackning av serviceavtal. Information om hur du konfigurerar data flöde i en behållare finns i [etablera data flöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

Att ställa in tillhandahållet data flöde på en behållare är det alternativ som används oftast. Du kan skala data flöde elastiskt för en behållare genom att tillhandahålla valfri mängd data flöde med hjälp av [enheter för programbegäran (ru: er)](request-units.md). 

Det data flöde som har allokerats för en behållare distribueras jämnt mellan de fysiska partitionerna och en lämplig partitionsnyckel som distribuerar de logiska partitionerna jämnt mellan de fysiska partitionerna fördelas också med data flödet jämnt över hela behållarens logiska partitioner. Du kan inte selektivt ange data flödet för logiska partitioner. Eftersom en eller flera logiska partitioner i en behållare finns i en fysisk partition, hör de fysiska partitionerna exklusivt till behållaren och har stöd för det data flöde som har allokerats på behållaren. 

Om arbets belastningen som körs på en logisk partition förbrukar mer än det data flöde som allokerats till den logiska partitionen får dina åtgärder begränsad hastighet. När Rate-Limiting inträffar kan du antingen öka det etablerade data flödet för hela behållaren eller utföra åtgärderna igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Vi rekommenderar att du konfigurerar data flödet på behållar precisionen när du vill ha garanterade prestanda för behållaren.

Följande bild visar hur en fysisk partition är värd för en eller flera logiska partitioner i en behållare:

![Fysisk partition](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ange data flöde för en databas

När du etablerar data flöde i en Azure Cosmos-databas delas data flödet över alla behållare (kallas delade databas behållare) i databasen. Ett undantag är om du har angett ett tillhandahållet data flöde på specifika behållare i databasen. Att dela databas nivåns etablerade data flöde bland dess behållare är detsamma som att vara värd för en databas på ett kluster med datorer. Eftersom alla behållare i en databas delar resurserna som är tillgängliga på en dator, kan du naturligt inte få förutsägbara prestanda för en viss behållare. Information om hur du konfigurerar tillhandahållet data flöde på en databas finns i [Konfigurera etablerade data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

Genom att ställa in data flöde på en Azure Cosmos-databas garanteras att du tar emot det etablerade data flödet för databasen hela tiden. Eftersom alla behållare i databasen delar det etablerade data flödet ger Azure Cosmos DB inga förutsägbara data flödes garantier för en viss behållare i databasen. Del av vilket dataflöde som kan ta emot en viss behållare är beroende av:

* Antalet behållare.
* Val av partitionsnyckel för olika behållare.
* Distribution av arbetsbelastning i olika logiska partitioner av behållarna. 

Vi rekommenderar att du konfigurerar data flödet på en databas när du vill dela data flödet över flera behållare, men inte vill tilldela data flödet till en viss behållare. 

Följande exempel visar var det är önskvärt att etablera data flöde på databas nivå:

* Att dela en Databass etablerade data flöde i en uppsättning behållare är användbart för ett program med flera innehavare. Varje användare kan representeras av en distinkt Azure Cosmos-behållare.

* Att dela en Databass etablerade data flöde i en uppsättning behållare är användbart när du migrerar en NoSQL-databas, till exempel MongoDB eller Cassandra, som finns på ett kluster med virtuella datorer eller från lokala fysiska servrar till Azure Cosmos DB. Tänk på det etablerade data flödet som kon figurer ATS på din Azure Cosmos-databas som en logisk motsvarighet, men mer kostnads effektivt och elastiskt, till beräknings kapaciteten för ditt MongoDB-eller Cassandra-kluster.  

Alla behållare som skapats i en databas med ett allokerat data flöde måste skapas med en [partitionsnyckel](partition-data.md). Vid en viss tidpunkt distribueras det data flöde som allokerats till en behållare i en databas över alla logiska partitioner i behållaren. När du har behållare som delar ett allokerat data flöde som kon figurer ATS för en databas, kan du inte selektivt använda genomflödet för en speciell behållare eller en logisk partition. 

Om arbets belastningen på en logisk partition förbrukar mer än det data flöde som har allokerats till en viss logisk partition, är dina åtgärder avgiftsbelagda. När Rate-Limiting sker kan du antingen öka data flödet för hela databasen eller försöka utföra åtgärderna igen. Mer information om partitionering finns i [logiska partitioner](partition-data.md).

Behållare i en delad data flödes databas delar data flöde (RU/s) som allokeras till databasen. Du kan ha upp till fyra behållare med minst 400 RU/s på databasen. Varje ny behållare efter de första fyra kräver ytterligare 100 RU/s-minimum. Om du till exempel har en delad data flödes databas med åtta behållare blir lägsta RU/s i databasen 800 RU/s.

> [!NOTE]
> I februari 2020 införde vi en ändring som gör att du kan ha högst 25 behållare i en delad data flödes databas, vilket bättre möjliggör data flödes delning över behållarna. Efter de första 25 behållarna kan du bara lägga till fler behållare i databasen om de är [etablerade med dedikerat data flöde](#set-throughput-on-a-database-and-a-container), som är åtskilda från det delade data flödet i databasen.<br>
Om ditt Azure Cosmos DB-konto redan innehåller en delad data flödes databas med > = 25 behållare, är kontot och alla andra konton i samma Azure-prenumeration undantagna från den här ändringen. [Kontakta produkt supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du har feedback eller frågor. 

Om arbets belastningarna innebär att du tar bort och återskapar alla samlingar i en databas, rekommenderar vi att du släpper den tomma databasen och återskapar en ny databas innan du skapar samlingen. Följande bild visar hur en fysisk partition kan vara värd för en eller flera logiska partitioner som tillhör olika behållare i en databas:

![Fysisk partition](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ange data flöde för en databas och en behållare

Du kan kombinera de två modellerna. Etablering av data flöde på både databasen och behållaren tillåts. I följande exempel visar hur du etablera dataflöde för en Azure Cosmos-databas och en behållare:

* Du kan skapa en Azure Cosmos-databas med namnet *Z* och det etablerade data flödet för *"K"* ru: er. 
* Skapa sedan fem behållare med namnet *A*, *B*, *C*, *D*och *E* i databasen. När du skapar container B, se till att aktivera **etablera dedikerat data flöde för det här behållar** alternativet och konfigurera *"P"* -ru: er av etablerat data flöde på den här behållaren. Observera att du bara kan konfigurera delade och dedikerade data flöde när du skapar databasen och behållaren. 

   ![Ange data flödet på behållar nivån](./media/set-throughput/coll-level-throughput.png)

* *"K"* ru: er-dataflödet delas mellan de fyra behållarna *A*, *C*, *D*, och *E*. Den exakta mängden data flöde som är tillgängliga för *A*, *C*, *D*eller *E* varierar. Det finns inga service avtal för varje enskild behållares data flöde.
* Behållaren med namnet *B* garanterar att du kan hämta *"P"* ru: er-dataflöde hela tiden. Den backas upp av service avtal.

> [!NOTE]
> Det går inte att konvertera en behållare med ett allokerat data flöde till en delad databas behållare. Det går inte att konvertera en delad databas behållare till ett dedikerat data flöde.

## <a name="update-throughput-on-a-database-or-a-container"></a>Uppdatera data flödet för en databas eller en behållare

När du har skapat en Azure Cosmos-behållare eller en databas kan du uppdatera det etablerade data flödet. Det finns ingen gräns för maximalt tillhandahållet data flöde som du kan konfigurera i databasen eller behållaren. Det lägsta allokerade data flödet beror på följande faktorer: 

* Maximal data storlek som du någonsin lagrat i behållaren
* Det maximala data flöde som du någonsin etablerar på behållaren
* Det maximala antalet Azure Cosmos-behållare som du någonsin skapar i en databas med delat data flöde. 

Du kan hämta det lägsta data flödet för en behållare eller en databas program mässigt genom att använda SDK: erna eller Visa värdet i Azure Portal. När du använder .NET SDK kan du skala det tillhandahållna data flöde svärdet med metoden [DocumentClient. ReplaceOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) . När du använder Java SDK kan du använda metoden [RequestOptions. setOfferThroughput](sql-api-java-samples.md#offer-examples) för att skala det tillhandahållna data flöde svärdet. 

När du använder .NET SDK kan du använda metoden [DocumentClient. ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) för att hämta det lägsta data flödet för en behållare eller en databas. 

Du kan skala det etablerade data flödet för en behållare eller en databas när som helst. När en skalnings åtgärd utförs för att öka data flödet kan det ta längre tid på grund av system aktiviteterna för att etablera nödvändiga resurser. Du kan kontrol lera status för skalnings åtgärden i Azure Portal eller program mässigt med SDK: er. När du använder .NET SDK kan du hämta status för skalnings åtgärden med hjälp av metoden `DocumentClient.ReadOfferAsync`.

## <a name="comparison-of-models"></a>Jämförelse av modeller

|**ProfileServiceApplicationProxy**  |**Data flöde som har allokerats till en databas**  |**Data flöde som har allokerats på en behållare**|
|---------|---------|---------|
|Minsta ru: er |400 (efter de första fyra behållarna kräver varje ytterligare behållare minst 100 ru: er per sekund.) |400|
|Minsta ru: er per behållare|100|400|
|Maximal ru: er|Obegränsat, på databasen.|Obegränsat, på behållaren.|
|Ru: er tilldelad eller tillgänglig för en speciell behållare|Inga garantier. Ru: er som tilldelas en specifik behållare beror på egenskaperna. Egenskaper kan vara valet av partitionsnyckel för behållare som delar data flödet, distributionen av arbets belastningen och antalet behållare. |Alla ru: er som har konfigurerats på behållaren är enbart för behållaren.|
|Maximalt lagringsutrymme för en behållare|Många.|Många.|
|Högsta dataflöde per logisk partition för en behållare|10K ru: er|10K ru: er|
|Maximalt lagringsutrymme (data + index) per logisk partition för en behållare|20 GB|20 GB|

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar data flöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).


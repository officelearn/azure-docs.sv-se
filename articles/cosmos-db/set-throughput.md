---
title: Etablera dataflöde på Azure Cosmos-behållare och databaser
description: Lär dig hur du anger etablerat dataflöde för dina Azure Cosmos-behållare och databaser.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/12/2019
ms.openlocfilehash: e7a64776cba00a6840af70cecad5bf9c02b3f38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251978"
---
# <a name="provision-throughput-on-containers-and-databases"></a>Etablera dataflöde på containrar och databaser

En Azure Cosmos-databas är en hanteringsenhet för en uppsättning behållare. En databas består av en uppsättning schemaaognostiska behållare. En Azure Cosmos-behållare är skalbarhetsenhet för både dataflöde och lagring. En behållare är vågrätt partitionerad över en uppsättning datorer inom en Azure-region och distribueras över alla Azure-regioner som är associerade med ditt Azure Cosmos-konto.

Med Azure Cosmos DB kan du etablera dataflöde på två granularities:
 
- Azure Cosmos-containrar
- Azure Cosmos-databaser

## <a name="set-throughput-on-a-container"></a>Ange dataflöde på en behållare  

Dataflödet som etablerats på en Azure Cosmos-behållare är endast reserverad för den behållaren. Behållaren tar emot det etablerade dataflödet hela tiden. Det etablerade dataflödet på en behållare backas upp ekonomiskt av SLA:er. Mer information om hur du konfigurerar dataflöde på en behållare finns [i Etablera dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).

Att ange etablerat dataflöde på en behållare är det mest använda alternativet. Du kan elastiskt skala dataflödet för en behållare genom att etablera valfri mängd dataflöde med hjälp av [Begärandeenheter (RU: er)](request-units.md). 

Dataflödet som etablerats för en behållare är jämnt fördelat mellan dess fysiska partitioner, och förutsatt att en bra partitionsnyckel som distribuerar de logiska partitionerna jämnt mellan de fysiska partitionerna, är dataflödet också jämnt fördelat över alla de logiska partitionerna i behållaren. Du kan inte selektivt ange dataflödet för logiska partitioner. Eftersom en eller flera logiska partitioner i en behållare är värd för en fysisk partition, tillhör de fysiska partitionerna uteslutande behållaren och stöder dataflödet som etablerats på behållaren. 

Om arbetsbelastningen som körs på en logisk partition förbrukar mer än dataflödet som allokerades till den logiska partitionen, blir dina åtgärder begränsade. När hastighetsbegränsning inträffar kan du antingen öka det etablerade dataflödet för hela behållaren eller försöka igen med åtgärderna. Mer information om partitionering finns i [Logiska partitioner](partition-data.md).

Vi rekommenderar att du konfigurerar dataflöde vid behållarens granularitet när du vill ha garanterad prestanda för behållaren.

Följande bild visar hur en fysisk partition är värd för en eller flera logiska partitioner i en behållare:

![Fysisk partition](./media/set-throughput/resource-partition.png)

## <a name="set-throughput-on-a-database"></a>Ange dataflöde i en databas

När du etablerar dataflöde på en Azure Cosmos-databas delas dataflödet mellan alla behållare (så kallade delade databasbehållare) i databasen. Ett undantag är om du har angett ett etablerat dataflöde på specifika behållare i databasen. Att dela det etablerade dataflödet på databasnivå mellan behållarna är analogt med att vara värd för en databas på ett kluster av datorer. Eftersom alla behållare i en databas delar de resurser som är tillgängliga på en dator får du naturligtvis inte förutsägbara prestanda på en specifik behållare. Mer information om hur du konfigurerar etablerat dataflöde i en databas finns i [Konfigurera etablerat dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).

Ställa in dataflöde på en Azure Cosmos-databas garanterar att du får det etablerade dataflödet för den databasen hela tiden. Eftersom alla behållare i databasen delar det etablerade dataflödet ger Azure Cosmos DB inga förutsägbara dataflödesgarantier för en viss behållare i databasen. Den del av dataflödet som en viss behållare kan ta emot är beroende av:

* Antalet containrar.
* Valet av partitionsnycklar för olika behållare.
* Fördelningen av arbetsbelastningen över olika logiska partitioner av behållarna. 

Vi rekommenderar att du konfigurerar dataflöde i en databas när du vill dela dataflödet över flera behållare, men inte vill dedikera dataflödet till någon viss behållare. 

Följande exempel visar var det är att föredra att etablera dataflöde på databasnivå:

* Det är användbart för ett program med flera innehavare av en databas med etablerat dataflöde över en uppsättning behållare. Varje användare kan representeras av en separat Azure Cosmos-behållare.

* Det är användbart att dela en databass etablerade dataflöde över en uppsättning behållare när du migrerar en NoSQL-databas, till exempel MongoDB eller Cassandra, som finns i ett kluster av virtuella datorer eller från lokala fysiska servrar till Azure Cosmos DB. Tänk på det etablerade dataflödet som konfigurerats på din Azure Cosmos-databas som en logisk motsvarighet, men mer kostnadseffektiv och elastisk, till beräkningskapaciteten för ditt MongoDB- eller Cassandra-kluster.  

Alla behållare som skapas i en databas med etablerat dataflöde måste skapas med en [partitionsnyckel](partition-data.md). Vid en viss tidpunkt distribueras dataflödet som allokerats till en behållare i en databas över alla logiska partitioner i den behållaren. När du har behållare som delar etablerat dataflöde konfigurerat i en databas kan du inte selektivt tillämpa dataflödet på en viss behållare eller en logisk partition. 

Om arbetsbelastningen på en logisk partition förbrukar mer än dataflödet som allokeras till en viss logisk partition, är dina åtgärder klass-begränsade. När hastighetsbegränsning inträffar kan du antingen öka dataflödet för hela databasen eller försöka igen med åtgärderna. Mer information om partitionering finns i [Logiska partitioner](partition-data.md).

Behållare i en delad dataflödesdatabas delar dataflödet (RU/s) som tilldelats databasen. Du kan ha upp till fyra behållare med minst 400 RU/s i databasen. Varje ny behållare efter de fyra första kommer att kräva ytterligare 100 RU/s. Om du till exempel har en databas med delat dataflöde med åtta behållare blir det minsta RU/s i databasen 800 RU/s.

> [!NOTE]
> I februari 2020 införde vi en ändring som gör att du kan ha högst 25 behållare i en delad dataflödesdatabas, vilket bättre möjliggör dataflödesdelning över behållarna. Efter de första 25 behållarna kan du bara lägga till fler behållare i databasen om de [etableras med dedikerat dataflöde](#set-throughput-on-a-database-and-a-container), som är separat från databasens delade dataflöde.<br>
Om ditt Azure Cosmos DB-konto redan innehåller en delad dataflödesdatabas med >=25 behållare, är kontot och alla andra konton i samma Azure-prenumeration undantagna från den här ändringen. [Kontakta produktsupporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) om du har feedback eller frågor. 

Om dina arbetsbelastningar innebär att ta bort och återskapa alla samlingar i en databas, rekommenderar vi att du släpper den tomma databasen och återskapar en ny databas innan samlingen skapas. Följande bild visar hur en fysisk partition kan vara värd för en eller flera logiska partitioner som tillhör olika behållare i en databas:

![Fysisk partition](./media/set-throughput/resource-partition2.png)

## <a name="set-throughput-on-a-database-and-a-container"></a>Ange dataflöde i en databas och en behållare

Du kan kombinera de två modellerna. Det är tillåtet att etablera dataflödet i både databasen och behållaren. I följande exempel visas hur du etablerar dataflöde på en Azure Cosmos-databas och en behållare:

* Du kan skapa en Azure Cosmos-databas med namnet *Z* med etablerat dataflöde av "K"-ru:er. *"K"* 
* Skapa sedan fem behållare med namnet *A,* *B*, *C*, *D*och *E* i databasen. När du skapar behållare B, se till att aktivera **etablering dedikerat dataflöde för det här behållaralternativet** och uttryckligen konfigurera *"P"* RU:er för etablerat dataflöde på den här behållaren. Observera att du bara kan konfigurera delat och dedikerat dataflöde när du skapar databasen och behållaren. 

   ![Ställa in dataflödet på behållarnivå](./media/set-throughput/coll-level-throughput.png)

* *Dataflödet "K"* delas mellan de fyra behållarna *A,* *C,* *D*och *E*. Den exakta mängden dataflöde som är tillgängligt för *A,* *C,* *D*eller *E* varierar. Det finns inga SLA:er för varje enskild behållares dataflöde.
* Behållaren som heter *B* är garanterad att få *"P"* RU dataflöde hela tiden. Det backas upp av SLA.

> [!NOTE]
> Det går inte att konvertera en behållare med etablerat dataflöde till delad databasbehållare. Omvänt kan inte en delad databasbehållare konverteras för att ha ett dedikerat dataflöde.

## <a name="update-throughput-on-a-database-or-a-container"></a>Uppdatera dataflödet i en databas eller en behållare

När du har skapat en Azure Cosmos-behållare eller en databas kan du uppdatera det etablerade dataflödet. Det finns ingen gräns för det maximala etablerade dataflödet som du kan konfigurera på databasen eller behållaren. Det [minsta etablerade dataflödet](concepts-limits.md#storage-and-throughput) beror på följande faktorer: 

* Den maximala datastorlek som du lagrar i behållaren
* Det maximala dataflöde som du någonsin etablerar på behållaren
* Det aktuella antalet Azure Cosmos-behållare som du har i en databas med delat dataflöde. 

Du kan hämta minsta dataflöde för en behållare eller en databas programmässigt med hjälp av SDK:erna eller visa värdet i Azure-portalen. När du använder .NET SDK kan du skala det etablerade dataflödesvärdet med metoden [DocumentClient.ReplaceOfferAsync.](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.replaceofferasync?view=azure-dotnet) När du använder Java SDK kan du skala det etablerade dataflödesvärdet med metoden [RequestOptions.setOfferThroughput.](sql-api-java-samples.md#offer-examples) 

När du använder .NET SDK kan du med metoden [DocumentClient.ReadOfferAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.documentclient.readofferasync?view=azure-dotnet) hämta det minsta dataflödet för en behållare eller en databas. 

Du kan skala det etablerade dataflödet för en behållare eller en databas när som helst. När en skalningsåtgärd utförs för att öka dataflödet kan det ta längre tid på grund av systemaktiviteterna för att etablera de resurser som krävs. Du kan kontrollera status för skalningsåtgärden i Azure-portalen eller programmässigt med hjälp av SDK:erna. När du använder .Net SDK kan du hämta status `DocumentClient.ReadOfferAsync` för skalningsåtgärden med hjälp av metoden.

## <a name="comparison-of-models"></a>Jämförelse av modeller

|**Parametern**  |**Dataflöde etablerat i en databas**  |**Dataflöde etablerat på en behållare**|
|---------|---------|---------|
|Minsta RUs |400 (Efter de första fyra behållarna kräver varje ytterligare behållare minst 100 ru:er per sekund.) |400|
|Minsta RUs per behållare|100|400|
|Maximalt antal ru:er|Obegränsad, på databasen.|Obegränsad, på behållaren.|
|Ru:er som tilldelats eller är tillgängliga för en viss behållare|Inga garantier. Ru:er som tilldelats en viss behållare beror på egenskaperna. Egenskaper kan vara valet av partitionsnycklar för behållare som delar dataflödet, fördelningen av arbetsbelastningen och antalet behållare. |Alla ru:er som konfigurerats på behållaren är endast reserverade för behållaren.|
|Maximal lagring för en behållare|Obegränsad.|Obegränsad.|
|Maximalt dataflöde per logisk partition i en behållare|10 000 ru:er|10 000 ru:er|
|Maximal lagring (data + index) per logisk partition för en behållare|20 GB|20 GB|

## <a name="next-steps"></a>Nästa steg

* Läs mer om [logiska partitioner](partition-data.md).
* Lär dig hur du [etablerar dataflöde på en Azure Cosmos-behållare](how-to-provision-container-throughput.md).
* Lär dig hur du [etablerar dataflöde i en Azure Cosmos-databas](how-to-provision-database-throughput.md).


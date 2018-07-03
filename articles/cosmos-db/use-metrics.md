---
title: Övervaka och felsöka med mått i Azure Cosmos DB | Microsoft Docs
description: Använda mått i Azure Cosmos DB för att felsöka vanliga problem och övervaka databasen.
keywords: metrics
services: cosmos-db
author: kanshiG
manager: kfile
editor: ''
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 9b9f72812b1a1f0e30379c32e10d316fcbf71d3b
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37345597"
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Övervaka och felsöka med mått i Azure Cosmos DB

Azure Cosmos DB tillhandahåller mått för dataflöde, lagring, konsekvens, tillgänglighet och svarstid. Den [Azure-portalen](https://portal.azure.com) visar en sammansatt vy av de här måtten; för mer detaljerade mätvärden, både klient-SDK och [diagnostikloggar](./logging.md) är tillgängliga.

Titta på följande Azure Friday-video för att få en översikt över nya mått och lär dig hitta heta partitioner i databasen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Den här artikeln beskriver vanliga användarsituationer och hur Azure Cosmos DB-mått kan användas för att analysera och felsöka problemen. Mått samlas in var femte minut och bevaras i sju dagar.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Förstå hur många begäranden lyckas eller orsakar fel

Kom igång genom att gå till den [Azure-portalen](https://portal.azure.com) och navigera till den **mått** bladet. På bladet hittar den **antalet begäranden överskred kapaciteten per 1 minut** diagram. Det här diagrammet visar en minut av minut förfrågningarna uppdelat efter statuskoden. Mer information om HTTP-statuskoder finns i [HTTP-statuskoder för Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Den vanligaste felkoden är 429 (Betygsätt begränsar/begränsning), vilket innebär att begäranden till Azure Cosmos DB överstiger det etablerade dataflödet. Den vanligaste lösningen på detta är att [skala upp antalet enheter för programbegäran](./set-throughput.md) för den givna samlingen.

![Antal begäranden per minut](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>Bestämma fördelningen dataflöde över partitioner

Att ha en bra Kardinaliteten för din partitionsnycklar är viktigt för alla skalbara program. För att fastställa dataflöde distributionen av en partitionerad samling uppdelat efter partitioner, navigera till den **måttbladet** i den [Azure-portalen](https://portal.azure.com). I den **dataflöde** fliken analys på detaljnivå för lagring visas i den **Max konsumerade RU/sekund av respektive fysisk partition** diagram. Följande bild illustrerar ett exempel på en dålig fördelning av data, vilket framgår av skeva partitionen längst till vänster. 

![Enskild partition ser tung användning klockan 3:05](media/use-metrics/metrics-17.png)

En ojämn dataflöde fördelning kan orsaka *frekvent* partitioner, vilket kan leda till begränsade begäranden och kan kräva ompartitionering. Mer information om partitionering i Azure Cosmos DB finns i [partitionera och skala i Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Bestämma fördelningen lagring över partitioner

Att ha en bra Kardinaliteten för partitionen är viktigt för alla skalbara program. För att fastställa dataflöde distributionen av en partitionerad samling uppdelat efter partitioner, gå till bladet mått i den [Azure-portalen](https://portal.azure.com). På fliken dataflöde visas analys på detaljnivå för lagring i Max konsumerade RU/sekund efter varje fysisk partition-diagram. Följande bild illustrerar en dålig fördelning av data, vilket framgår av skeva partitionen längst till vänster. 

![Exempel på dålig Datadistribution](media/use-metrics/metrics-07.png)

Du kan rotorsak vilka partitionsnyckel skeva distributionen genom att klicka på partitionen i diagrammet. 

![Partitionsnyckeln skeva distributionen](media/use-metrics/metrics-05.png)

Efter att identifiera vilka partitionsnyckel orsakar skeva i distributionen, kan du behöva partitionera om din samling med en fler distribuerade partitionsnyckel. Mer information om partitionering i Azure Cosmos DB finns i [partitionera och skala i Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Jämföra datastorlek mot Indexstorlek

I Azure Cosmos DB är det totala lagringsutrymmet som förbrukar en kombination av både datastorlek och Indexstorlek. Indexstorleken är vanligtvis en bråkdel av storleken på data. I bladet mått i den [Azure-portalen](https://portal.azure.com), fliken lagring visar fördelningen av användningen av lagringsutrymme baserat på data och index. Bild (kanske) också från SDK, du kan hitta aktuella lagringsanvändningen genom en samling läsa.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Om du vill spara utrymme för index kan du justera den [indexeringspolicy](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Felsökning varför frågorna körs långsamt

I SDK: er för SQL API: et tillhandahåller Azure Cosmos DB frågestatistik för körning. 

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName), 
 “SELECT * FROM c WHERE c.city = ‘Seattle’”, 
 new FeedOptions 
 { 
 PopulateQueryMetrics = true, 
 MaxItemCount = -1, 
 MaxDegreeOfParallelism = -1, 
 EnableCrossPartitionQuery = true 
 }).AsDocumentQuery();
FeedResponse<dynamic> result = await query.ExecuteNextAsync();

// Returns metrics by partition key range Id 
IReadOnlyDictionary<string, QueryMetrics> metrics = result.QueryMetrics;
```

*QueryMetrics* innehåller information om hur lång tid varje komponent i frågan tog att körningen. De vanligaste orsaken för lång körs frågorna är genomsökningar (frågan var det går inte att använda index), som kan lösas med en bättre filtervillkoret.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig att övervaka och felsöka problem med hjälp av mätvärden i Azure-portalen, kanske du vill lära dig mer om att förbättra databasens prestanda genom att läsa följande artiklar:

* [Prestanda och skalningstester med Azure Cosmos DB](performance-testing.md)
* [Prestandatips för Azure Cosmos DB](performance-tips.md)

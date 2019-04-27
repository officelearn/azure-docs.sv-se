---
title: Övervaka och felsöka med mått i Azure Cosmos DB
description: Använda mått i Azure Cosmos DB för att felsöka vanliga problem och övervaka databasen.
ms.service: cosmos-db
author: kanshiG
ms.author: sngun
ms.topic: conceptual
ms.date: 11/15/2018
ms.reviewer: sngun
ms.openlocfilehash: ff6e0b6084eebf236d01b4dd00a46897687938c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765846"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Övervaka och felsöka med mått i Azure Cosmos DB

Azure Cosmos DB tillhandahåller mått för dataflöde, lagring, konsekvens, tillgänglighet och svarstid. Den [Azure-portalen](https://portal.azure.com) visar en sammansatt vy av de här måtten. För mer detaljerade mätvärden, både klient-SDK och [diagnostikloggar](./logging.md) är tillgängliga.

Den här artikeln beskriver vanliga användarsituationer och hur Azure Cosmos DB-mått kan användas för att analysera och felsöka problemen. Mått samlas in var femte minut och hålls i sju dagar.

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Förstå hur många begäranden lyckas eller orsakar fel

Kom igång genom att gå till den [Azure-portalen](https://portal.azure.com) och navigera till den **mått** bladet. På bladet hittar den **antalet begäranden överskred kapaciteten per 1 minut** diagram. Det här diagrammet visar en minut av minut förfrågningarna uppdelat efter statuskoden. Mer information om HTTP-statuskoder finns i [HTTP-statuskoder för Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Den vanligaste felkoden är 429 (pris begränsar/begränsning). Detta fel innebär att begäranden till Azure Cosmos DB är mer än det etablerade dataflödet. De vanligaste lösningen på det här problemet är att [skala upp antalet enheter för programbegäran](./set-throughput.md) för den givna samlingen.

![Antal begäranden per minut](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Fastställa dataflöde distribution över partitioner

Att ha en bra Kardinaliteten för din partitionsnycklar är viktigt för alla skalbara program. För att fastställa dataflöde distributionen av alla partitionerad behållare uppdelat efter partitioner, navigera till den **måttbladet** i den [Azure-portalen](https://portal.azure.com). I den **dataflöde** fliken analys på detaljnivå för lagring visas i den **Max konsumerade RU/sekund av respektive fysisk partition** diagram. Följande bild illustrerar ett exempel på en dålig fördelning av data som visas av skeva partitioner längst till vänster.

![Enskild partition ser tung användning klockan 3:05](media/use-metrics/metrics-17.png)

En ojämn dataflöde fördelning kan orsaka *frekvent* partitioner, vilket kan leda till begränsade begäranden och kan kräva ompartitionering. Mer information om partitionering i Azure Cosmos DB finns i [partitionera och skala i Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Fastställa storage distribution över partitioner

Att ha en bra Kardinaliteten för partitionen är viktigt för alla skalbara program. För att fastställa storage distributionen av alla partitionerad behållare uppdelat efter partitioner, gå till bladet mått i den [Azure-portalen](https://portal.azure.com). På fliken lagring, analys på detaljnivå för lagring visas i Data + Index lagringsutrymmet som förbrukas av övre partition nycklar diagram. Följande bild illustrerar en dålig fördelning av lagring av data som visas av skeva partitioner längst till vänster.

![Exempel på dålig Datadistribution](media/use-metrics/metrics-07.png)

Du kan rotorsak vilka partitionsnyckel skeva distributionen genom att klicka på partitionen i diagrammet.

![Partitionsnyckeln skeva distributionen](media/use-metrics/metrics-05.png)

Efter att identifiera vilka partitionsnyckel orsakar skeva i distributionen, kan du behöva partitionera om din behållare med en fler distribuerade partitionsnyckel. Mer information om partitionering i Azure Cosmos DB finns i [partitionera och skala i Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Jämför datastorlek mot Indexstorlek

I Azure Cosmos DB är det totala lagringsutrymmet som förbrukar en kombination av både datastorlek och Indexstorlek. Indexstorleken är vanligtvis en bråkdel av storleken på data. I bladet mått i den [Azure-portalen](https://portal.azure.com), fliken lagring visar fördelningen av användningen av lagringsutrymme baserat på data och index.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Om du vill spara utrymme för index kan du justera den [indexeringspolicy](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Felsöka anledningen till att frågorna körs långsamt

I SDK: er för SQL API: et tillhandahåller Azure Cosmos DB frågestatistik för körning.

```csharp
IDocumentQuery<dynamic> query = client.CreateDocumentQuery(
 UriFactory.CreateDocumentCollectionUri(DatabaseName, CollectionName),
 "SELECT * FROM c WHERE c.city = 'Seattle'",
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

*QueryMetrics* innehåller information om hur lång tid varje komponent i frågan tog att körningen. Den vanligaste orsaken för tidskrävande frågor är genomsökningar, vilket innebär att frågan inte kunde utnyttja index. Det här problemet kan lösas med en bättre filtervillkoret.

## <a name="next-steps"></a>Nästa steg

Nu har du lärt dig hur du övervakar och felsöka problem med hjälp av mätvärden i Azure-portalen. Du kanske vill lära dig mer om att förbättra databasens prestanda genom att läsa följande artiklar:

* [Prestanda och skalningstester med Azure Cosmos DB](performance-testing.md)
* [Prestandatips för Azure Cosmos DB](performance-tips.md)

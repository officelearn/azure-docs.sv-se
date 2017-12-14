---
title: "Övervakning och felsökning med mått i Azure Cosmos DB | Microsoft Docs"
description: "Använd mått i Azure Cosmos DB att felsöka vanliga problem och övervaka databasen."
keywords: metrics
services: cosmos-db
author: gnot
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: govindk
ms.openlocfilehash: 3b3de91c3850071d7c3fbff1faccde6c17a606e3
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="monitoring-and-debugging-with-metrics-in-azure-cosmos-db"></a>Övervakning och felsökning med mått i Azure Cosmos DB

Azure Cosmos-DB tillhandahåller mått för genomflöde, lagring, konsekvens, tillgänglighet och svarstid. Den [Azure-portalen](https://portal.azure.com) ger en samlad vy av de här måtten; för mer detaljerade statistik, både klient-SDK och [diagnostikloggar](./logging.md) är tillgängliga.

Titta på följande Azure fredag videoklipp om du vill få en översikt över de nya mätvärdena och lär dig hitta varm partitioner i databasen:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Azure-Cosmos-DB-Get-the-Most-Out-of-Provisioned-Throughput/player]
> 

Den här artikeln beskriver hur vanliga användningsområden och hur Azure Cosmos DB mått kan användas för att analysera och felsöka dessa problem. Mått har samlats in var femte minut och bevaras i sju dagar.

## <a name="understanding-how-many-requests-are-succeeding-or-causing-errors"></a>Förstå hur många förfrågningar lyckas eller orsakar fel

Kom igång genom att gå till den [Azure-portalen](https://portal.azure.com) och navigera till den **mått** bladet. I bladet hittar den **antalet begäranden överskred kapacitet per minut** diagram. Det här diagrammet visar en minut av minut förfrågningarna åtskilda med statuskoden. Mer information om HTTP-statuskoder finns [http-statuskoder för Azure Cosmos DB](https://docs.microsoft.com/rest/api/documentdb/http-status-codes-for-documentdb).

Den vanligaste felkoden är 429 (begränsning), vilket innebär att begäranden till Azure Cosmos DB överskrider det tillhandahållna dataflödet. Den vanligaste lösningen till detta är att [skala upp RUs](./set-throughput.md) för den angivna samlingen.

![Antal begäranden per minut](media/use-metrics/metrics-12.png)

## <a name="determining-the-throughput-distribution-across-partitions"></a>När du fastställer genomströmning distribution över partitioner

Med en bra kardinalitet för din partitionsnycklar är viktigt för alla skalbara program. För att fastställa genomströmning distribution av en partitionerad samling uppdelat efter partitioner, navigera till den **mått bladet** i den [Azure-portalen](https://portal.azure.com). I den **genomströmning** fliken lagring uppdelning visas i den **Max RU per sekund som används av varje fysiska partition** diagram. Följande bild illustrerar ett exempel på en dålig fördelning av data, vilket framgår av skeva partitionen längst till vänster. 

![Enskild partition ser stor förbrukning klockan 3:05](media/use-metrics/metrics-17.png)

En ojämn genomströmning distributionsplats kan orsaka *varm* partitioner, vilket kan resultera i begränsad begäranden och kan kräva att partitionera om. Mer information om partitionering i Azure Cosmos DB finns [Partition och skala i Azure Cosmos DB](./partition-data.md).

## <a name="determining-the-storage-distribution-across-partitions"></a>Bestämma fördelningen lagring över partitioner

Det är nödvändigt för alla skalbara program med en bra kardinalitet för partitionen. För att fastställa genomströmning distribution av en partitionerad samling uppdelat efter partitioner, gå till bladet mått i den [Azure-portalen](https://portal.azure.com). Genomströmning på fliken visas lagring uppdelning i Max RU per sekund som används av varje fysiska partition diagram. Följande bild illustrerar en dålig fördelning av data, vilket framgår av skeva partitionen längst till vänster. 

![Exempel på dålig Datadistribution](media/use-metrics/metrics-07.png)

Du kan rotorsak som partitionsnyckel skeva distributionen genom att klicka på partitionen i diagrammet. 

![Partitionsnyckeln skeva distributionen](media/use-metrics/metrics-05.png)

När du identifierar vilka partitionsnyckel orsakar förskjutning i distributionen, kanske partitionera om samlingen med en fler distribuerade partitionsnyckel. Mer information om partitionering i Azure Cosmos DB finns [Partition och skala i Azure Cosmos DB](./partition-data.md).

## <a name="comparing-data-size-against-index-size"></a>Jämföra datastorleken mot Indexstorlek

I Azure Cosmos DB är totala förbrukad lagring en kombination av både den Data och Indexstorlek. Indexstorleken är vanligtvis en bråkdel av storleken på data. I bladet mått i den [Azure-portalen](https://portal.azure.com), fliken lagring visar fördelningen av användningen av lagringsutrymme baserat på data och index. Bild (kanske) också från SDK, hittar du den nuvarande lagringsanvändningen via en samling läsa.
```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll")); 
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
``` 
Om du vill spara index plats kan du justera de [indexering princip](./indexing-policies.md).

## <a name="debugging-why-queries-are-running-slow"></a>Felsökning av orsaken frågorna körs långsamt

I SDK: erna för SQL API ger Azure Cosmos DB statistik för körning av frågan. 

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

*QueryMetrics* innehåller information om hur länge varje komponent i frågan tog att körningen. De vanligaste orsaken till långa körs frågor är genomsökningar (frågan kunde inte använda index), som kan lösas med ett bättre filtervillkor.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du övervakar och Felsök problem med hjälp av mätvärden i Azure-portalen, kanske du vill lära dig mer om hur du förbättrar databasens prestanda genom att läsa följande artiklar:

* [Prestanda och skalning testa med Azure Cosmos DB](performance-testing.md)
* [Prestandatips för Azure Cosmos DB](performance-tips.md)

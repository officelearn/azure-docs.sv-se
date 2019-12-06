---
title: Köra frågor mot containrar i Azure Cosmos DB
description: Lär dig hur du söker efter behållare i Azure Cosmos DB att använda partitioner, kors partitions-och partiella corss
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 88ebb8bb80ec3406c98b77db481994d415b04373
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872034"
---
# <a name="query-an-azure-cosmos-container"></a>Köra frågor mot en Azure Cosmos-container

Den här artikeln beskriver hur du kör frågor mot en container (samling, graf eller tabell) i Azure Cosmos DB.

## <a name="in-partition-query"></a>Frågekörning inom en partition

När du hämtar data från containrar, och ett partitionsnyckelfilter har angetts för frågan, hanteras frågan automatiskt av Azure Cosmos DB. Frågan dirigeras till de partitioner som motsvarar de partitionsnyckelvärden som angetts i filtret. Till exempel dirigeras följande fråga till `DeviceId`-partitionen som innehåller alla dokument som motsvarar partitionsnyckelvärdet `XMS-0001`.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Frågekörning mellan partitioner

Följande fråga har inget filter för partitionsnyckeln (`DeviceId`) och sprids till alla partitioner där den körs mot partitionens index. Om du vill köra en fråga över partitioner anger du `EnableCrossPartitionQuery` till true (eller `x-ms-documentdb-query-enablecrosspartition` i REST-API:et).

Egenskapen EnableCrossPartitionQuery accepterar ett booleskt värde. Om värdet är true (sant) och om frågan inte har en partitionsnyckel skickar Azure Cosmos DB ut frågan över partitioner. Utskicket görs genom att enskilda frågor utfärdas till alla partitioner. Om du vill läsa frågeresultaten ska klient programmen förbruka resultaten från FeedResponse och kontrol lera ContinuationToken-egenskapen. Om du vill läsa alla resultat ska data fortsätta att itereras tills ContinuationToken är null. 

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Azure Cosmos DB har stöd för mängdfunktionerna COUNT, MIN, MAX och AVG mellan containrar med hjälp av SQL. Mängdfunktionerna över containrar startar från SDK-version 1.12.0 och senare. Frågor måste innehålla en enskild mängdoperator och ett enda värde i projektionen.

## <a name="parallel-cross-partition-query"></a>Parallell frågekörning mellan partitioner

Azure Cosmos DB SDK:erna 1.9.0 och senare stöder alternativ för parallell frågekörning. Parallell frågekörning mellan partitioner gör att du kan genomföra frågor med låg latens mellan partitioner. Följande fråga är till exempel konfigurerad för att köras parallellt över partitioner.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Du kan hantera parallell frågekörning genom att justera följande parametrar:

- **MaxDegreeOfParallelism**: Anger det maximala antalet samtidiga nätverksanslutningar till containerns partitioner. Om du anger den här egenskapen till -1 hanterar SDK graden av parallellitet. Om  `MaxDegreeOfParallelism` inte har angetts eller har angetts till 0, vilket är standardvärdet, finns det en enda nätverksanslutning till containerns partitioner.

- **MaxBufferedItemCount**: Avväger frågesvarstid kontra minnesanvändning på klientsidan. Om det här alternativet utelämnas eller anges till -1 hanterar SDK:n antalet objekt som buffras under en parallell frågekörning.

Med samma status för samlingen returnerar en parallell fråga resultat i samma ordning som vid seriell körning. När du utför en fråga över partitioner som inkluderar sorteringsoperatorer (ORDER BY, TOP) utfärdar Azure Cosmos DB SDK frågan parallellt över partitionerna. Den sammanfogar delvis sorterade resultat på klientsidan för att skapa globalt ordnade resultat.

## <a name="next-steps"></a>Nästa steg

I följande artiklar lär du dig mer om partitionering i Azure Cosmos DB:

- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
- [Syntetiska partitionsnycklar i Azure Cosmos DB](synthetic-partition-keys.md)

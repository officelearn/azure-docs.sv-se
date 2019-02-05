---
title: Köra frågor mot containrar i Azure Cosmos DB
description: Lär dig hur du kör frågor mot containrar i Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 11/06/2018
ms.author: mjbrown
ms.openlocfilehash: 08d9978134ce214a468691ec367fb1797f6e86fc
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457759"
---
# <a name="query-an-azure-cosmos-container"></a>Köra frågor mot en Azure Cosmos-container

I den här artikeln beskrivs hur du kör frågor mot en container (samling, graf, tabell) i Azure Cosmos DB.

## <a name="in-partition-query"></a>Frågekörning inom en partition

När du frågar efter data från containrar, och partitionsnyckelfiltret har angetts för frågan, dirigerar Azure Cosmos DB automatiskt frågan till de partitioner som motsvarar partitionsnyckelvärdena som angetts i filtret. Till exempel dirigeras följande fråga till DeviceId-partitionen som innehåller alla dokument som motsvarar partitionsnyckelvärdet ”XMS-0001”.

```csharp
// Query using partition key into a class called, DeviceReading
IQueryable<DeviceReading> query = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"))
    .Where(m => m.MetricType == "Temperature" && m.DeviceId == "XMS-0001");
```

## <a name="cross-partition-query"></a>Frågekörning mellan partitioner

Följande fråga har inget filter på partitionsnyckeln (DeviceId) och är utspridd till alla partitioner där den körs mot partitionens index. Om du vill köra en fråga mellan partitioner anger du **EnableCrossPartitionQuery** till sant (eller x-ms-documentdb-query-enablecrosspartition i REST API).

```csharp
// Query across partition keys into a class called, DeviceReading
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),
    new FeedOptions { EnableCrossPartitionQuery = true })
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100);
```

Cosmos DB har stöd för mängdfunktionerna COUNT, MIN, MAX och AVG mellan containrar med hjälp av SQL. Mängdfunktionerna över containrar startar från SDK-version 1.12.0 och senare. Frågor måste innehålla en enda mängdoperator och ett enda värde i projektionen.

## <a name="parallel-cross-partition-query"></a>Parallell frågekörning mellan partitioner

Cosmos DB-SDK:er 1.9.0 och högre stöder alternativ för parallell frågekörning.  Parallell frågekörning mellan partitioner gör att du kan genomföra frågor med låg latens mellan partitioner. Följande fråga är till exempel konfigurerad för att köras parallellt över partitioner.

```csharp
// Cross-partition Order By Query with parallel execution
IQueryable<DeviceReading> crossPartitionQuery = client.CreateDocumentQuery<DeviceReading>(
    UriFactory.CreateDocumentCollectionUri("myDatabaseName", "myCollectionName"),  
    new FeedOptions { EnableCrossPartitionQuery = true, MaxDegreeOfParallelism = 10, MaxBufferedItemCount = 100})
    .Where(m => m.MetricType == "Temperature" && m.MetricValue > 100)
    .OrderBy(m => m.MetricValue);
```

Du kan hantera parallell frågekörning genom att justera följande parametrar:

- **MaxDegreeOfParallelism**: Anger det högsta antalet samtidiga nätverksanslutningar till containerns partitioner. Om du anger den här egenskapen till -1 hanteras graden av parallellitet av SDK:n. Om MaxDegreeOfParallelism inte anges eller anges till 0, vilket är standardvärdet, finns det en enda nätverksanslutning till containerns partitioner.

- **MaxBufferedItemCount**: Gör en avvägning mellan frågesvarstid och minnesanvändning på klientsidan. Om det här alternativet utelämnas eller anges till -1 hanteras det antal objekt som buffras under parallell frågekörning av SDK:n.

Med samma status för samlingen returnerar en parallell fråga resultat i samma ordning som vid seriell körning. När du utför en fråga över partitioner som inkluderar sorteringsoperatorer (ORDER BY och/eller TOP), utfärdar Azure Cosmos DB SDK frågan parallellt mellan partitionerna och sammanfogar delvis sorterade resulterar på klientsidan för att skapa globalt sorterade resultat.

## <a name="next-steps"></a>Nästa steg

I följande artiklar kan du lära dig om partitionering i Cosmos DB:

- [Partitionering i Azure Cosmos DB](partitioning-overview.md)
- [Syntetiska partitionsnycklar i Azure Cosmos DB](synthetic-partition-keys.md)

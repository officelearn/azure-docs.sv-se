---
title: Övervaka och felsöka med mått i Azure Cosmos DB
description: Använd mått i Azure Cosmos DB för att felsöka vanliga problem och övervaka databasen.
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: b65bc6097d4841c79a68d4313ac7a3f89f6d1dbb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065937"
---
# <a name="monitor-and-debug-with-metrics-in-azure-cosmos-db"></a>Övervaka och felsöka med mått i Azure Cosmos DB

Azure Cosmos DB tillhandahåller mått för dataflöde, lagring, konsekvens, tillgänglighet och svarstid. Azure-portalen innehåller en sammanställd vy över dessa mått. Du kan också Visa Azure Cosmos DB-mått från Azure Monitor-API:et. Mer information om hur du visar mått från Azure-övervakaren finns i artikeln [Hämta mått från Azure Monitor.](cosmos-db-azure-monitor-metrics.md) 

Den här artikeln går igenom vanliga användningsfall och hur Azure Cosmos DB-mått kan användas för att analysera och felsöka dessa problem. Statistiken samlas in var femte minut och hålls i sju dagar.

## <a name="view-metrics-from-azure-portal"></a>Visa mått från Azure Portal

1. Logga in på [Azure-portalen](https://portal.azure.com/)

1. Öppna fönstret **Mått.** Som standard visar mätfönstret mått mått mått måtten måtten måtten för alla databaser i ditt Azure Cosmos-konto. Du kan filtrera dessa mått per databas, behållare eller en region. Du kan också filtrera måtten vid en viss tidsgranularitet. Mer information om dataflöde, lagring, tillgänglighet, svarstid och konsekvensmått finns på separata flikar. 

   ![Resultatmått för Cosmos DB i Azure-portalen](./media/use-metrics/performance-metrics.png)

Följande mått är tillgängliga från **fönstret Mått:** 

* **Dataflödesmått** - Det här måttet visar antalet begäranden som förbrukats eller misslyckades (429 svarskod) eftersom dataflödet eller lagringskapaciteten som etablerats för behållaren har överskridits.

* **Lagringsmått** - Det här måttet visar storleken på data och indexanvändning.

* **Tillgänglighetsmått** - Det här måttet visar procentandelen lyckade begäranden över den totala begäranden per timme. Framgångsfrekvensen definieras av Azure Cosmos DB SLAs.

* **Svarstidsmått** - Det här måttet visar läs- och skrivsvarstiden som observerats av Azure Cosmos DB i den region där ditt konto är verksamt. Du kan visualisera svarstid över regioner för ett georenaderat konto. Det här måttet representerar inte svarstiden från på begäran från på sluten tid.

* **Konsekvensmått** - Det här måttet visar hur slutligen är konsekvensen för den konsekvensmodell du väljer. För konton med flera regioner visar det här måttet också replikeringsfördröjningen mellan de regioner som du har valt.

* **Systemmått** - Det här måttet visar hur många metadatabegäranden som betjänas av huvudpartitionen. Det hjälper också till att identifiera de begränsade begärandena.

I följande avsnitt beskrivs vanliga scenarier där du kan använda Azure Cosmos DB-mått. 

## <a name="understand-how-many-requests-are-succeeding-or-causing-errors"></a>Förstå hur många begäranden som lyckas eller orsakar fel

För att komma igång går du till [Azure-portalen](https://portal.azure.com) och navigerar till **bladet Mått.** I bladet hittar du **Antalet begäranden överskred kapaciteten per 1-minutersdiagram. Det här diagrammet visar en minut för minut totalt begäranden segmenteras av statuskoden. Mer information om HTTP-statuskoder finns i [HTTP-statuskoder för Azure Cosmos DB](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb).

Den vanligaste felstatuskoden är 429 (hastighetsbegränsning/begränsning). Det här felet innebär att begäranden till Azure Cosmos DB är mer än det etablerade dataflödet. Den vanligaste lösningen på det här problemet är att [skala upp ru:erna](./set-throughput.md) för den givna samlingen.

![Antal begäranden per minut](media/use-metrics/metrics-12.png)

## <a name="determine-the-throughput-distribution-across-partitions"></a>Fastställ dataflödesdistributionen mellan partitioner

Att ha en bra kardinalitet för dina partitionsnycklar är viktigt för alla skalbara program. Om du vill ta reda på dataflödesdistributionen av en partitionerad behållare som delas upp efter partitioner navigerar du till **bladet Mått** i [Azure-portalen](https://portal.azure.com). På fliken **Dataflöde** visas lagringsfördelningen i **max förbrukad RU/sekund av varje fysiskt partitionsdiagram.** Följande bild illustrerar ett exempel på en dålig fördelning av data som visas av den skeva partitionen längst till vänster.

![En partition ser tung användning vid 15:05](media/use-metrics/metrics-17.png)

En ojämn dataflödesfördelning kan orsaka *heta* partitioner, vilket kan resultera i begränsade begäranden och kan kräva partitionering. Mer information om partitionering i Azure Cosmos DB finns [i Partition och skala i Azure Cosmos DB](./partition-data.md).

## <a name="determine-the-storage-distribution-across-partitions"></a>Fastställ lagringsdistributionen mellan partitioner

Att ha en bra kardinalitet för din partition är viktigt för alla skalbara program. Om du vill bestämma lagringsdistributionen av en partitionerad behållare som delas upp efter partitioner går du till bladet Mått i [Azure-portalen](https://portal.azure.com). På fliken Lagring visas lagringsuppdelningen i data + indexlagring som förbrukas av diagrammet översta partitionsnycklar. Följande bild illustrerar en dålig fördelning av datalagring som visas av den skeva partitionen längst till vänster.

![Exempel på dålig datadistribution](media/use-metrics/metrics-07.png)

Du kan grundorsaken som partitionsnyckeln förvränger distributionen genom att klicka på partitionen i diagrammet.

![Partitionsnyckeln förvränger distributionen](media/use-metrics/metrics-05.png)

När du har identifierat vilken partitionsnyckel som orsakar skevningen i distributionen kan du behöva partitionera om behållaren med en mer distribuerad partitionsnyckel. Mer information om partitionering i Azure Cosmos DB finns [i Partition och skala i Azure Cosmos DB](./partition-data.md).

## <a name="compare-data-size-against-index-size"></a>Jämföra datastorlek med indexstorlek

I Azure Cosmos DB är den totala förbrukade lagringen en kombination av både datastorlek och indexstorlek. Vanligtvis är indexstorleken en bråkdel av datastorleken. I bladet Mått i [Azure-portalen](https://portal.azure.com)visar fliken Lagring fördelningen av lagringsförbrukningen baserat på data och index.

```csharp
// Measure the document size usage (which includes the index size)  
ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));
 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);
```

Om du vill spara indexutrymme kan du justera [indexeringsprincipen](index-policy.md).

## <a name="debug-why-queries-are-running-slow"></a>Felsöka varför frågor körs långsamt

I SQL API-SDK:erna tillhandahåller Azure Cosmos DB statistik för körning av frågor.

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

*QueryMetrics* innehåller information om hur lång tid varje komponent i frågan tog att utföra. Den vanligaste grundorsaken för tidskrävande frågor är genomsökningar, vilket innebär att frågan inte kunde utnyttja indexen. Det här problemet kan lösas med ett bättre filtertillstånd.

## <a name="next-steps"></a>Nästa steg

Du har nu lärt dig hur du övervakar och felsöker problem med hjälp av måtten i Azure-portalen. Du kanske vill veta mer om hur du förbättrar databasens prestanda genom att läsa följande artiklar:

* Mer information om hur du visar mått från Azure-övervakaren finns i artikeln [Hämta mått från Azure Monitor.](cosmos-db-azure-monitor-metrics.md) 
* [Prestanda- och skalningstestning med Azure Cosmos DB](performance-testing.md)
* [Prestandatips för Azure Cosmos DB](performance-tips.md)

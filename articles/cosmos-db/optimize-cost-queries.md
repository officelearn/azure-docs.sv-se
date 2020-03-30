---
title: Optimera kostnader och RU/s för att köra frågor i Azure Cosmos DB
description: Lär dig hur du utvärderar enhetsavgifter för begäran för en fråga och optimerar frågan när det gäller prestanda och kostnad.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/01/2019
ms.openlocfilehash: dd75ad4ed1024292868f113e474fe8b8b73679b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445136"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optimera kostnaden för frågor i Azure Cosmos DB

Azure Cosmos DB erbjuder en omfattande uppsättning databasåtgärder, inklusive relationella och hierarkiska frågor som används för objekten i en behållare. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. Istället för att tänka på och hantera maskinvaruresurser kan du tänka på en begäransenhet som det enda måttet på de resurser som krävs för att utföra olika databasoperationer och tillgodose en begäran. Den här artikeln beskriver hur du utvärderar kostnader för enheter för programbegäran och optimerar frågan avseende prestanda och kostnad. 

Frågor i Azure Cosmos DB beställs vanligtvis från snabbast/mest effektiva till långsammare/mindre effektiva när det gäller dataflöde enligt följande:  

* GET-åtgärd på en enda partitionsnyckel och objektnyckel.

* Fråga med en filtersats inom en enda partitionsnyckel.

* Fråga utan likhets- eller intervallfiltersats på någon egenskap.

* Fråga utan filter.

Frågor som läser data från en eller flera partitioner medför högre svarstid och förbrukar ett högre antal begärandeenheter. Eftersom varje partition har automatisk indexering för alla egenskaper kan frågan hanteras effektivt från indexet. Du kan göra frågor som använder flera partitioner snabbare med hjälp av parallellismalternativen. Mer information om partitionering och partitionsnycklar finns [i Partitionering i Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Utvärdera begäranhetsavgift för en fråga

När du har lagrat vissa data i dina Azure Cosmos-behållare kan du använda Data Explorer i Azure-portalen för att konstruera och köra dina frågor. Du kan också få kostnaden för frågorna med hjälp av datautforskaren. Den här metoden ger dig en känsla av de faktiska avgifter som är inblandade i typiska frågor och åtgärder som ditt system stöder.

Du kan också få kostnaden för frågor programmässigt med hjälp av SDK: er. Om du vill mäta omkostnaderna för en åtgärd `x-ms-request-charge` som att skapa, uppdatera eller ta bort inspekterar du huvudet när du använder REST API. Om du använder .NET eller Java SDK är egenskapen `RequestCharge` motsvarande egendom för att få begärssedan och den här egenskapen finns i ResourceResponse eller FeedResponse.

```csharp
// Measure the performance (request units) of writes 
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument); 

Console.WriteLine("Insert of an item consumed {0} request units", response.RequestCharge); 

// Measure the performance (request units) of queries 
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery(); 

while (queryable.HasMoreResults) 
     { 
          FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
          Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge); 
     }
```

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Faktorer som påverkar begäranhetsenhetsavgift för en fråga

Begäranheter för frågor är beroende av ett antal faktorer. Till exempel antalet Azure Cosmos-objekt som lästs in/returnerats, antalet sökninger mot indexet, frågekompileringstiden etc. information. Azure Cosmos DB garanterar att samma fråga när den körs på samma data alltid förbrukar samma antal begärandeenheter även med upprepade körningar. Frågeprofilen med hjälp av frågekörningsmått ger dig en god uppfattning om hur begäranheterna används.  

I vissa fall kan du se en sekvens av 200 och 429 svar och variabla begärandeenheter i en sidsida körning av frågor, det vill säga eftersom frågor körs så snabbt som möjligt baserat på tillgängliga ru: er. Du kan se en frågekörning brytas till flera sidor /rundresor mellan server och klient. Till exempel kan 10 000 objekt returneras som flera sidor, var och en debiteras baserat på den beräkning som utförs för den sidan. När du summerar över dessa sidor bör du få samma antal ru:er som du skulle få för hela frågan.  

## <a name="metrics-for-troubleshooting"></a>Mått för felsökning

Prestanda och dataflöde som förbrukas av frågor, användardefinierade funktioner (UDFs) beror oftast på funktionstexten. Det enklaste sättet att ta reda på hur mycket tid frågekörningen spenderas i UDF och antalet ru:er som förbrukas är att aktivera frågemåtten. Om du använder .NET SDK här är exempelfrågemått som returneras av SDK:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

## <a name="best-practices-to-cost-optimize-queries"></a>Metodtips för kostnadsoptimering av frågor 

Tänk på följande metodtips när du optimerar frågor för kostnad:

* **Samlokalisera flera entitetstyper**

   Försök att samlokalisera flera entitetstyper inom ett enskilt eller mindre antal behållare. Den här metoden ger fördelar inte bara ur ett prisperspektiv, utan även för frågekörning och transaktioner. Frågor begränsas till en enda behållare. och atomtransaktioner över flera poster via lagrade procedurer/utlösare begränsas till en partitionsnyckel i en enda behållare. Samlokalisera entiteter i samma behållare kan minska antalet nätverksrundturer för att lösa relationer mellan poster. Så det ökar end-to-end prestanda, möjliggör atomära transaktioner över flera poster för en större datauppsättning, och som ett resultat sänker kostnaderna. Om det är svårt för ditt scenario att samlokalisera flera entitetstyper inom ett enskilt eller mindre antal behållare, oftast för att du migrerar ett befintligt program och du inte vill göra några kodändringar - bör du överväga att etablera dataflödet på databasnivå.  

* **Mäta och justera för lägre begäranheter/andra användning**

   Komplexiteten i en fråga påverkar hur många begärandeenheter (RU: er) förbrukas för en åtgärd. Antalet predikat, typ av predikat, antal UDF:er och storleken på källdatauppsättningen. Alla dessa faktorer påverkar kostnaden för frågeåtgärder. 

   Begärsavgift som returneras i begärandehuvudet anger kostnaden för en viss fråga. Om en fråga till exempel returnerar 1000 1 KB-artiklar är kostnaden för åtgärden 1000. Som sådan, inom en sekund, servern respekterar endast två sådana förfrågningar innan kurs begränsa efterföljande begäranden. Mer information finns i artikeln [för begärandeenheter](request-units.md) och räknaren för begäranheten. 

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta att lära dig mer om kostnadsoptimering i Azure Cosmos DB med följande artiklar:

* Läs mer om [hur Azure Cosmos-priser fungerar](how-pricing-works.md)
* Läs mer om [Optimering för utveckling och testning](optimize-dev-test.md)
* Läs mer om [att förstå din Azure Cosmos DB-faktura](understand-your-bill.md)
* Läs mer om [att optimera kostnaden för dataflöde](optimize-cost-throughput.md)
* Läs mer om [att optimera lagringskostnaden](optimize-cost-storage.md)
* Läs mer om [att optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [att optimera kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Läs mer om [reserverad kapacitet för Azure Cosmos DB](cosmos-db-reserved-capacity.md)


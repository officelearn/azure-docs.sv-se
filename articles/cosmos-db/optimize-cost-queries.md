---
title: Optimera enheter för programbegäran och kostnaden för att köra frågor i Azure Cosmos DB
description: Lär dig att utvärdera begäran units för en fråga och optimera frågor vad gäller prestanda och kostnad.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: e2cb1a76bfde5bea7707cf3cc6658e3fc82163c5
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54043098"
---
# <a name="optimize-the-cost-required-to-run-queries-in-azure-cosmos-db"></a>Optimera kostnader som krävs för att köra frågor i Azure Cosmos DB

Azure Cosmos DB erbjuder en omfattande uppsättning databasoperationer, inklusive Relations- och Hierarkifrågor frågor som körs på objekt i en behållare. Den kostnad som hör till var och en av dessa operationer varierar beroende på CPU, IO och minne som krävs för att slutföra åtgärden. I stället för att tänka på och hantera maskinvaruresurser kan tänka du på en begäransenhet (RU) som det enda måttet på de resurser som krävs för att utföra olika databasoperationer för att leverera en begäran. Den här artikeln beskriver hur du utvärderar begäran units för en fråga och optimera frågor vad gäller prestanda och kostnad. 

Frågor i Azure Cosmos DB är vanligtvis från snabbaste/mest effektiva till långsammare/mindre effektiva när det gäller dataflöde följande ordning:  

* Åtgärd för hämtning på en enda partitionsnyckel och Objektnyckel.

* Fråga med en filtersats inom en enda partitionsnyckel.

* Fråga utan en filtersats för likhet eller ett intervall om en egenskap.

* Fråga utan filter.

Frågor som läser data från en eller flera partitioner medföra svarstider och högre antal enheter för programbegäran att använda. Eftersom varje partition har automatisk indexering för alla egenskaper, kan frågan hanteras effektivt från indexet. Du kan göra frågor som använder flera partitioner snabbare genom att använda parallellitet. Mer information om partitionering och partitionsnycklar finns [partitionering i Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Utvärdera kostnaden för begäran-enheten för en fråga

När du har lagrat vissa data i dina Azure Cosmos-behållare, kan du använda Datautforskaren i Azure-portalen för att skapa och köra dina frågor. Du kan också få kostnaden för frågorna med hjälp av datautforskaren. Den här metoden ger dig en uppfattning om de faktiska kostnader som ingår i vanliga frågor och åtgärder som har stöd för ditt system.

Du kan också få kostnaden för frågor via programmering med hjälp av SDK: erna. För att mäta arbetet med att alla åtgärder, till exempel som att skapa, uppdatera eller ta bort granska den `x-ms-request-charge` rubrik när du använder REST API. Om du använder .net eller Java-SDK på `RequestCharge` egenskapen är motsvarande egenskap att hämta kostnad för begäran och den här egenskapen finns i ResourceResponse eller FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Faktorer som påverkar enheter för att ta betalt för en fråga

Begäransenheter för frågor är beroende av ett antal faktorer. Till exempel antalet objekt som Azure Cosmos lästs in/returneras, antal sökningar mot indexet kompileringstid fråga osv information. Azure Cosmos DB garanterar att samma fråga när den körs på samma data alltid kommer att använda samma antal enheter för programbegäran även med upprepningar körningar. Fråga-profil med hjälp av frågan körningsstatistik ger dig en uppfattning om hur du har använt enheter för programbegäran.  

I vissa fall kan du se en sekvens med 200 429 svar och variabeln begäransenheter i en växlade körning av frågor, som beror på att frågor körs så snabbt som möjligt baserat på tillgängliga ru: er. Du kan se en Frågekörningen dela i flera sidor/nätverksförfrågningar mellan servern och klienten. Till exempel kan 10 000 objekt returneras som flera sidor, var och en debiteras baserat på beräkningen utförs för sidan. När du summor över dessa sidor, bör du få samma antal ru: er som du skulle få för hela frågan.  

## <a name="metrics-for-troubleshooting"></a>Mätvärden för felsökning

Prestanda och dataflöde som används av frågor, användardefinierade funktioner (UDF) huvudsakligen beror på själva funktionen. Det enklaste sättet att ta reda på hur mycket tid körningen av frågan är i en användardefinierad funktion och antalet mediereserverade enheter används, är genom att aktivera mätvärden för frågan. Om du använder .net SDK, här är exempel fråga mätvärden som returneras av SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Bästa praxis för att kosta optimera frågor 

Överväg följande metodtips när du optimerar frågor för kostnad:

* **Samordna flera typer av enheter**

   Försök att samordna flera typer av enheter inom en enda eller mindre antal behållare. Den här metoden ger fördelar, inte bara ur en prissättning, utan också för frågekörning och transaktioner. Frågorna är begränsade till en enskild behållare. och atomiska transaktioner över flera poster via lagrade procedurer/utlösare är begränsade till en partitionsnyckel inom en enskild behållare. Samordna entiteter i samma behållare kan minska antalet nätverk tur och RETUR för att lösa relationer för poster. Så den ger högre prestanda för slutpunkt till slutpunkt, aktiverar atomiska transaktioner över flera poster för en större datauppsättning, och därmed sänker kostnaderna. Om samordna flera typer av enheter inom en enda eller mindre antal behållare är svårt för ditt scenario, vanligtvis eftersom du migrerar ett befintligt program och du inte vill göra några ändringar i koden – du bör du överväga att etablering dataflöde på databasnivå.  

* **Mät och justering för lägre begäran begärandeenheter/sekund användning**

   Komplexiteten för en fråga påverkar hur många enheter för programbegäran (ru) används för en åtgärd. Antalet predikat, natur predikat, antal UDF: er och storleken på datauppsättningen för källan. Dessa faktorer påverkar kostnaden för frågeåtgärder. 

   Kostnad för begäran som returnerades i rubriken anger kostnaden för en viss fråga. Om en fråga returnerar 1000 1 KB-artiklar, är kostnaden för åtgärden 1000. Inom en sekund godkänner servern därför bara två sådana begäranden innan hastighet som begränsar efterföljande förfrågningar. Mer information finns i [programbegäran](request-units.md) artikeln och begäran enhet Kalkylatorn. 

## <a name="next-steps"></a>Nästa steg

Därefter kan du fortsätta till mer information om kostnadsoptimeringar i Azure Cosmos DB i följande artiklar:

* Läs mer om [hur Azure Cosmos fungerar prissättningen](how-pricing-works.md)
* Läs mer om [Optimera för utveckling och testning](optimize-dev-test.md)
* Läs mer om [förstå din faktura för Azure Cosmos DB](understand-your-bill.md)
* Läs mer om [optimera dataflödet kostnad](optimize-cost-throughput.md)
* Läs mer om [optimera kostnaden för lagring](optimize-cost-storage.md)
* Läs mer om [optimera kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om [optimera kostnaden för Azure Cosmos-konton för flera regioner](optimize-cost-regions.md)
* Läs mer om [Azure Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)


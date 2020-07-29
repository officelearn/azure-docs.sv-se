---
title: Optimera kostnader och RU/s för att köra frågor i Azure Cosmos DB
description: Lär dig hur du utvärderar begär ande enhets avgifter för en fråga och optimerar frågan med avseende på prestanda och kostnad.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/24/2020
ms.openlocfilehash: e1c60542ec16ca19d26a77c1b9fb9676cf875e3d
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87318274"
---
# <a name="optimize-query-cost-in-azure-cosmos-db"></a>Optimera kostnaden för frågor i Azure Cosmos DB

Azure Cosmos DB erbjuder en omfattande uppsättning databasåtgärder, inklusive relationella och hierarkiska frågor som används för objekten i en behållare. Den kostnad som hör till var och en av dessa operationer varierar baserat på vilken CPU, vilka IO-resurser och hur mycket minne som krävs för att slutföra operationen. Istället för att tänka på och hantera maskinvaruresurser kan du tänka på en begäransenhet som det enda måttet på de resurser som krävs för att utföra olika databasoperationer och tillgodose en begäran. Den här artikeln beskriver hur du utvärderar kostnader för enheter för programbegäran och optimerar frågan avseende prestanda och kostnad.

Läsningar i Azure Cosmos DB beställs vanligt vis från snabbast/mest effektiva till långsammare/mindre effektiva i form av data flöde enligt följande:  

* Punkt läsningar (nyckel/värde-sökning för ett enskilt objekt-ID och partitionsnyckel).

* Fråga med en filter-sats inom en enskild partitionsnyckel.

* Fråga utan en likhets-eller Range filter-sats för en egenskap.

* Fråga utan filter.

Eftersom nyckel/värde-sökningar i objekt-ID är den mest effektiva typen av läsning bör du se till att objekt-ID: t har ett meningsfullt värde.

Frågor som läser data från en eller flera partitioner medför högre latens och använder ett högre antal enheter för programbegäran. Eftersom varje partition har automatisk indexering för alla egenskaper kan frågan behandlas effektivt från indexet. Du kan göra frågor som använder flera partitioner snabbare med hjälp av alternativen för parallellitet. Mer information om partitionering och partitionerings nycklar finns i [partitionering i Azure Cosmos DB](partitioning-overview.md).

## <a name="evaluate-request-unit-charge-for-a-query"></a>Utvärdera enhets avgiften för begäran för en fråga

När du har lagrat några data i dina Azure Cosmos-behållare kan du använda Datautforskaren i Azure Portal för att skapa och köra dina frågor. Du kan också få kostnaden för frågorna genom att använda data Utforskaren. Med den här metoden får du en uppfattning om de faktiska avgifterna som ingår i vanliga frågor och åtgärder som systemet stöder.

Du kan också få kostnaden för frågor via programmering med SDK: er. För att mäta omkostnader för en åtgärd, till exempel skapa, uppdatera eller ta bort, inspekterar du `x-ms-request-charge` sidhuvudet när du använder REST API. Om du använder .NET eller Java SDK `RequestCharge` är egenskapen motsvarande egenskap för att hämta begär ande avgiften och den här egenskapen finns i ResourceResponse eller FeedResponse.

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

## <a name="factors-influencing-request-unit-charge-for-a-query"></a>Faktorer som påverkar begär ande av enhets avgifter för en fråga

Enheter för programbegäran för frågor är beroende av ett antal faktorer. Till exempel antalet inlästa/returnerade Azure Cosmos-objekt, antalet uppslag mot indexet, tiden för frågans kompilering osv. Azure Cosmos DB garanterar att samma fråga när den körs på samma data alltid kommer att förbruka samma antal enheter för programbegäran även med upprepad körning. Du får en bra uppfattning om hur enheter för programbegäran används i frågan profil med hjälp av körnings mått för frågor.  

I vissa fall kan du se en sekvens med 200-och 429-svar, och variabla enheter för programbegäran i en växlad körning av frågor, det vill säga att frågor körs så snabbt som möjligt baserat på tillgängliga ru: er. Du kan se att en frågekörningen bryts i flera sidor/avrunda resor mellan server och klient. Till exempel kan 10 000 objekt returneras som flera sidor, varje debiteras baserat på den beräkning som utförs för den sidan. När du summerar över dessa sidor bör du få samma antal ru: er som du skulle få för hela frågan.  

## <a name="metrics-for-troubleshooting"></a>Mått för fel sökning

De prestanda och data flöden som används av frågor, användardefinierade funktioner (UDF: er) är huvudsakligen beroende av funktions texten. Det enklaste sättet att ta reda på hur lång tid det tar att köra frågekörningen i UDF och antalet förbrukade ru: er, är att aktivera frågans mått. Om du använder .NET SDK är här exempel frågans mått som returneras av SDK:

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

## <a name="best-practices-to-cost-optimize-queries"></a>Bästa metoder för att kostnads optimera frågor 

Tänk på följande rekommendationer när du optimerar frågor för kostnad:

* **Samplacera flera typer av enheter**

   Försök att samplacera flera entitetstyper inom ett enda eller mindre antal behållare. Den här metoden ger förmåner inte bara från ett pris perspektiv, utan även för frågekörning och transaktioner. Frågor är begränsade till en enda behållare. och atomiska transaktioner över flera poster via lagrade procedurer/utlösare är begränsade till en partitionsnyckel inom en enda behållare. Att placera entiteter i samma behållare kan minska antalet nätverks fördröjningar för att lösa relationer mellan poster. Det ökar prestandan från slut punkt till slut punkt, som möjliggör atomiska transaktioner över flera poster för en större data uppsättning och därmed lägre kostnader. Om samplacering av flera entitetstyper inom ett enda eller mindre antal behållare är svårt för ditt scenario, vanligt vis på grund av att du migrerar ett befintligt program och inte vill göra några kod ändringar, bör du överväga att konfigurera data flödet på databas nivå.  

* **Mått och justering för lägre enheter för programbegäran/andra användning**

   Komplexiteten i en fråga påverkar hur många ru: er (Request units) som används för en åtgärd. Antalet predikat, typen av predikat, antalet UDF: er och storleken på käll data uppsättningen. Alla dessa faktorer påverkar kostnaderna för frågor. 

   Begär ande avgift som returneras i rubriken för begäran visar kostnaden för en specifik fråga. Om en fråga till exempel returnerar 1000 1 KB-objekt är kostnaden för åtgärden 1000. I och med den här servern bevarar servern bara två sådana begär Anden innan hastigheten begränsar efterföljande begär Anden. Mer information finns i artikeln om [enheter för programbegäran](request-units.md) och Kalkylatorn för begär ande enheter. 

## <a name="next-steps"></a>Nästa steg

Härnäst kan du fortsätta med att lära dig mer om kostnads optimering i Azure Cosmos DB med följande artiklar:

* Lär dig mer om [hur Azure Cosmos-prissättning fungerar](how-pricing-works.md)
* Läs mer om [optimering för utveckling och testning](optimize-dev-test.md)
* Lär dig mer om [att förstå din Azure Cosmos DB faktura](understand-your-bill.md)
* Läs mer om hur du [optimerar data flödes kostnaden](optimize-cost-throughput.md)
* Läs mer om hur du [optimerar lagrings kostnader](optimize-cost-storage.md)
* Läs mer om hur [du optimerar kostnaden för läsningar och skrivningar](optimize-cost-reads-writes.md)
* Läs mer om hur [du optimerar kostnaden för Azure Cosmos-konton med flera regioner](optimize-cost-regions.md)
* Läs mer om [Azure Cosmos DB reserverad kapacitet](cosmos-db-reserved-capacity.md)


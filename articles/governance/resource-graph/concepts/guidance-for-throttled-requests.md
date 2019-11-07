---
title: Vägledning för begränsade begäranden
description: Lär dig att skapa bättre frågor för att undvika att förfrågningar till Azure Resource Graph begränsas.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/18/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 2dea1c160b07ac08075dad3a1ca1f6fc753e3481
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622658"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Vägledning för begränsade begär anden i Azure Resource Graph

När du skapar program mässig och frekvent användning av data i Azure Resource Graph bör du tänka på hur begränsningen påverkar resultatet av frågorna. Att ändra hur data begärs kan hjälpa dig och din organisation att undvika att begränsas och upprätthålla data flödet för dina Azure-resurser.

Den här artikeln beskriver fyra områden och mönster som rör skapandet av frågor i Azure Resource Graph:

- Förstå begränsnings rubriker
- Batching-frågor
- Sprid frågor
- Effekten av sid brytning

## <a name="understand-throttling-headers"></a>Förstå begränsnings rubriker

Azure Resource Graph allokerar kvot nummer för varje användare baserat på ett tids periods fönster. En användare kan till exempel skicka högst 15 frågor inom varje 5-sekunders period utan att vara begränsad. Kvot svärdet bestäms av många faktorer och kan komma att ändras.

I varje fråge svar lägger Azure Resource Graph till två begränsnings rubriker:

- `x-ms-user-quota-remaining` (int): den återstående resurs kvoten för användaren. Det här värdet mappar till antal frågor.
- `x-ms-user-quota-resets-after` (hh: mm: SS): tids perioden tills en användares kvot förbrukning återställs.

För att illustrera hur rubrikerna fungerar, ska vi titta på ett fråge svar som har sidhuvud och värden för `x-ms-user-quota-remaining: 10` och `x-ms-user-quota-resets-after: 00:00:03`.

- Inom de kommande 3 sekunderna kan högst 10 frågor skickas utan begränsning.
- I 3 sekunder kommer värdena för `x-ms-user-quota-remaining` och `x-ms-user-quota-resets-after` att återställas till `15` respektive `00:00:05`.

Om du vill se ett exempel på hur du använder rubrikerna för att _backoff_ på fråge förfrågningar, se exemplet i [query parallellt](#query-in-parallel).

## <a name="batching-queries"></a>Batching-frågor

Batching-frågor av prenumerationen, resurs gruppen eller enskilda resurser är mer effektiva än parallella frågor. Kvot kostnaden för en större fråga är ofta lägre än kvot kostnaden för många små och riktade frågor. Batchstorleken bör vara mindre än _300_.

- Exempel på en dåligt optimerad metod

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exempel på #1 av en optimerad batching-metod

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exempel på #2 av en optimerad batching-metod

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Sprid frågor

På grund av hur begränsningen upprätthålls rekommenderar vi att frågor översätts till varandra. Det vill säga, i stället för att skicka 60-frågor samtidigt, ska du sprida frågorna till fyra andra fönster i 5:

- Schema för icke-spridd fråga

  | Antal frågor         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Tidsintervall (SEK) | 0-5 | 5-10 | 10-15 | 15-20 |

- Schema för överdelad fråga

  | Antal frågor         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Tidsintervall (SEK) | 0-5 | 5-10 | 10-15 | 15-20 |

Nedan visas ett exempel på hur du kan följa upp begränsnings rubriker när du frågar Azure Resource Graph:

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>Fråga parallellt

Även om batching rekommenderas över parallellisering, finns det tillfällen där frågor inte kan grupperas. I dessa fall kanske du vill fråga Azure Resource Graph genom att skicka flera frågor på ett parallellt sätt. Nedan visas ett exempel på hur du _backoff_ baserat på begränsnings rubriker i sådana scenarier:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>Sidbrytning

Eftersom Azure Resource Graph returnerar högst 1000 poster i ett enda fråge svar, kan du behöva [fylla i frågorna](./work-with-data.md#paging-results) för att få den fullständiga data uppsättningen som du letar efter. Vissa Azure Resource Graph-klienter hanterar dock sid brytning annorlunda än andra.

- C#-SDK

  När du använder ResourceGraph SDK måste du hantera sid brytning genom att skicka Skip-token som returneras från föregående fråge svar till nästa sid brytnings fråga. Den här designen innebär att du måste samla in resultat från alla sid brytnings anrop och kombinera dem tillsammans i slutet. I det här fallet tar varje sid brytnings fråga som du skickar en fråga-kvot:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI/Azure PowerShell

  När du använder antingen Azure CLI eller Azure PowerShell, fylls frågor till Azure Resource Graph automatiskt i för att hämta högst 5000 poster. Frågeresultaten returnerar en kombinerad lista med poster från alla sid brytnings anrop. I det här fallet, beroende på antalet poster i frågeresultatet, kan en enskild sid brytnings fråga förbruka mer än en fråga-kvot. I exemplet nedan kan till exempel en enskild körning av frågan använda upp till fem frågesträng:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Är du fortfarande begränsad?

Om du får en begränsning efter ovanstående rekommendationer kan du kontakta teamet på [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Ange följande information:

- Din specifika användnings fall och affärs driv rutin behöver en högre begränsnings gräns.
- Hur många resurser har du åtkomst till? Hur många av returneras från en enda fråga?
- Vilka typer av resurser är du intresse rad av?
- Vad är ditt fråge mönster? X-frågor per Y-sekunder osv.

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Start frågor](../samples/starter.md).
- Se avancerade användnings områden i [avancerade frågor](../samples/advanced.md).
- Lär dig mer om hur du [utforskar resurser](explore-resources.md).
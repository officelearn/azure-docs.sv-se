---
title: Vägledning för begränsade begäranden
description: Lär dig att skapa bättre frågor för att undvika begäranden till Azure Resource-diagram från begränsas.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276904"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Vägledning för begränsade begäranden i Azure Resource-diagram

När du skapar programmässiga och ofta användning av Azure Resource diagramdata, ska överväganden göras för hur begränsning påverkan resultatet av frågorna. Ändra hur data begärs kan hjälpa dig och din organisation undvika begränsas och underhålla flödet av aktuella data om dina Azure-resurser.

Den här artikeln innehåller fyra områden och mönster som rör skapandet av frågor i Azure Resource-diagram:

- Förstå begränsningar rubriker
- Batchbearbetning frågor
- Häpnadsväckande frågor
- Effekten av sidbrytning

## <a name="understand-throttling-headers"></a>Förstå begränsningar rubriker

Azure Resource Graph allokerar kvot antalet för varje användare baserat på ett tidsintervall. En användare kan till exempel skicka högst 15 frågor inom var 5-sekundersintervall period utan begränsas. Kvotvärdet bestäms av många faktorer och kan komma att ändras.

Lägger till två begränsning rubriker i varje fråga svar Azure Resource-diagram:

- `x-ms-user-quota-remaining` (int): Återstående Resurskvot för användaren. Det här värdet motsvarar antalet förfrågningar.
- `x-ms-user-quota-resets-after` (: mm: ss): Tidslängd tills en användares kvot förbrukning återställs.

För att illustrera hur rubrikerna fungerar, ska vi titta på något svar med rubrik och värdena för `x-ms-user-quota-remaining: 10` och `x-ms-user-quota-resets-after: 00:00:03`.

- I nästa 3 sekunder, kan högst 10 frågor skickas utan att begränsas.
- I 3 sekunder, värdena för `x-ms-user-quota-remaining` och `x-ms-user-quota-resets-after` kommer att återställas till `15` och `00:00:05` respektive.

Se ett exempel på hur du använder rubriker för att _backoff_ på frågebegäranden, se exemplet i [frågan parallellt](#query-in-parallel).

## <a name="batching-queries"></a>Batchbearbetning frågor

Batchbearbetning frågor från prenumerationen, resursgruppen eller enskilda resursen är effektivare än att parallellisera frågor. Kvot kostnaden för en större fråga är ofta mindre än kvot kostnaden för många små och riktade frågor. Batchstorleken bör vara mindre än _300_.

- Exempel på ett dåligt optimerad metoden

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- Exempel #1 på en optimerad batchbearbetningen metod

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
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exempel #2 på en optimerad batchbearbetningen metod

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
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Häpnadsväckande frågor

På grund av hur begränsning tillämpas rekommenderar vi frågor blir successiva. Det vill säga skickas 60 frågor på samma gång, i stället för sprida frågorna i fyra 5-sekundersintervall windows:

- Icke-ut fråga schema

  | Antal frågor         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Tidsintervall (sek) | 0-5 | 5-10 | 10-15 | 15-20 |

- Ut fråga schema

  | Antal frågor         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Tidsintervall (sek) | 0-5 | 5-10 | 10-15 | 15-20 |

Nedan visas ett exempel på respekterar begränsningen rubriker vid frågor om Azure Resource-diagram:

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

### <a name="query-in-parallel"></a>Köra parallella frågor

Även om batchbearbetning rekommenderas framför parallellisering, finns det tillfällen där frågor inte kan enkelt batchhanteras. I dessa fall kan du fråga Azure Resource grafen genom att skicka flera frågor i en parallell sätt. Nedan visas ett exempel på hur du _backoff_ baserat på begränsning rubriker i sådana scenarier:

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

Eftersom Azure Resource Graph returnerar högst 1000 poster i en enda frågesvaret, du kan behöva [sidbryta](./work-with-data.md#paging-results) dina frågor att hämta den fullständiga datauppsättningen som du letar efter. Men hantera vissa klienter med Azure Resource Graph sidbrytning annorlunda än andra.

- C#-SDK

  När du använder ResourceGraph SDK kan behöva du hantera sidbrytning genom att skicka hoppa över token som returneras från föregående frågesvaret till nästa sidnumrerade fråga. Den här designen innebär att du behöver samla in resultaten från alla sidnumrerade anrop och kombinera dem tillsammans i slutet. I det här fallet tar varje sidnumrerade frågan som du skickar en fråga kvot:

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
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

- Azure CLI / Azure PowerShell

  När du använder Azure CLI eller Azure PowerShell, sidnumrerade frågor till Azure Resource Graph automatiskt för att hämta högst 5000 posterna. Resultatet av frågan returnerar en kombinerad lista över poster från alla sidnumrerade anrop. Beroende på antalet poster i frågeresultatet, i det här fallet förbrukar en fråga som sidnumrerade mer än en fråga kvot. I exemplet nedan, exempelvis använda en enda körning av frågan upp till fem fråga kvot:

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>Fortfarande begränsas?

Om du är komma begränsad efter utöva ovanstående rekommendationer, kontakta teamet på [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com).

Ange följande information:

- Din specifika användningsfall och business-drivrutinen behöver för en högre gränsen.
- Hur många resurser har du åtkomst till? Hur många av är returnerats från en enda fråga?
- Vilka typer av resurser är du intresserad av?
- Vad är mönstret fråga? X-frågor per Y sekunder osv.

## <a name="next-steps"></a>Nästa steg

- Se språket som användes i [Starter frågor](../samples/starter.md).
- Se avancerade använder i [avancerade frågor](../samples/advanced.md).
- Lär dig hur du [Utforska resurser](explore-resources.md).
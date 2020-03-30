---
title: Vägledning för begränsade begäranden
description: Lär dig att gruppera, fördela, paginera och fråga parallellt för att undvika att begäranden begränsas av Azure Resource Graph.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259856"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Vägledning för begränsade begäranden i Azure Resource Graph

När du skapar programmatiska och frekventa användning av Azure Resource Graph-data bör hänsyn tas till hur begränsning påverkar resultatet av frågorna. Om du ändrar hur data efterfrågas kan du och din organisation undvika att begränsas och underhålla flödet av snabbdata om dina Azure-resurser.

Den här artikeln innehåller fyra områden och mönster som är relaterade till skapandet av frågor i Azure Resource Graph:

- Förstå begränsningsrubriker
- Gruppera frågor
- Spridning av frågor
- Effekterna av sidnumrering

## <a name="understand-throttling-headers"></a>Förstå begränsningsrubriker

Azure Resource Graph allokerar kvotnummer för varje användare baserat på ett tidsfönster. En användare kan till exempel skicka högst 15 frågor inom varje 5-sekundersfönster utan att begränsas. Kvotvärdet bestäms av många faktorer och kan komma att ändras.

I varje frågesvar lägger Azure Resource Graph till två begränsningshuvuden:

- `x-ms-user-quota-remaining`(int): Den återstående resurskvoten för användaren. Det här värdet mappar till frågeantal.
- `x-ms-user-quota-resets-after`(hh:mm:ss): Tidslängden tills en användares kvotförbrukning återställs.

Om du vill illustrera hur rubrikerna fungerar ska vi titta på `x-ms-user-quota-remaining: 10` ett `x-ms-user-quota-resets-after: 00:00:03`frågesvar som har huvudet och värdena för och .

- Inom de närmaste 3 sekunderna kan högst 10 frågor skickas in utan att begränsas.
- `x-ms-user-quota-remaining` Om 3 sekunder återställs `x-ms-user-quota-resets-after` och återställs värdena för `15` `00:00:05` respektive.

Ett exempel på hur du använder rubrikerna för att _backa_ från frågebegäranden finns i exemplet [i Fråga i parallell](#query-in-parallel).

## <a name="grouping-queries"></a>Gruppera frågor

Att gruppera frågor efter prenumeration, resursgrupp eller enskild resurs är effektivare än att parallellisera frågor. Kvotkostnaden för en större fråga är ofta mindre än kvotkostnaden för många små och riktade frågor. Gruppstorleken rekommenderas att vara mindre än _300_.

- Exempel på ett dåligt optimerat tillvägagångssätt

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

- Exempel #1 av en optimerad grupperingsmetod

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- Exempel #2 en optimerad grupperingsmetod för att hämta flera resurser i en fråga

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>Spridning av frågor

På grund av hur begränsning tillämpas rekommenderar vi frågor som ska fördelas. Det vill än att skicka 60 frågor samtidigt, fördela frågorna i fyra 5-sekundersfönster:

- Icke-förskjutet frågeschema

  | Antal frågor         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | Tidsintervall (sek) | 0-5 | 5-10 | 10-15 | 15-20 |

- Förskjutet frågeschema

  | Antal frågor         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | Tidsintervall (sek) | 0-5 | 5-10 | 10-15 | 15-20 |

Nedan följer ett exempel på respekt för begränsningshuvuden när du frågar Azure Resource Graph:

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

Även om gruppering rekommenderas över parallellisering finns det tillfällen där frågor inte lätt kan grupperas. I dessa fall kanske du vill fråga Azure Resource Graph genom att skicka flera frågor på ett parallellt sätt. Nedan följer ett exempel på hur du _backar_ baserat på begränsningshuvuden i sådana scenarier:

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

## <a name="pagination"></a>Sidnumrering

Eftersom Azure Resource Graph returnerar högst 1000 poster i ett enda frågesvar kan du behöva [paginera](./work-with-data.md#paging-results) dina frågor för att få den fullständiga datauppsättningen du letar efter. Vissa Azure Resource Graph-klienter hanterar dock sidnumrering på ett annat sätt än andra.

- C#-SDK

  När du använder ResourceGraph SDK måste du hantera sidnumrering genom att skicka hoppa token som returneras från föregående frågesvar till nästa sidnumrerade fråga. Denna design innebär att du måste samla in resultat från alla sidnumrerade samtal och kombinera dem tillsammans i slutet. I det här fallet tar varje sidnumrerad fråga som du skickar en frågekvot:

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

- Azure CLI / Azure PowerShell

  När du använder antingen Azure CLI eller Azure PowerShell sidnumrerar frågor till Azure Resource Graph automatiskt för att hämta högst 5000 poster. Frågeresultatet returnerar en kombinerad lista med poster från alla sidnumrerade anrop. I det här fallet, beroende på antalet poster i frågeresultatet, kan en enskild sidnumrerad fråga förbruka mer än en frågekvot. I exemplet nedan kan till exempel en enda körning av frågan förbruka upp till fem frågekvoter:

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>Fortfarande få strypt?

Om du får strypt efter att ha utövat ovanstående rekommendationer, kontakta teamet på [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com).

Lämna följande information:

- Din specifika användningsfall och affärsdrivrutin behöver för en högre begränsningsgräns.
- Hur många resurser har du åtkomst till? Hur många av de returneras från en enda fråga?
- Vilka typer av resurser är du intresserad av?
- Vad är frågemönstret? X frågor per Y sekunder etc.

## <a name="next-steps"></a>Nästa steg

- Se språket som används i [Starter-frågor](../samples/starter.md).
- Se avancerade användningsområden i [avancerade frågor](../samples/advanced.md).
- Läs mer om hur du [utforskar resurser](explore-resources.md).
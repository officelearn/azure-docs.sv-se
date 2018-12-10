---
title: Lär dig hur du hanterar konsekvens i Azure Cosmos DB
description: Lär dig hur du hanterar konsekvens i Azure Cosmos DB
services: cosmos-db
author: christopheranderson
ms.service: cosmos-db
ms.topic: sample
ms.date: 10/17/2018
ms.author: chrande
ms.openlocfilehash: 9d8c1296fc811d97dc9e7e66ad9bd9fdc79d66f9
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52634344"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Hantera konsekvensnivåer i Azure Cosmos DB

Den här artikeln förklarar hur du hanterar konsekvensnivåer i Azure Cosmos DB. Du lär dig att konfigurera standardkonsekvensnivån, åsidosätta standardkonsekvensen, manuellt hantera sessionstoken och förstå PBS-mått (probabilistiskt begränsad föråldring).

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

Standardkonsekvensnivån är den konsekvensnivå som klienter använder som standard. Klienter kan åsidosätta den.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Strong

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level BoundedStaleness
```

### <a name="powershell"></a>PowerShell

Det här exemplet skapar ett nytt Azure Cosmos DB-konto med flera original aktiverat i regionerna USA, östra och USA, västra. Standardkonsekvensprincipen ställs in på Session.

```azurepowershell-interactive
$locations = @(@{"locationName"="East US"; "failoverPriority"=0},
             @{"locationName"="West US"; "failoverPriority"=1})

$iprangefilter = ""

$consistencyPolicy = @{"defaultConsistencyLevel"="Session"}

$CosmosDBProperties = @{"databaseAccountOfferType"="Standard";
                        "locations"=$locations;
                        "consistencyPolicy"=$consistencyPolicy;
                        "ipRangeFilter"=$iprangefilter;
                        "enableMultipleWriteLocations"="true"}

New-AzureRmResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="portal"></a>Portalen

Om du vill visa eller ändra standardkonsekvensnivån loggar du in på Azure-portalen. Leta reda på ditt Azure Cosmos DB-konto och öppna fönsterrutan **Standardkonsekvens**. Välj den konsekvensnivå som du vill ha som den nya standarden, och välj sedan **Spara**.

![Konsekvensmeny på Azure-portalen](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Åsidosätta standardkonsekvensnivån

Klienter kan åsidosätta den standardkonsekvensnivå som anges av tjänsten. Det här alternativet kan anges för hela klienten eller per begäran.

### <a id="override-default-consistency-dotnet"></a>.NET SDK

```csharp
// Override consistency at the client level
ConsistencyPolicy consistencyPolicy = new ConsistencyPolicy
    {
        DefaultConsistencyLevel = ConsistencyLevel.BoundedStaleness,
        MaxStalenessIntervalInSeconds = 5,
        MaxStalenessPrefix = 100
    };
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, consistencyPolicy);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-java-async"></a>Java Async SDK

```java
// Override consistency at the client level
ConnectionPolicy policy = new ConnectionPolicy();

AsyncDocumentClient client =
        new AsyncDocumentClient.Builder()
                .withMasterKey(this.accountKey)
                .withServiceEndpoint(this.accountEndpoint)
                .withConsistencyLevel(ConsistencyLevel.Eventual)
                .withConnectionPolicy(policy).build();
```

### <a id="override-default-consistency-java-sync"></a>Java Sync SDK

```java
// Override consistency at the client level
ConnectionPolicy connectionPolicy = new ConnectionPolicy();
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Strong);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Strong
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Strong)
```

## <a name="utilize-session-tokens"></a>Använda sessionstoken

Om du vill hantera sessionstoken manuellt hämtar du sessionstoken från svaret och anger dem per begäran. Om du inte behöver hantera sessionstoken manuellt behöver du inte använda de här exemplen. SDK håller reda på sessionstoken automatiskt. Om du inte anger sessionstoken manuellt använder SDK som standard den senaste sessionstoken.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-java-async"></a>Java Async SDK

```java
// Get session token from response
RequestOptions options = new RequestOptions();
options.setPartitionKey(new PartitionKey(document.get("mypk")));
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
readObservable.single()           // we know there will be one response
  .subscribe(
      documentResourceResponse -> {
          System.out.println(documentResourceResponse.getSessionToken());
      },
      error -> {
          System.err.println("an error happened: " + error.getMessage());
      });

// Resume the session by setting the session token on RequestOptions
RequestOptions options = new RequestOptions();
requestOptions.setSessionToken(sessionToken);
Observable<ResourceResponse<Document>> readObservable = client.readDocument(document.getSelfLink(), options);
```

### <a id="utilize-session-tokens-java-sync"></a>Java Sync SDK

```java
// Get session token from response
ResourceResponse<Document> response = client.readDocument(documentLink, null);
String sessionToken = response.getSessionToken();

// Resume the session by setting the session token on the RequestOptions
RequestOptions options = new RequestOptions();
options.setSessionToken(sessionToken);
ResourceResponse<Document> response = client.readDocument(documentLink, options);
```

### <a id="utilize-session-tokens-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Get session token from response
const { headers, item } = await container.items.create({ id: "meaningful-id" });
const sessionToken = headers["x-ms-session-token"];

// Immediately or later, you can use that sessionToken from the header to resume that session.
const { body } = await item.read({ sessionToken });
```

### <a id="utilize-session-tokens-python"></a>Python SDK

```python
// Get the session token from the last response headers
item = client.ReadItem(item_link)
session_token = client.last_response_headers["x-ms-session-token"]

// Resume the session by setting the session token on the options for the request
options = {
    "sessionToken": session_token
}
item = client.ReadItem(doc_link, options)
```

## <a name="monitor-probabilistically-bounded-staleness-pbs-metric"></a>Övervaka PBS-mått (probabilistiskt begränsad föråldring)

Om du vill visa PBS-måttet går du till ditt Azure Cosmos DB-konto på Azure-portalen. Öppna fönsterrutan **Mått** och välj fliken **Konsekvens**. Titta på grafen med namnet **Probability of strongly consistent reads based on your workload (see PBS)** (Sannolikheten för starkt konsekventa läsningar baserat på din arbetsbelastning (se PBS)).

![PBS-graf i Azure-portalen](./media/how-to-manage-consistency/pbs-metric.png)

Använd menyn för mått i Azure Cosmos DB för att se det här måttet. Det visas inte gränssnittet för Azure Monitoring-mått.

## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hanterar datakonflikter eller gå vidare till nästa viktiga begrepp i Azure Cosmos DB. Se följande artiklar:

* [Hantera konflikter mellan regioner](how-to-manage-conflicts.md)
* [Partitionering och datadistribution](partition-data.md)

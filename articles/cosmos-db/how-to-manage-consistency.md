---
title: Lär dig hur du hanterar konsekvens i Azure Cosmos DB
description: 'Lär dig hur du konfigurerar och hanterar konsekvens nivåer i Azure Cosmos DB att använda Azure Portal, .NET SDK, Java SDK och diverse andra SDK: er'
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 46c4c4afe737fd11c2e711a23a3355f48dbd6231
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872085"
---
# <a name="manage-consistency-levels-in-azure-cosmos-db"></a>Hantera konsekvensnivåer i Azure Cosmos DB

Den här artikeln förklarar hur du hanterar konsekvensnivåer i Azure Cosmos DB. Du lär dig att konfigurera standardkonsekvensnivån, åsidosätta standardkonsekvensen, manuellt hantera sessionstoken och förstå PBS-mått (probabilistiskt begränsad föråldring).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-the-default-consistency-level"></a>Konfigurera standardkonsekvensnivån

[Standard konsekvens nivån](consistency-levels.md) är den konsekvens nivå som klienter använder som standard. Klienter kan alltid åsidosätta den.

### <a name="cli"></a>CLI

```bash
# create with a default consistency
az cosmosdb create --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Session

# update an existing account's default consistency
az cosmosdb update --name <name of Cosmos DB Account> --resource-group <resource group name> --default-consistency-level Eventual
```

### <a name="powershell"></a>PowerShell

Det här exemplet skapar ett nytt Azure Cosmos-konto med flera Skriv regioner aktiverade, i regionerna östra USA och västra USA. Standard konsekvens nivån är inställd på konsekvens av *sessioner* .

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

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
  -ApiVersion "2015-04-08" `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -Name "myCosmosDbAccount" `
  -Properties $CosmosDBProperties
```

### <a name="azure-portal"></a>Azure portal

Om du vill visa eller ändra standardkonsekvensnivån loggar du in på Azure-portalen. Hitta ditt Azure Cosmos-konto och öppna fönstret **standard konsekvens** . Välj den konsekvensnivå som du vill ha som den nya standarden, och välj sedan **Spara**. Azure Portal innehåller också en visualisering av olika konsekvens nivåer med noter. 

![Konsekvensmeny på Azure-portalen](./media/how-to-manage-consistency/consistency-settings.png)

## <a name="override-the-default-consistency-level"></a>Åsidosätta standardkonsekvensnivån

Klienter kan åsidosätta standardkonsekvensnivån som anges av tjänsten. Konsekvens nivå kan ställas in per begäran, vilket åsidosätter standard konsekvens nivån som angetts på konto nivå.

### <a id="override-default-consistency-dotnet"></a>.NET SDK V2

```csharp
// Override consistency at the client level
documentClient = new DocumentClient(new Uri(endpoint), authKey, connectionPolicy, ConsistencyLevel.Eventual);

// Override consistency at the request level via request options
RequestOptions requestOptions = new RequestOptions { ConsistencyLevel = ConsistencyLevel.Eventual };

var response = await client.CreateDocumentAsync(collectionUri, document, requestOptions);
```

### <a id="override-default-consistency-dotnet-v3"></a>.NET SDK V3

```csharp
// Override consistency at the request level via request options
ItemRequestOptions requestOptions = new ItemRequestOptions { ConsistencyLevel = ConsistencyLevel.Strong };

var response = await client.GetContainer(databaseName, containerName)
    .CreateItemAsync(
        item, 
        new PartitionKey(itemPartitionKey), 
        requestOptions);
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
DocumentClient client = new DocumentClient(accountEndpoint, accountKey, connectionPolicy, ConsistencyLevel.Eventual);
```

### <a id="override-default-consistency-javascript"></a>Node.js/JavaScript/TypeScript SDK

```javascript
// Override consistency at the client level
const client = new CosmosClient({
  /* other config... */
  consistencyLevel: ConsistencyLevel.Eventual
});

// Override consistency at the request level via request options
const { body } = await item.read({ consistencyLevel: ConsistencyLevel.Eventual });
```

### <a id="override-default-consistency-python"></a>Python SDK

```python
# Override consistency at the client level
connection_policy = documents.ConnectionPolicy()
client = cosmos_client.CosmosClient(self.account_endpoint, {
                                    'masterKey': self.account_key}, connection_policy, documents.ConsistencyLevel.Eventual)
```

## <a name="utilize-session-tokens"></a>Använda sessionstoken

En av konsekvens nivåerna i Azure Cosmos DB är konsekvens på *sessionen* . Detta är standard nivån som tillämpas på Cosmos-konton som standard. När du arbetar med konsekvens för *sessioner* använder klienten en session-token internt med varje Läs/fråga-begäran för att säkerställa att ange konsekvens nivå.

Om du vill hantera sessionstoken manuellt hämtar du sessionstoken från svaret och anger dem per begäran. Om du inte behöver hantera sessionstoken manuellt behöver du inte använda de här exemplen. SDK håller reda på sessionstoken automatiskt. Om du inte anger sessionstoken manuellt använder SDK som standard den senaste sessionstoken.

### <a id="utilize-session-tokens-dotnet"></a>.NET SDK V2

```csharp
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"));
string sessionToken = response.SessionToken;

RequestOptions options = new RequestOptions();
options.SessionToken = sessionToken;
var response = await client.ReadDocumentAsync(
                UriFactory.CreateDocumentUri(databaseName, collectionName, "SalesOrder1"), options);
```

### <a id="utilize-session-tokens-dotnet-v3"></a>.NET SDK V3

```csharp
Container container = client.GetContainer(databaseName, collectionName);
ItemResponse<SalesOrder> response = await container.CreateItemAsync<SalesOrder>(salesOrder);
string sessionToken = response.Headers.Session;

ItemRequestOptions options = new ItemRequestOptions();
options.SessionToken = sessionToken;
ItemResponse<SalesOrder> response = await container.ReadItemAsync<SalesOrder>(salesOrder.Id, new PartitionKey(salesOrder.PartitionKey), options);
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

Hur eventuell konsekvens fungerar? I genomsnitts fallet kan vi erbjuda inaktuella gränser med avseende på versions historik och-tid. Probabilistically-måttet för [**föråldrad (PBS)** ](https://pbs.cs.berkeley.edu/) försöker kvantifiera sannolikheten för inaktuellhet och visar det som ett mått. Om du vill visa PBS-måttet går du till ditt Azure Cosmos-konto i Azure Portal. Öppna fönstret **mått** och välj fliken **konsekvens** . titta på diagrammet med namnet **sannolikhet för starkt konsekventa läsningar baserat på din arbets belastning (se PBS)** .

![PBS-graf i Azure-portalen](./media/how-to-manage-consistency/pbs-metric.png)


## <a name="next-steps"></a>Nästa steg

Läs mer om hur du hanterar datakonflikter eller gå vidare till nästa viktiga begrepp i Azure Cosmos DB. Se följande artiklar:

* [Konsekvens nivåer i Azure Cosmos DB](consistency-levels.md)
* [Hantera konflikter mellan regioner](how-to-manage-conflicts.md)
* [Partitionering och datadistribution](partition-data.md)
* [Konsekvens i moderna distribuerade databas system design](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
* [Hög tillgänglighet](high-availability.md)
* [Azure Cosmos DB service avtal](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)

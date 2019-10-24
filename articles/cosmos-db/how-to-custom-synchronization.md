---
title: Så implementerar du anpassad synkronisering för att optimera för högre tillgänglighet och prestanda i Azure Cosmos DB
description: Lär dig hur du implementerar anpassad synkronisering för att optimera för högre tillgänglighet och prestanda i Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: 1fdd05f8a4757a49414a2a03c8f991a80186ed44
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755050"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementera anpassad synkronisering för att optimera för högre tillgänglighet och prestanda

Azure Cosmos DB erbjuder [fem väldefinierade konsekvens nivåer](consistency-levels.md) som du kan välja mellan för att utjämna kompromissen mellan konsekvens, prestanda och tillgänglighet. Stark konsekvens säkerställer att data synkront replikeras och varaktigt sparas i varje region där Azure Cosmos-kontot är tillgängligt. Den här konfigurationen ger den högsta nivån av hållbarhet, men kommer till kostnaden för prestanda och tillgänglighet. Om du vill att ditt program ska styra eller minska data hållbarheten för att passa programmets behov utan att kompromissa med tillgängligheten kan du använda *anpassad synkronisering* i program lagret för att uppnå den önskade livs längden.

Följande bild visar den anpassade synkroniserings modellen:

![Anpassad synkronisering](./media/how-to-custom-synchronization/custom-synchronization.png)

I det här scenariot replikeras en Azure Cosmos-behållare globalt över flera regioner på flera kontinenter. Att använda stark konsekvens för alla regioner i det här scenariot påverkar prestanda. För att säkerställa en högre nivå av data hållbarhet utan att kompromissa med Skriv fördröjning kan programmet använda två klienter som delar samma [sessionstoken](how-to-manage-consistency.md#utilize-session-tokens).

Den första klienten kan skriva data till den lokala regionen (till exempel USA, västra). Den andra klienten (till exempel i östra USA) är en Read-klient som används för att säkerställa synkroniseringen. Genom att flöda token från Skriv svaret till följande läsning, säkerställer läsningen synkroniseringen av skrivningar till östra USA. Azure Cosmos DB säkerställer att skrivningar visas i minst en region. De är garanterade att överleva ett regionalt avbrott om den ursprungliga Skriv regionen slutar fungera. I det här scenariot synkroniseras varje skrivning till östra USA, vilket minskar svars tiden för att använda stark konsekvens i alla regioner. I ett scenario med flera huvud, där skrivningar sker i varje region, kan du utöka den här modellen till att synkronisera till flera regioner parallellt.

## <a name="configure-the-clients"></a>Konfigurera klienterna

Följande exempel visar ett data åtkomst lager som instansierar två klienter för anpassad synkronisering:

### <a name="net-v2-sdk"></a>.NET v2 SDK
```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3 SDK
```csharp
class MyDataAccessLayer
{
    private CosmosClient writeClient;
    private CosmosClient readClient;

    public void InitializeAsync(string accountEndpoint, string key)
    {
        CosmosClientOptions writeConnectionOptions = new CosmosClientOptions();
        writeConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        writeConnectionOptions.ApplicationRegion = "West US";

        CosmosClientOptions readConnectionOptions = new CosmosClientOptions();
        readConnectionOptions.ConnectionMode = ConnectionMode.Direct;
        readConnectionOptions.ApplicationRegion = "East US";


        writeClient = new CosmosClient(accountEndpoint, key, writeConnectionOptions);
        readClient = new CosmosClient(accountEndpoint, key, readConnectionOptions);
    }
}
```

## <a name="implement-custom-synchronization"></a>Implementera anpassad synkronisering

När klienterna har initierats kan programmet utföra skrivningar till den lokala regionen (västra USA) och tvinga fram synkroniseringen till östra USA på följande sätt.

### <a name="net-v2-sdk"></a>.NET v2 SDK
```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

### <a name="net-v3-sdk"></a>.Net v3 SDK
```csharp
class MyDataAccessLayer
{
     public async Task CreateItem(string databaseId, string containerId, dynamic item)
     {
        ItemResponse<dynamic> response = await writeClient.GetContainer("containerId", "databaseId")
            .CreateItemAsync<dynamic>(
                item,
                new PartitionKey("test"));

        await readClient.GetContainer("containerId", "databaseId").ReadItemAsync<dynamic>(
            response.Resource.id,
            new PartitionKey("test"),
            new ItemRequestOptions { SessionToken = response.Headers.Session, ConsistencyLevel = ConsistencyLevel.Session });
    }
}
```

Du kan utöka modellen till att synkronisera till flera regioner parallellt.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om global distribution och konsekvens i Azure Cosmos DB kan du läsa följande artiklar:

* [Välj rätt konsekvens nivå i Azure Cosmos DB](consistency-levels-choosing.md)
* [Avvägningar gällande konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitionering och datadistribution i Azure Cosmos DB](partition-data.md)

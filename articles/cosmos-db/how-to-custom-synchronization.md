---
title: Så implementerar du anpassad synkronisering för att optimera för högre tillgänglighet och prestanda i Azure Cosmos DB
description: Lär dig mer om att implementera anpassade synkronisering för att optimera för högre tillgänglighet och prestanda i Azure Cosmos DB.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: f10e260432a93a0413d65d6f5814d00a50e9465a
ms.sourcegitcommit: 179918af242d52664d3274370c6fdaec6c783eb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/13/2019
ms.locfileid: "65560273"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Implementera anpassade synkronisering för att optimera för högre tillgänglighet och prestanda

Azure Cosmos DB erbjuder [fem väldefinierade konsekvensnivåer](consistency-levels.md) som du kan välja mellan att balansera förhållandet mellan konsekvens, prestanda och tillgänglighet. Stark konsekvens hjälper till att säkerställa att data replikeras synkront och varaktigt sparas i varje region där Azure Cosmos-konto är tillgänglig. Den här konfigurationen ger den högsta nivån av tillförlitlighet men man bekostnad av prestanda och tillgänglighet. Om du vill att programmet ska kunna övervaka eller lätta tålighet som passar programmet behöver utan att kompromissa med tillgänglighet kan du använda *anpassade synkronisering* på programnivå att uppnå nivån av tillförlitlighet du vill.

Följande bild visar visuellt synkronisering av anpassade modellen:

![Anpassade synkronisering](./media/how-to-custom-synchronization/custom-synchronization.png)

I det här scenariot är en behållare i Azure Cosmos replikeras globalt över flera regioner på flera kontinenter. Stark konsekvens påverkar för alla regioner i det här scenariot prestandan. För att säkerställa en högre nivå av tillförlitlighet utan att kompromissa med skrivfördröjning, programmet kan använda två klienter som delar samma [sessionstoken](how-to-manage-consistency.md#utilize-session-tokens).

Den första klienten kan skriva data till den lokala regionen (till exempel USA, västra). Andra klienten (till exempel i östra USA) är en skrivskyddad klient som används för att se till att synkronisering. Med flödar sessionstoken från write-svar på följande Läs säkerställer läsningen synkronisering av skrivningar till östra USA. Azure Cosmos DB garanterar att visas skrivningar för minst en region. De garanteras att överleva ett regionalt strömavbrott om den ursprungliga skrivregionen stängs av. I det här scenariot synkroniseras varje skrivning till östra USA, minskar fördröjningen för att använda stark konsekvens i alla regioner. Du kan utöka den här modellen för att synkronisera till flera regioner parallellt i ett scenario med flera huvudservrar, där skrivningar uppstår i varje region.

## <a name="configure-the-clients"></a>Konfigurera klienterna

I följande exempel visas en dataåtkomstlagret som instantierar två klienter för anpassade synkronisering:

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

## <a name="implement-custom-synchronization"></a>Implementera anpassad synkronisering

När klienterna initieras utföra programmet skrivningar till den lokala region (USA, västra) och Framtvinga synkronisering skrivningar till östra USA på följande sätt.

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

Du kan utöka modellen för att synkronisera till flera regioner parallellt.

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om global distribution och konsekvens i Azure Cosmos DB kan du läsa de här artiklarna:

* [Välj rätt konsekvensnivå i Azure Cosmos DB](consistency-levels-choosing.md)
* [Avvägningar gällande konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Hantera konsekvens i Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitionering och datadistribution i Azure Cosmos DB](partition-data.md)

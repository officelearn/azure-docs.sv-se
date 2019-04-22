---
title: Så implementerar du anpassad synkronisering för att optimera för högre tillgänglighet och prestanda i Azure Cosmos DB
description: Lär dig att implementera anpassad synkronisering för att optimera för högre tillgänglighet och prestanda i Azure Cosmos DB
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d948798f161eb36578cb679b6d96409917424fd4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678470"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Så implementerar du anpassad synkronisering för att optimera för högre tillgänglighet och prestanda

Azure Cosmos DB erbjuder [fem väldefinierade konsekvensnivåer](consistency-levels.md) som du kan välja mellan att balansera förhållandet mellan konsekvens, prestanda och tillgänglighet. Stark konsekvens säkerställer att data replikeras synkront och varaktigt sparas i varje region där Azure Cosmos-konto är tillgänglig. Den här konfigurationen ger den högsta nivån av tillförlitlighet men påverkar prestanda och tillgänglighet. Om ett program vill kontroll/lätta tillförlitlighet som passar programmet behov utan att kompromissa med tillgängligheten, den kan använda *anpassade synkronisering* på programnivå att uppnå det önskade tillförlitlighet.

Diagrammet nedan ger en visuell illustration av modellen för anpassad synkronisering.

![Anpassad synkronisering](./media/how-to-custom-synchronization/custom-synchronization.png)

I det här scenariot replikeras en Azure Cosmos-container globalt över flera regioner på flera kontinenter. Med stark konsekvens för alla regioner i det här scenariot skulle påverka prestandan. För att säkerställa en högre nivå av tillförlitlighet utan att kompromissa med skrivfördröjningen programmet kan använda två klienter som delar samma [sessionstoken](how-to-manage-consistency.md#utilize-session-tokens).

Den första klienten kan skriva data till den lokala regionen (till exempel USA, västra). Den andra klienten (till exempel USA, östra) är en läsningsklient som används för att säkerställa synkroniseringen. Genom att flöda sessionstoken från write-svar på följande Läs garanterar läsningen synkronisering av skrivningar till östra USA. Azure Cosmos DB säkerställer att skrivningar ses av minst en region och garanterat tål ett regionalt strömavbrott om den ursprungliga skrivregionen skulle sluta fungera. I det här scenariot synkroniseras varje skrivning till USA, östra, vilket minskar den svarstid som är förknippad med användning av stark latens mellan alla regioner. I ett scenario med flera original, där skrivningar sker i varje region, kan den här modellen utökas för synkronisering till flera regioner parallellt.

## <a name="configure-the-clients"></a>Konfigurera klienterna

Exemplet nedan visar hur dataåtkomstlagret instansierar två klienter för det här ändamålet.

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

När klienterna har initierats kan programmet utföra skrivningar till en lokal region (USA, västra) och kan framtvinga synkronisering av skrivningarna till USA, östra enligt nedan.

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

Den här modellen kan utökas för synkronisering till flera regioner parallellt.

## <a name="next-steps"></a>Nästa steg

Läs mer om flera global distribution och konsekvens i Azure Cosmos DB i följande artiklar:

* [Välja rätt konsekvensnivå i Azure Cosmos DB](consistency-levels-choosing.md)
* [Avvägningar gällande konsekvens, tillgänglighet och prestanda i Azure Cosmos DB](consistency-levels-tradeoffs.md)
* [Så hanterar du konsekvens i Azure Cosmos DB](how-to-manage-consistency.md)
* [Partitionering och datadistribution i Azure Cosmos DB](partition-data.md)

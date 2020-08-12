---
title: Felsök Azure Cosmos DB HTTP 408 eller begär timeout-problem med .NET SDK
description: Så här diagnostiserar och åtgärdar du undantags tids gränsen för .NET SDK-begäran
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 09442e01fa160d3851169a51230fa4cbef7e0980
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118577"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-net-sdk-request-timeout"></a>Diagnostisera och Felsök Azure Cosmos DB timeout för .NET SDK-begäran
HTTP 408-felet uppstår om SDK inte kunde slutföra begäran innan tids gränsen uppnåddes.

## <a name="customizing-the-timeout-on-the-azure-cosmos-net-sdk"></a>Anpassa tids gränsen för Azure Cosmos .NET SDK

SDK har två olika alternativ för att kontrol lera tids gränser, var och en med olika omfång.

### <a name="requesttimeout"></a>RequestTimeout

Med `CosmosClientOptions.RequestTimeout` konfigurationen (eller `ConnectionPolicy.RequestTimeout` för SDK v2) kan du ange en tids gräns som påverkar varje enskild nätverks förfrågan.  En åtgärd som startas av en användare kan omfatta flera nätverks begär Anden (till exempel kan begränsning) och den här konfigurationen gäller för varje nätverks förfrågan på nytt försök. Detta är inte en tids gräns för begäran från slut punkt till slut punkt.

### <a name="cancellationtoken"></a>CancellationToken

Alla asynkrona åtgärder i SDK har en valfri CancellationToken-parameter. Den här [CancellationToken](https://docs.microsoft.com/dotnet/standard/threading/how-to-listen-for-cancellation-requests-by-polling) används i hela åtgärden, över alla nätverks begär Anden. I-mellan-nätverks begär Anden kan CancellationToken kontrol leras och en åtgärd avbröts om den relaterade token har upphört att gälla. CancellationToken ska användas för att definiera en ungefärlig förväntad tids gräns i åtgärds omfånget.

> [!NOTE]
> CancellationToken är en mekanism där biblioteket kontrollerar annulleringen när det [inte orsakar ett ogiltigt tillstånd](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/). Åtgärden kan inte avbrytas exakt när tiden som definierats i annulleringen är upp, men när tiden är upp avbryts den när den är säker.

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar
Följande lista innehåller kända orsaker och lösningar för timeout-undantag för begäran.

### <a name="1-high-cpu-utilization-most-common-case"></a>1. hög processor användning (vanligt förekommande fall)
För optimal latens rekommenderar vi att CPU-användningen är ungefär 40%. Vi rekommenderar att du använder 10 sekunder som intervall för att övervaka Max (inte genomsnittlig) processor belastning. CPU-toppar är vanligare med frågor om flera partitioner där det kan göra flera anslutningar för en enda fråga.

#### <a name="solution"></a>Lösning:
Klient programmet som använder SDK bör skalas upp/ut.

### <a name="2-socket--port-availability-might-be-low"></a>2. tillgänglighet för socket/port kan vara låg
När du kör i Azure kan klienter som använder .NET SDK nå ut till PAT-port (Azure SNAT).

#### <a name="solution-1"></a>Lösning 1:
Om du kör på virtuella Azure-datorer följer du [guiden för SNAT-portens överbelastnings guide](troubleshoot-dot-net-sdk.md#snat).

#### <a name="solution-2"></a>Lösning 2:
Om du kör på Azure App Service följer du [fel söknings guiden för anslutnings fel](../app-service/troubleshoot-intermittent-outbound-connection-errors.md#cause) och [använder App Service diagnostik](https://azure.github.io/AppService/2018/03/01/Deep-Dive-into-TCP-Connections-in-App-Service-Diagnostics.html).

#### <a name="solution-3"></a>Lösning 3:
Om du kör på Azure Functions kontrollerar du att du följer [Azure Functions rekommendationer](../azure-functions/manage-connections.md#static-clients) för att underhålla singleton/static-klienter för alla berörda tjänster (inklusive Cosmos dB) och kontrollerar [tjänst gränserna](../azure-functions/functions-scale.md#service-limits) baserat på typen av och storleken på din Funktionsapp värd.

#### <a name="solution-4"></a>Lösning 4:
Om du använder en HTTP-proxy kontrollerar du att den har stöd för det antal anslutningar som kon figurer ATS i SDK `ConnectionPolicy` .
Annars är det problem med ansikts anslutning.

### <a name="3-creating-multiple-client-instances"></a>3. skapa flera klient instanser
Att skapa flera klient instanser kan leda till problem med anslutningens innehåll och timeout.

#### <a name="solution"></a>Lösning:
Följ [prestanda tipsen](performance-tips-dotnet-sdk-v3-sql.md#sdk-usage)och Använd en enda CosmosClient-instans i hela processen.

### <a name="4-hot-partition-key"></a>4. snabb partitionsnyckel
Azure Cosmos DB distribuerar det övergripande allokerade data flödet jämnt över fysiska partitioner. När det finns en aktiv partition tar en eller flera logiska partitionsnyckel på en fysisk partition över alla den fysiska partitionen RU/s, medan RU/s på andra fysiska partitioner går in. Det totala antalet RU/s som förbrukas är mindre än den totala etablerade RU/s i databasen eller behållaren, men du kommer fortfarande att se begränsning (429s) i begär Anden mot den heta logiska partitionsnyckel. Använd [måttet normaliserad ru-förbrukning](monitor-normalized-request-units.md) för att se om arbets belastningen påträffar en aktiv partition. 

#### <a name="solution"></a>Lösning:
Välj en lämplig partitionsnyckel som jämnt distribuerar begär ande volym och lagring. Lär dig hur du [ändrar partitionsnyckel](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

### <a name="5-high-degree-of-concurrency"></a>5. hög grad av samtidighet
Programmet gör en hög nivå av samtidighet, vilket kan leda till konkurrens på kanalen

#### <a name="solution"></a>Lösning:
Klient programmet som använder SDK bör skalas upp/ut.

### <a name="6-large-requests-andor-responses"></a>6. stora förfrågningar och/eller svar
Stora begär Anden eller svar kan leda till att det finns en rad olika block i kanal-och exacerbate-konkurrens, även med en relativt låg grad av samtidighet.

#### <a name="solution"></a>Lösning:
Klient programmet som använder SDK bör skalas upp/ut.

### <a name="7-failure-rate-is-within-cosmos-db-sla"></a>7. felfrekvensen är inom Cosmos DB SLA
Programmet ska kunna hantera tillfälliga fel och försöka igen när det behövs. 408-undantag upprepas inte på grund av att det inte går att ta reda på om tjänsten skapade objektet, eller om det inte gick att skapa sökvägar. Om du skickar samma objekt igen för att skapa uppstår ett konflikt undantag. Affärs logik för användar program kan ha anpassad logik för att hantera konflikter, vilket skulle leda till att ett befintligt objekt är tvetydigt jämfört med en konflikt med ett nytt försök.

### <a name="8-failure-rate-is-violating-the-cosmos-db-sla"></a>8. felfrekvensen bryter mot Cosmos DB SLA
Kontakta Azure-supporten.

## <a name="next-steps"></a>Nästa steg
* [Diagnostisera och Felsök](troubleshoot-dot-net-sdk.md) problem när du använder Azure Cosmos dB .NET SDK
* Lär dig mer om prestanda rikt linjer för [.net v3](performance-tips-dotnet-sdk-v3-sql.md) och [.NET v2](performance-tips.md)

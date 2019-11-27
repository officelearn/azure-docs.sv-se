---
title: 'Azure Cosmos DB: bulk utförar .NET API, SDK &-resurser'
description: Lär dig allt om bulk-utförar .NET API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB bulk utförar .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/24/2019
ms.author: ramkris
ms.openlocfilehash: b6685f8524dc1f3ac080abe81389d091c7cf6f1b
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74306752"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET bulk utförar-bibliotek: Hämta information 

> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-ändra feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursleverantör](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Mass utförar – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Beskrivning**| Med .net bulk utförar-biblioteket kan klient program utföra Mass åtgärder på Azure Cosmos DB-konton. Det här biblioteket innehåller BulkImport-, BulkUpdate-och BulkDelete-namnrymder. BulkImport-modulen kan samla in dokument på ett optimerat sätt, så att det data flöde som har allokerats för en samling förbrukas i maximal omfattning. BulkUpdate-modulen kan massredigera befintliga data i Azure Cosmos-behållare som korrigeringsfiler. BulkDelete-modulen kan Mass ta bort dokument på ett optimerat sätt, så att det data flöde som har allokerats för en samling förbrukas i maximal omfattning.|
|**SDK-hämtning**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Mass utförar-bibliotek i GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentation**|[Dokumentation om .NET API-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Kom igång**|[Kom igång med bulk utförar Library .NET SDK](bulk-executor-dot-net.md)|
| **Aktuellt ramverk som stöds**| Microsoft .NET Framework 4.5.2, 4.6.1 och .NET standard 2,0 |

> [!NOTE]
> Om du använder Mass utförar kan du se den senaste versionen 3. x av [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), som har en grupp utförar inbyggd i SDK: n. 

## <a name="release-notes"></a>Viktig information

### <a name="a-name241-preview241-preview"></a><a name="2.4.1-preview"/>2.4.1 – för hands version

* Fast TotalElapsedTime i svaret från BulkDelete för att mäta den totala tiden, inklusive eventuella återförsök.

### <a name="a-name240-preview240-preview"></a><a name="2.4.0-preview"/>2.4.0 – för hands version

* Förändrat SDK-beroende till > = 2.5.1

### <a name="a-name230-preview2230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Stöd har lagts till för utförar i Graph för att acceptera TTL på hörn och kanter

### <a name="a-name220-preview2220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-preview2

* Ett problem har åtgärd ATS, vilket orsakade undantag vid elastisk skalning av Azure Cosmos DB vid körning i Gateway-läge. Den här korrigeringen gör att den fungerar som likvärdig med 1.4.1-versionen.

### <a name="a-name210-preview2210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-preview2

* Stöd har lagts till för BulkDelete-stöd för SQL API-konton för att acceptera partitionsnyckel, dokument-ID-tupler att ta bort. Den här ändringen gör att den fungerar som likvärdig med 1.4.0-versionen.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Inklusive MongoBulkExecutor som stöder .NET standard 2,0. Den här funktionen gör att den fungerar som likvärdig med 1.3.0-versionen, med ytterligare stöd för .NET standard 2,0 som mål ramverk.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0 – för hands version

* .NET standard 2,0 har lagts till som en av de mål ramverk som stöds för att utförar-biblioteket ska fungera med .NET Core-program.

### <a name="a-name185185"></a><a name="1.8.5"/>1.8.5

* Lås på SDK-version har tagits bort. Paketet är nu beroende av SDK > = 2.5.1.

### <a name="a-name184184"></a><a name="1.8.4"/>1.8.4

* Fast hantering av identifierare vid anrop av BulkImport med en lista över POCO-objekt med numeriska värden.

### <a name="a-name183183"></a><a name="1.8.3"/>1.8.3

* Fast TotalElapsedTime i svaret från BulkDelete för att mäta den totala tiden, inklusive eventuella återförsök.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Fast hög CPU-förbrukning i vissa scenarier.
* Spårningen använder nu TraceSource. Användare kan definiera lyssnare för `BulkExecutorTrace` källan.
* Åtgärdat ett sällsynt scenario som kan orsaka ett lås när dokument skickas nästan MB i storlek.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Uppdaterade Mass utförar för att nu använda den senaste versionen av Azure Cosmos DB .NET SDK (2.4.0)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0

* Stöd har lagts till för utförar i Graph för att acceptera TTL på hörn och kanter

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

* Ett problem har åtgärd ATS, vilket orsakade undantag vid elastisk skalning av Azure Cosmos DB vid körning i Gateway-läge.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

* Stöd har lagts till för BulkDelete-stöd för SQL API-konton för att acceptera partitionsnyckel, dokument-ID-tupler att ta bort.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Ett problem har åtgärd ATS, vilket orsakade ett formateringsfel i användar agenten som används av bulk-utförar.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Förbättring av Mass utförar-import och uppdatering av API: er för transparent anpassning till elastisk skalning av Cosmos-behållare när lagringen överskrider den nuvarande kapaciteten utan att undantagen utlöses.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* DocumentDB .NET SDK-beroendet har överkulls till version 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Ett problem har åtgärd ATS, vilket ledde till att Mass utförar skulle utlösa JSRT-fel vid import till fasta samlingar.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Stöd har lagts till för BulkDelete-åtgärd för Azure Cosmos DB SQL API-konton.
* Stöd har lagts till för BulkImport-åtgärd för konton med Azure Cosmos DBs API för MongoDB.
* Har överkull DocumentDB .NET SDK-beroendet till version 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Stöd har lagts till för BulkImport-åtgärd för Azure Cosmos DB Gremlin API-konton.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Mindre fel korrigering i BulkImport-åtgärden för Azure Cosmos DB SQL API-konton.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Stöd har lagts till för BulkImport-och BulkUpdate-åtgärder för Azure Cosmos DB SQL API-konton.

## <a name="next-steps"></a>Nästa steg

Mer information om bulk utförar Java Library finns i följande artikel:

[Utförar Library SDK och versions information för Java bulk](sql-api-sdk-bulk-executor-java.md)

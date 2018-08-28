---
title: Använda BulkExecutor .NET-diagrambiblioteket för att utföra massåtgärder i Azure Cosmos DB Gremlin API
description: Lär dig hur du använder BulkExecutor-biblioteket för att importera mycket diagramdata till en Azure Cosmos DB Gremlin API-container.
services: cosmos-db
keywords: graph, gremlin, bulk, bulkexecutor, migration, data, cosmosdb, cosmos, database, import
author: luisbosquez
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-graph
ms.devlang: na
ms.topic: tutorial
ms.date: 08/14/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 39abf6d6da8a8035cf486ceb30b9c21186bbb925
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40234435"
---
# <a name="using-the-graph-bulkexecutor-net-library-to-perform-bulk-operations-in-azure-cosmos-db-gremlin-api"></a>Använda BulkExecutor .NET-diagrambiblioteket för att utföra massåtgärder i Azure Cosmos DB Gremlin API

Den här självstudiekursen innehåller instruktioner om hur du använder BulkExecutor .NET-biblioteket i Azure CosmosDB för att importera och uppdatera diagramobjekt till en Azure Cosmos DB Gremlin API-container. Den här processen använder diagramklassen i [BulkExecutor-biblioteket](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview) för att skapa hörn- och kant-objekt programmatiskt och sedan infoga flera av dem per nätverksbegäran. Det här beteendet kan konfigureras via BulkExecutor-biblioteket för optimal användning av resurser för både databasen och det lokala minnet.

Till skillnad från att skicka Gremlin-frågor till en databas, där kommandot utvärderas och sedan körs ett i taget, kräver användning av BulkExecutor-biblioteket i stället att objekt skapas och valideras lokalt. När objekten har skapats gör biblioteket att du kan skicka diagramobjekt till databasen sekventiellt. Med den här metoden kan hastigheten för datainmatning bli upp till 100 gånger snabbare, vilket gör metoden utmärkt för initiala datamigreringar eller periodiska dataflyttningsåtgärder. Lär dig mer genom att gå till GitHub-sidan för [Azure Cosmos DB Graph BulkExecutor-exempelprogrammet](https://aka.ms/graph-bulkexecutor-sample).

## <a name="bulk-operations-with-graph-data"></a>Massåtgärder med diagramdata

[BulkExecutor-biblioteket](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) innehåller en `Microsoft.Azure.CosmosDB.BulkExecutor.Graph`-namnrymd för att tillhandahålla funktioner för att skapa och importera diagramobjekt. 

Följande process beskriver hur datamigrering kan användas för en Gremlin API-container:
1. Hämta poster från datakällan.
2. Konstruera `GremlinVertex`- och `GremlinEdge`-objekt från de erhållna posterna och lägg till dem i en `IEnumerable`-datastruktur. I den här delen av programmet bör logik för att identifiera och lägga till relationer implementeras, om datakällan inte är en diagramdatabas.
3. Använd [Graph BulkImportAsync-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph.graphbulkexecutor.bulkimportasync?view=azure-dotnet) för att infoga diagramobjekt i samlingen.

Den här mekanismen förbättrar effektiviteten för datamigrering jämfört med att använda en Gremlin-klient. Den här förbättringen uppstår eftersom infogande av data med Gremlin kräver att programmet skickar en fråga åt gången som måste verifieras, utvärderas och sedan köras för att skapa data. BulkExecutor-biblioteket hanterar verifieringen i programmet och skickar flera diagramobjekt i taget för varje nätverksbegäran.

### <a name="creating-vertices-and-edges"></a>Skapa hörn och kanter

`GraphBulkExecutor` tillhandahåller den `BulkImportAsync`-metod som kräver en `IEnumerable`-lista över `GremlinVertex`- eller `GremlinEdge`-objekt, som både definieras i `Microsoft.Azure.CosmosDB.BulkExecutor.Graph.Element`-namnrymden. I det här exemplet delade vi in kanterna och hörnen i två BulkExecutor-importuppgifter. Se exemplet nedan:

```csharp

IBulkExecutor graphbulkExecutor = new GraphBulkExecutor(documentClient, targetCollection);

BulkImportResponse vResponse = null;
BulkImportResponse eResponse = null;

try
{
    // Import a list of GremlinVertex objects
    vResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateVertices(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);

    // Import a list of GremlinEdge objects
    eResponse = await graphbulkExecutor.BulkImportAsync(
            Utils.GenerateEdges(numberOfDocumentsToGenerate),
            enableUpsert: true,
            disableAutomaticIdGeneration: true,
            maxConcurrencyPerPartitionKeyRange: null,
            maxInMemorySortingBatchSize: null,
            cancellationToken: token);
}
catch (DocumentClientException de)
{
    Trace.TraceError("Document client exception: {0}", de);
}
catch (Exception e)
{
    Trace.TraceError("Exception: {0}", e);
}
```

Mer information om parametrarna i BulkExecutor-biblioteket finns i [ämnet BulkImportData till Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#bulk-import-data-to-azure-cosmos-db).

Nyttolasten behöver instansieras till `GremlinVertex`- och `GremlinEdge`-objekt. Så här kan dessa objekt skapas:

**Hörn**:
```csharp
// Creating a vertex
GremlinVertex v = new GremlinVertex(
    "vertexId",
    "vertexLabel");

// Adding custom properties to the vertex
v.AddProperty("customProperty", "value");

// Partitioning keys must be specified for all vertices
v.AddProperty("partitioningKey", "value");
```

**Kanter**:
```csharp
// Creating an edge
GremlinEdge e = new GremlinEdge(
    "edgeId",
    "edgeLabel",
    "targetVertexId",
    "sourceVertexId",
    "targetVertexLabel",
    "sourceVertexLabel",
    "targetVertexPartitioningKey",
    "sourceVertexPartitioningKey");

// Adding custom properties to the edge
e.AddProperty("customProperty", "value");
```

> [!NOTE]
> BulkExecutor-verktyget söker inte automatiskt efter befintliga hörn innan du lägger till kanter. Det här måste verifieras i programmet innan du kör BulkImport-uppgifter.

## <a name="sample-application"></a>Exempelprogram

### <a name="prerequisites"></a>Nödvändiga komponenter
* Visual Studio 2017 med arbetsbelastningen Azure Development. Du kan komma igång med [Visual Studio 2017 Community Edition](https://visualstudio.microsoft.com/downloads/) utan kostnad.
* En Azure-prenumeration. Du kan skapa [ett kostnadsfritt Azure-konto här](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cosmos-db). Du kan även skapa ett Cosmos DB-databaskonto med [Testa Azure Cosmos DB kostnadsfritt](https://azure.microsoft.com/try/cosmosdb/) utan en Azure-prenumeration.
* En Azure Cosmos DB Gremlin API-databas med en **obegränsad samling**. Den här guiden visar hur du kommer igång med [Azure Cosmos DB Gremlin API i .NET](https://docs.microsoft.com/azure/cosmos-db/create-graph-dotnet).
* Git. Mer information finns på [sidan för Git-nedladdningar](https://git-scm.com/downloads).

### <a name="clone-the-sample-application"></a>Klona exempelprogrammet
I den här självstudien går vi igenom stegen för att komma igång med hjälp av [Azure Cosmos DB Graph BulkExecutor-exemplet](https://aka.ms/graph-bulkexecutor-sample) på GitHub. Det här programmet består av en .NET-lösning som slumpmässigt genererar hörn- och kantobjekt och därefter kör massinfogningar till det angivna diagramdatabaskontot. För att hämta programmet kör du `git clone`-kommandot nedan:

```bash
git clone https://github.com/Azure-Samples/azure-cosmosdb-graph-bulkexecutor-dotnet-getting-started.git
```

Den här lagringsplatsen innehåller GraphBulkExecutor-exemplet med följande filer:

Fil|Beskrivning
---|---
`App.config`|Det här är där program- och databasspecifika parametrar anges. Den här filen ska först ändras för att ansluta till måldatabasen och -samlingarna.
`Program.cs`| Den här filen innehåller logiken bakom att skapa `DocumentClient`-samlingen, hantera rensningarna och skicka BulkExecutor-begärandena.
`Util.cs`| Den här filen innehåller en hjälpklass som innehåller logiken bakom att generera testdata och kontrollera om databasen och samlingarna finns.

I `App.config`-filen är följande konfigurationsvärden som kan tillhandahållas:

Inställning|Beskrivning
---|---
`EndPointUrl`|Det här är **din .NET SDK-slutpunkt** som finns i bladet Översikt för ditt Cosmos DB Gremlin API-databaskonto. Det har formatet för `https://your-graph-database-account.documents.azure.com:443/`
`AuthorizationKey`|Det här är den primära eller sekundära nyckel som visas under ditt Azure Cosmos DB-konto. Läs mer om att [skydda åtkomst till Azure Cosmos DB-data](https://docs.microsoft.com/azure/cosmos-db/secure-access-to-data#master-keys)
`DatabaseName`, `CollectionName`|Det här är **måldatabas- och samlingsnamnen**. När `ShouldCleanupOnStart` är inställt på `true` används dessa värden, tillsammans med `CollectionThroughput`, för att ta bort dem och skapa en ny databas och samling. På ett liknande sätt används de om `ShouldCleanupOnFinish` är inställt på `true` för att ta bort databasen när datainmatningen är klar. Observera att målsamlingen måste vara **en obegränsad samling**.
`CollectionThroughput`|Detta används för att skapa en ny samling om `ShouldCleanupOnStart`-alternativet är inställt på `true`.
`ShouldCleanupOnStart`|Detta kommer ta bort databaskontot och samlingarna innan programmet körs och sedan skapa nya med värdena `DatabaseName`, `CollectionName` och `CollectionThroughput`.
`ShouldCleanupOnFinish`|Detta kommer att ta bort databaskontot och samlingarna med angivna `DatabaseName` och `CollectionName` när programmet har körts.
`NumberOfDocumentsToImport`|Detta avgör hur många testhörn och testgränser som skapas i exemplet. Detta antal gäller för både hörn och kanter.
`NumberOfBatches`|Detta avgör hur många testhörn och testgränser som skapas i exemplet. Detta antal gäller för både hörn och kanter.
`CollectionPartitionKey`|Detta används för att skapa testhörn och testkanter, där den här egenskapen tilldelas automatiskt. Detta används även vid återskapande av databasen och samlingarna om `ShouldCleanupOnStart`-alternativet är inställt på `true`.

### <a name="run-the-sample-application"></a>Köra exempelprogrammet

1. Lägg till dina specifika databaskonfigurationsparametrar i `App.config`. Detta används för att skapa en DocumentClient-instans. Om databasen och containern inte har skapats ännu skapas de automatiskt.
2. Kör appen. Detta anropar `BulkImportAsync` två gånger, en gång för att importera hörn och en gång för att importera kanter. Om några av objekten genererar ett fel när de infogas läggs de till i antingen `.\BadVertices.txt` eller `.\BadEdges.txt`.
3. Utvärdera resultaten genom att köra frågor mot diagramdatabasen. Om `ShouldCleanupOnFinish`-alternativet är inställt på true (sant) tas databasen bort automatiskt.

## <a name="next-steps"></a>Nästa steg
* Mer information om Nuget-paketinformation och viktig information för massexecutor-.Net-biblioteket finns i [information om massexecutor-SDK](sql-api-sdk-bulk-executor-dot-net.md). 
* Gå igenom [prestandatipsen](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-dot-net#performance-tips) för att optimera användningen av BulkExecutor.
* Läs [referensartikeln om BulkExecutor.Graph](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.graph?view=azure-dotnet) för mer information om de klasser och metoder som definieras i den här namnrymden.

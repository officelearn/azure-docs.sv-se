---
title: 'Azure Cosmos DB: .NET (Microsoft.Azure.Cosmos) exempel för SQL API'
description: Hitta exemplen C# .NET V3 SDK på GitHub för vanliga uppgifter med Azure Cosmos DB SQL API.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 10/07/2019
ms.author: sngun
ms.openlocfilehash: 2a33a59ae0184e6c41fe7121560bc5df3a69cffd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "73888954"
---
# <a name="azure-cosmos-dbnet-v3-sdk-microsoftazurecosmos-examples-for-the-sql-api"></a>Azure Cosmos DB.NET V3 SDK (Microsoft.Azure.Cosmos) exempel för SQL API

> [!div class="op_single_selector"]
> * [.NET V2 SDK Exempel](sql-api-dotnet-samples.md)
> * [.NET V3 SDK Exempel](sql-api-dotnet-v3sdk-samples.md)
> * [Java-exempel](sql-api-java-samples.md)
> * [Async Java-exempel](sql-api-async-java-samples.md)
> * [Node.js-exempel](sql-api-nodejs-samples.md)
> * [Python-exempel](sql-api-python-samples.md)
> * [Azure-kodexempelgalleri](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
>
>

[Azure-cosmos-dotnet-v3 GitHub-databasen](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage) innehåller de senaste .NET-exempellösningarna för att utföra CRUD och andra vanliga åtgärder på Azure Cosmos DB-resurser. Om du är bekant med den tidigare versionen av .NET SDK kan du vara van vid termsamlingen och dokumentet. Eftersom Azure Cosmos DB stöder flera API-modeller använder version 3.0 av .NET SDK de allmänna termerna "container" och "item". En container kan vara en samling, ett diagram eller en tabell. Ett objekt kan vara ett dokument, en kant/ett hörn eller en rad, och är innehållet i en container. Den här artikeln innehåller:

* Länkar till uppgifterna i var och en av C#-projektfilerna.
* Länkar till det relaterade API-referensinnehållet.

## <a name="prerequisites"></a>Krav

Visual Studio 2019 med Azure-utvecklingsarbetsflödet installerat

- Du kan ladda ner och använda den **kostnadsfria** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/). Se till att du aktiverar **Azure-utveckling** under installationen av Visual Studio.

   [Package Microsoft.Azure.cosmos NuGet](https://www.nuget.org/packages/Microsoft.Azure.cosmos/)

En Azure-prenumeration eller ett kostnadsfritt Cosmos DB-utvärderingskonto

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- Du kan [aktivera Visual Studio-prenumerationsförmåner:](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)Din Visual Studio-prenumeration ger dig krediter varje månad, som du kan använda för betalda Azure-tjänster.
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  

> [!NOTE]
> Proverna är fristående och ställer in och städar upp efter sig själva. Varje förekomst fakturerar din prenumeration för en timmes användning på behållarens prestandanivå.
> 

## <a name="database-examples"></a>Databasexempel

[Metoden RunDatabaseDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L65-L91) i exempelprojektet *DatabaseManagement* visar hur du utför följande aktiviteter. Mer information om Azure Cosmos-databaser innan du kör följande exempel finns i [Arbeta med databaser, behållare och objekt](databases-containers-items.md).

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en databas](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L68) |[CosmosClient.CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.cosmosclient.createdatabaseifnotexistsasync?view=azure-dotnet) |
| [Läsa en databas via ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L80) |[Database.readAsync](/dotnet/api/microsoft.azure.cosmos.database.readasync?view=azure-dotnet) |
| [Läsa alla databaser för ett konto](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L96) |[CosmosClient.GetDatabaseQueryIterator](/dotnet/api/microsoft.azure.cosmos.cosmosclient.getdatabasequeryiterator?view=azure-dotnet) |
| [Ta bort en databas](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/DatabaseManagement/Program.cs#L106) |[Database.DeleteAsync](/dotnet/api/microsoft.azure.cosmos.database.deleteasync?view=azure-dotnet) |

## <a name="container-examples"></a>Containerexempel

Metoden [RunContainerDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L69-L89) i exempelprojektet *ContainerManagement* visar hur du utför följande aktiviteter. Mer information om Azure Cosmos-behållare innan du kör följande exempel finns i [Arbeta med databaser, behållare och objekt](databases-containers-items.md).

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en behållare](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L97-L107) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Skapa en behållare med anpassad indexprincip](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L111-L127) |[Database.CreateContainerIfNotExistsAsync](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet) |
| [Ändra konfigurerad prestanda för en behållare](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L149-L171) |[Behållare.ErsättGenomströmningAsync](/dotnet/api/microsoft.azure.cosmos.container.replacethroughputasync?view=azure-dotnet) |
| [Hämta en behållare efter ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L176-L185) |[Container.readContainerAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.container.readcontainerasync?view=azure-dotnet) |
| [Läsa alla behållare i en databas](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L193-L205) |[Databas.GetContainerQueryIterator](/dotnet/api/microsoft.azure.cosmos.database.getcontainerqueryiterator?view=azure-dotnet) |
| [Ta bort en container](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ContainerManagement/Program.cs#L213-L2018) |[Behållare.DeleteContainerAsync](/dotnet/api/microsoft.azure.cosmos.container.deletecontainerasync?view=azure-dotnet) |

## <a name="item-examples"></a>Objektexempel

[Metoden RunItemsDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L119-L130) i *exempelprojektet ItemManagement* visar hur du utför följande aktiviteter. Mer information om Azure Cosmos-objekt innan du kör följande exempel finns i [Arbeta med databaser, behållare och objekt](databases-containers-items.md).

| Aktivitet | API-referens |
| --- | --- |
| [Skapa ett objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L161-L200) |[Container.CreateItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Läsa ett objekt via ID](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L203-L241) |[Behållare. ReadItemAsync](/dotnet/api/microsoft.azure.cosmos.container.readitemasync?view=azure-dotnet) |
| [Fråga efter objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L244-L320) |[Behållare. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Ersätta en artikel](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L323-L363) |[Behållare. ErsättItemAsync](/dotnet/api/microsoft.azure.cosmos.container.replaceitemasync?view=azure-dotnet) |
| [Upsert ett objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L366-L411) |[Behållare. UpsertItemAsync](/dotnet/api/microsoft.azure.cosmos.container.upsertitemasync?view=azure-dotnet) |
| [Ta bort ett objekt](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L414-L424) |[Behållare. DeleteItemAsync](/dotnet/api/microsoft.azure.cosmos.container.deleteitemasync?view=azure-dotnet) |
| [Ersätta ett objekt med villkorsstyrd ETag-kontroll](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs#L585-L674) |[RequestOptions.IfMatchEtag](/dotnet/api/microsoft.azure.cosmos.requestoptions.ifmatchetag?view=azure-dotnet) |

## <a name="indexing-examples"></a>Indexeringsexempel

[Metoden RunIndexDemo](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L108-L122) i *exempelprojektet IndexManagement* visar hur du utför följande aktiviteter. Mer information om hur du indexerar i Azure Cosmos DB innan du kör följande exempel finns i [indexprinciper,](index-policy.md) [indextyper](index-types.md)och [indexsökvägar](index-paths.md). 

| Aktivitet | API-referens |
| --- | --- |
| [Utesluta ett objekt från indexet](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L130-L186) |[IndexingDirective.Exclude](/dotnet/api/microsoft.azure.cosmos.indexingdirective?view=azure-dotnet) |
| [Använd Lazy-indexering](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L198-L220) |[IndexingPolicy.IndexingMode](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.indexingmode?view=azure-dotnet) |
| [Exkludera angivna objektsökvägar från indexet](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/IndexManagement/Program.cs#L230-L297) |[IndexingPolicy.ExcludedPaths](/dotnet/api/microsoft.azure.cosmos.indexingpolicy.excludedpaths?view=azure-dotnet) |

## <a name="query-examples"></a>Exempelfrågor

[Metoden RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L76-L96) i exempelprojektet *Frågor* visar hur du utför följande uppgifter med hjälp av SQL-fråge grammatik, LINQ-providern med fråga och Lambda. Mer information om SQL-frågereferensen i Azure Cosmos DB innan du kör följande exempel finns i [SQL-frågeexempel för Azure Cosmos DB](how-to-sql-query.md).

| Aktivitet | API-referens |
| --- | --- |
| [Frågeobjekt från en enda partition](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L154-L186) |[Behållare. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Frågeobjekt från flera partitioner](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L215-L275) |[Behållare. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |
| [Fråga med hjälp av ett SQL-uttryck](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/Queries/Program.cs#L189-L212) |[Behållare. GetItemQueryIterator](/dotnet/api/microsoft.azure.cosmos.container.getitemqueryiterator?view=azure-dotnet) |

## <a name="change-feed-examples"></a>Exempel på ändringsflöde

[Metoden RunBasicChangeFeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) i exempel *changefeed-projektet* visar hur du utför följande aktiviteter. Mer information om ändringsflödet i Azure Cosmos DB innan du kör följande exempel finns i [Läs Azure Cosmos DB ändra feed](read-change-feed.md) och Ändra [feedprocessor](change-feed-processor.md).

| Aktivitet | API-referens |
| --- | --- |
| [Grundläggande funktioner för ändringsfeed](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L91-L119) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Läs ändringsflödet från en viss tid](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L127-L162) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |
| [Läs ändringsflödet från början](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L170-L198) |[ChangeFeedProcessorBuilder.WithStartTime(DateTime)](/dotnet/api/microsoft.azure.cosmos.changefeedprocessorbuilder.withstarttime?view=azure-dotnet) |
| [MIgrate från byte av matningsprocessor för att byta matning i V3 SDK](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs#L256-L333) |[Container.GetChangeFeedProcessorBuilder](/dotnet/api/microsoft.azure.cosmos.container.getchangefeedprocessorbuilder?view=azure-dotnet) |

## <a name="server-side-programming-examples"></a>Programmeringsexempel på serversidan

[Metoden RunDemoAsync](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L72-L102) i exempelprojektet *ServerSideScripts* visar hur du utför följande aktiviteter. Mer information om programmering på serversidan i Azure Cosmos DB innan du kör följande exempel finns i [Lagrade procedurer, utlösare och användardefinierade funktioner](stored-procedures-triggers-udfs.md).

| Aktivitet | API-referens |
| --- | --- |
| [Skapa en lagrad procedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L116) |[Scripts.CreateStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.createstoredprocedureasync?view=azure-dotnet) |
| [Köra en lagrad procedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L135) |[Scripts.ExecuteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.executestoredprocedureasync?view=azure-dotnet) |
| [Ta bort en lagrad procedur](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ServerSideScripts/Program.cs#L351) |[Scripts.DeleteStoredProcedureAsync](/dotnet/api/microsoft.azure.cosmos.scripts.scripts.deletestoredprocedureasync?view=azure-dotnet) |
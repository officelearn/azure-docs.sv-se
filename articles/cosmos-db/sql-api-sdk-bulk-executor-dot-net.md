---
title: 'Azure Cosmos DB: Massregistrera Executor .NET-API, SDK och resurser'
description: Lär dig allt om Bulk Executor .NET-API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB Bulk Executor .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 11/19/2018
ms.author: ramkris
ms.openlocfilehash: 8675c5ff90f7be2dc0b3ac80eb593adb613ba860
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039766"
---
# <a name="net-bulk-executor-library-download-information"></a>Bibliotek för .NET bulk Executor: Hämtningsinformation 

> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-Ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Massinläsning Executor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Massinläsning Executor – Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Beskrivning**</td><td>Massinläsning Executor biblioteket kan klientprogram för att utföra massåtgärder på Azure Cosmos DB-konton. Massinläsning Executor biblioteket innehåller BulkImport och BulkUpdate BulkDelete namnområden. BulkImport modulen kan masstilldela mata in dokument på ett optimerat sätt så att dataflödet som tillhandahållits för en samling används den största utsträckning. BulkUpdate modulen kan masstilldela uppdatera befintliga data i Azure Cosmos DB-behållare som korrigeringar. Modulen BulkDelete kan masstilldela ta bort dokument på ett optimerat sätt så att dataflödet som tillhandahållits för en samling används den största utsträckning.</td></tr>

<tr><td>**Hämta SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**BulkExecutor biblioteket i GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API-dokumentation**</td><td>[.NET API-referensdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Kom igång**</td><td>[Kom igång med Massregistrering Executor-biblioteket för .NET SDK](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Aktuella framework som stöds**</td><td>Microsoft .NET Framework 4.5.2, 4.6.1 och .NET Standard 2.0 </td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Inklusive MongoBulkExecutor till support .NET Standard 2.0. Den här funktionen gör det funktionellt motsvarar 1.3.0 släpptes med att lägga till stöd för .NET Standard 2.0 som målramverk.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* Har lagts till .NET Standard 2.0 som en av mål som stöds ramverken att göra BulkExecutor biblioteket fungerar med .NET Core-program.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

* Lägga till en överlagring för BulkDelete åtgärden för SQL-API-konton att acceptera partitionsnyckel, dokument-id tupplar att ta bort.
* Lägga till en överlagring för BulkDelete åtgärden för SQL-API-konton att acceptera RequestOptions som innehåller Partitionsnyckeln anger värdet för Partitionsnyckeln, förutom att använda det som ett filter i den inkommande fråga som anger dokument att ta bort.
* Ett problem som orsakade ett formatering problem i användaragenten som används av BulkExecutor har åtgärdats.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Gjort förbättringar i BulkExecutor import- och API: er för transparent anpassning till integrationsbehov elastisk skalning av Cosmos DB-behållare när lagring överskrider aktuell kapacitet utan undantag.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Ökar DocumentDB .NET SDK-beroendet till version 2.1.3.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Ett problem som gjorde att BulkExecutor utlöser JSRT fel medan du importerar till fasta samlingar har åtgärdats.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Stöd har lagts till för BulkDelete åtgärden för Azure Cosmos DB SQL API-konton.
* Stöd har lagts till för BulkImport åtgärden för konton med Azure Cosmos DB API för MongoDB.
* Ökar DocumentDB .NET SDK-beroendet till version 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Stöd har lagts till för BulkImport åtgärden för Gremlin-API för Azure Cosmos DB-konton.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Mindre felkorrigering BulkImport åtgärden för Azure Cosmos DB SQL API-konton.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Stöd har lagts till för BulkImport och BulkUpdate för Azure Cosmos DB SQL API-konton.

## <a name="next-steps"></a>Nästa steg

Mer information om grupp eller kör Java-bibliotek, finns i följande artikel:

[Bibliotek för Java Bulk Executor SDK och viktig information](sql-api-sdk-bulk-executor-java.md)

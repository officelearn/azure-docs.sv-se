---
title: 'Azure Cosmos DB: Bulk Executor .NET-API, SDK och resurser | Microsoft Docs'
description: Lär dig allt om Bulk Executor .NET-API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB Bulk Executor .NET SDK.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294466"
---
# <a name="net-bulk-executor-library-download-information"></a>Biblioteket för .NET bulk Executor: information om hämtning 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-Ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Massinläsning Executor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Massinläsning Executor – Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Beskrivning**</td><td>Massinläsning Executor biblioteket kan klientprogram att utföra massåtgärder i Azure Cosmos DB-konton. Massinläsning Executor biblioteket innehåller BulkImport, BulkUpdate och BulkDelete namnområden. BulkImport modulen kan masstilldela mata in dokument på ett optimerat sätt så att dataflödet som tillhandahållits för en samling används den största utsträckning. BulkUpdate modulen kan masstilldela uppdatera befintliga data i Azure Cosmos DB-behållare som korrigeringar. Modulen BulkDelete kan masstilldela ta bort dokument på ett optimerat sätt så att dataflödet som tillhandahållits för en samling används den största utsträckning.</td></tr>

<tr><td>**Hämta SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**BulkExecutor biblioteket i GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**API-dokumentation**</td><td>[.NET API-referensdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Kom igång**</td><td>[Kom igång med Massregistrering Executor-biblioteket för .NET SDK](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Aktuella framework som stöds**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(version > = 2.0.0)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(version > = 9.0.1)
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Stöd har lagts till för BulkDelete åtgärden för Azure Cosmos DB SQL API-konton.
* Stöd har lagts till för BulkImport åtgärden för Azure Cosmos DB MongoDB API-konton.
* Ökar DocumentDB .NET SDK-beroendet till version 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Stöd har lagts till för BulkImport åtgärden för Gremlin-API för Azure Cosmos DB-konton.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Mindre felkorrigering BulkImport åtgärden för Azure Cosmos DB SQL API-konton.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Stöd har lagts till för BulkImport och BulkUpdate för Azure Cosmos DB SQL API-konton.

---
title: 'Azure Cosmos DB: Massutnämningsman .NET API, SDK & resurser'
description: Lär dig allt om massutnämningsverkställaren .NET API och SDK, inklusive utgivningsdatum, återsändningsdatum och ändringar som gjorts mellan varje version av Azure Cosmos DB bulk executor .NET SDK.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 01/16/2020
ms.author: ramkris
ms.openlocfilehash: 1a8040fc397b526b540ce9343baa985cab49e2b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169404"
---
# <a name="net-bulk-executor-library-download-information"></a>.NET bulk executor library: Hämta information 

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [Resten](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkutnrutören - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
| **Beskrivning**| Med .Net bulk executor-biblioteket kan klientprogram utföra massåtgärder på Azure Cosmos DB-konton. Det här biblioteket innehåller namnområden BulkImport, BulkUpdate och BulkDelete. BulkImport-modulen kan massinta dokument på ett optimerat sätt så att dataflödet som etablerats för en samling förbrukas i dess maximala omfattning. BulkUpdate-modulen kan massuppdatering av befintliga data i Azure Cosmos-behållare som korrigeringsfiler. BulkDelete-modulen kan massborttagningsdokument på ett optimerat sätt så att dataflödet som etablerats för en samling förbrukas i dess maximala omfattning.|
|**SDK nedladdning**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/) |
| **Bibliotek för massutnrör i GitHub**| [GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)|
|**API-dokumentation**|[.NET API-referensdokumentation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)|
|**Kom igång**|[Komma igång med massutnämningsbiblioteket .NET SDK](bulk-executor-dot-net.md)|
| **Nuvarande ramverk som stöds**| Microsoft .NET Framework 4.5.2, 4.6.1 och .NET Standard 2.0 |

> [!NOTE]
> Om du använder massutnrönaren läser du den senaste versionen 3.x av [.NET SDK](tutorial-sql-api-dotnet-bulk-import.md), som har bulkutnrönaren inbyggd i SDK. 

## <a name="release-notes"></a>Viktig information

### <a name="241-preview"></a><a name="2.4.1-preview"/>2.4.1-förhandsvisning

* Fast TotalElapsedTime i svaret från BulkDelete att korrekt mäta den totala tiden inklusive eventuella försök.

### <a name="240-preview"></a><a name="2.4.0-preview"/>2.4.0-förhandsvisning

* Ändrat SDK-beroende till >= 2.5.1

### <a name="230-preview2"></a><a name="2.3.0-preview2"/>2.3.0-preview2

* Lagt till stöd för diagram bulk executor att acceptera ttl på hörn och kanter

### <a name="220-preview2"></a><a name="2.2.0-preview2"/>2.2.0-förhandsvisning2

* Åtgärdade ett problem som orsakade undantag under elastisk skalning av Azure Cosmos DB när du kör i gateway-läge. Den här korrigeringen gör den funktionellt likvärdig med 1.4.1 release.

### <a name="210-preview2"></a><a name="2.1.0-preview2"/>2.1.0-förhandsvisning2

* Lade till BulkDelete-stöd för SQL API-konton för att acceptera partitionsnyckel, dokument-ID-tupplar att ta bort. Denna förändring gör det funktionellt motsvarar 1.4.0 release.

### <a name="200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Inklusive MongoBulkExecutor för att stödja .NET Standard 2.0. Den här funktionen gör den funktionellt likvärdig med 1.3.0-versionen, med tillägget att stödja .NET Standard 2.0 som målramverk.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-förhandsvisning

* Lade till .NET Standard 2.0 som ett av de målramverk som stöds för att få massutrÃytssatorbiblioteket att fungera med .NET Core-program.

### <a name="188"></a><a name="1.8.8"/>1.8.8

* Fixade ett problem på MongoBulkExecutor som ökade dokumentstorleken oväntat genom att lägga till utfyllnad och i vissa fall gå över den maximala dokumentstorleksgränsen.

### <a name="187"></a><a name="1.8.7"/>1.8.7

* Åtgärdade ett problem med BulkDeleteAsync när samlingen har kapslade sökvägar för partitionsnyckeln.

### <a name="186"></a><a name="1.8.6"/>1.8.6

* MongoBulkExecutor implementerar nu IDisposable och det förväntas att avyttras efter användning.

### <a name="185"></a><a name="1.8.5"/>1.8.5

* Tog bort lås på SDK-versionen. Paketet är nu beroende av SDK >= 2.5.1.

### <a name="184"></a><a name="1.8.4"/>1.8.4

* Fast hantering av identifierare när bulkimport anropas med en lista över POCO-objekt med numeriska värden.

### <a name="183"></a><a name="1.8.3"/>1.8.3

* Fast TotalElapsedTime i svaret från BulkDelete att korrekt mäta den totala tiden inklusive eventuella försök.

### <a name="182"></a><a name="1.8.2"/>1.8.2

* Fast hög CPU-förbrukning på vissa scenarier.
* Spårning använder nu TraceSource. Användare kan definiera lyssnare `BulkExecutorTrace` för källan.
* Fixade ett sällsynt scenario som kunde orsaka ett lås när dokument skickades nära 2 Mb storlek.

### <a name="160"></a><a name="1.6.0"/>1.6.0

* Uppdaterade den massutnämningsman för att nu använda den senaste versionen av Azure Cosmos DB .NET SDK (2.4.0)

### <a name="150"></a><a name="1.5.0"/>1.5.0

* Lagt till stöd för diagram bulk executor att acceptera ttl på hörn och kanter

### <a name="141"></a><a name="1.4.1"/>1.4.1

* Åtgärdade ett problem som orsakade undantag under elastisk skalning av Azure Cosmos DB när du kör i gateway-läge.

### <a name="140"></a><a name="1.4.0"/>1.4.0

* Lade till BulkDelete-stöd för SQL API-konton för att acceptera partitionsnyckel, dokument-ID-tupplar att ta bort.

### <a name="130"></a><a name="1.3.0"/>1.3.0

* Åtgärdade ett problem som orsakade ett formateringsproblem i användaragenten som används av massutnredare.

### <a name="120"></a><a name="1.2.0"/>1.2.0

* Förbättrad massutnã¤nningsimport och uppdatering av API:er för att transparent anpassa sig till elastisk skalning av Cosmos-behållaren när lagringen överskrider den aktuella kapaciteten utan att utlösa undantag.

### <a name="112"></a><a name="1.1.2"/>1.1.2

* Stötte upp DocumentDB .NET SDK-beroendet till version 2.1.3.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* Åtgärdade ett problem som orsakade att bulkutnämningsfel kastade JSRT-fel när du importerade till fasta samlingar.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* Lade till stöd för MassDelete-åtgärden för Azure Cosmos DB SQL API-konton.
* Lade till stöd för MassImport-åtgärden för konton med Azure Cosmos DB:s API för MongoDB.
* Stötte upp DocumentDB .NET SDK-beroendet till version 2.0.0. 

### <a name="102"></a><a name="1.0.2"/>1.0.2

* Lade till stöd för MassImport-åtgärden för Azure Cosmos DB Gremlin API-konton.

### <a name="101"></a><a name="1.0.1"/>1.0.1

* Mindre felkorrigering till MassImport-åtgärden för Azure Cosmos DB SQL API-konton.

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Lade till stöd för MassImport- och BulkUpdate-åtgärder för Azure Cosmos DB SQL API-konton.

## <a name="next-steps"></a>Nästa steg

Mer information om Java-biblioteket för massut executor finns i följande artikel:

[Java bulk executor bibliotek SDK och släppa information](sql-api-sdk-bulk-executor-java.md)

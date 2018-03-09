---
title: 'Azure Cosmos DB: SQL .NET Core API, SDK & resurser | Microsoft Docs'
description: "Lär dig mer om SQL .NET Core API och SDK inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version av Azure Cosmos DB .NET Core SDK."
services: cosmos-db
documentationcenter: .net
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: f899b314-26ac-4ddb-86b2-bfdf05c2abf2
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/05/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 888740b434292c2ccc36fc90e0a9baaec96e2033
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK för SQL-API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET ändra Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Det här paketet har föråldrad och ersättas med Microsoft.Azure.DocumentDB https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/

<table>

<tr><td>**SDK-hämtningen**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)</td></tr>

<tr><td>**API-dokumentationen**</td><td>[.NET API-referensdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)</td></tr>

<tr><td>**Exempel**</td><td>[.NET-kodexempel](sql-api-dotnet-samples.md)</td></tr>

<tr><td>**Kom igång**</td><td>[Kom igång med Azure Cosmos DB .NET Core SDK](sql-api-dotnetcore-get-started.md)</td></tr>

<tr><td>**Självstudier för Web app**</td><td>[Utveckling av webbappar med Azure Cosmos DB](sql-api-dotnet-application.md)</td></tr>

<tr><td>**Aktuella framework som stöds**</td><td>[.NET standard 1.6 och .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)</td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information

Azure Cosmos DB .NET Core SDK har funktionsparitet med den senaste versionen av den [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md).

> [!NOTE] 
> Azure Cosmos DB .NET Core SDK är inte kompatibel med den universella Windowsplattformen (UWP) appar ännu. Om du är intresserad av att .NET Core SDK som stöder UWP-appar kan skicka e-post till [ askcosmosdb@microsoft.com ](mailto:askcosmosdb@microsoft.com).

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0

* Det här paketet har föråldrad och ersättas med Microsoft.Azure.DocumentDB https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/
* Fast KeyNotFoundException för mellan partition order by-frågor i specialfall.
* Fast bugg där JsonPropery attribut i select-satsen för LINQ-frågor inte som lösts in.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Fast programfel som träffar under vissa konkurrenstillstånd som resulterar i återkommande ”Microsoft.Azure.Documents.NotFoundException: Läs sessionen är inte tillgänglig för inkommande sessionstoken” fel när du använder konsekvensnivå för sessionen.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Fast regression där FeedOptions.MaxItemCount = -1 utlöste ett System.ArithmeticException: sidstorleken är negativt.
* Lägga till en ny funktion ToString() QueryMetrics.
* Exponeras partitionsstatistik på läsning av samlingar.
* Tillagda PartitionKey-egenskapen till ChangeFeedOptions.
* Mindre felkorrigeringar.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Lägger till möjligheten att ange unika index för dokument med UniqueKeyPolicy-egenskapen på dokumentsamling.
 * Fast ett programfel där de anpassade inställningarna JsonSerializer inte har som Inlöst för vissa frågor och lagrade proceduren körning.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Anpassning ändring från Azure DocumentDB Azure Cosmos-DB i API-referens dokumentation, metadatainformation i sammansättningar och NuGet-paketet. 
 * Visa diagnostikinformation och fördröjning från svar på förfrågningar som skickas med direkt anslutning läge. Egenskapsnamnen är RequestDiagnosticsString och RequestLatency på ResourceResponse klass.
 * Den här SDK-versionen kräver den senaste versionen av Azure Cosmos DB emulatorn måste vara tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Lägga till flera tillförlitlighet korrigeringar och förbättringar.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Internt ändringar med stöd för [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Stöd för PartitionKeyRangeId som en FeedOption scope frågeresultat till en viss nyckel partitionsintervallvärdet har lagts till. 
* Stöd har lagts till för StartTime som en ChangeFeedOption att starta söker efter ändringar efter den tidsperioden. 

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Ett problem har åtgärdats i klassen JsonSerializable som kan orsaka en dataspillsundantag.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Tillagt stöd för att ange anpassade JsonSerializerSettings när en [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) instans.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Stöder .NET Standard 1.5 som en mål-ramverk.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Ett problem som påverkade x64 har åtgärdats datorer som inte stöder SSE4 instruktion och utlösa SEHException när du kör Azure DB som Cosmos-frågor.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Tillagt stöd för en ny konsekvensnivå kallas ConsistentPrefix.
*   Stöd har lagts till för frågan måtten för enskilda partitioner.
*   Stöd har lagts till för att begränsa storleken på fortsättningstoken för frågor.
*   Tillagt stöd för mer detaljerad spårning för misslyckade begäranden.
*   Gjort vissa prestandaförbättringar i SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Ett problem som ignoreras PartitionKey värdet som angetts i FeedOptions för sammanställd frågor har åtgärdats.
* Ett problem har åtgärdats i transparent hantering av partition hantering under halva rör sig över flera partitioner Order By Frågekörningen.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Ett problem som orsakade deadlocks i några av asynkrona API: er när den används i ASP.NET-kontexten har åtgärdats.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Åtgärdar att göra SDK mer robust för automatisk redundans under vissa förhållanden.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Korrigering för ett problem som ibland medför en WebException: fjärrnamnet kunde inte matchas.
* Lägga till stöd för att direkt läsa ett skrivet dokument genom att lägga till nya överlagringar ReadDocumentAsync API.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* LINQ-stöd för aggregering frågor (COUNT, MIN, MAX, SUM och AVG) lagts till.
* Åtgärda för en minnesläcka för objektet ConnectionPolicy beror på användning av händelsehanterare.
* Korrigering för ett problem där UpsertAttachmentAsync inte fungerade när ETag har använts.
* Korrigering för ett problem där mellan partition ordning av frågan fortsättning inte arbetar vid sortering i string-fält.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Stöd för aggregering frågor (COUNT, MIN, MAX, SUM och AVG) har lagts till. Se [aggregering support](sql-api-sql-query.md#Aggregates).
* Sänks minsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

Azure Cosmos DB .NET Core SDK låter dig skapa snabb, plattformsoberoende [ASP.NET Core](https://www.asp.net/core) och [.NET Core](https://www.microsoft.com/net/core#windows) program ska köras på Windows-, Mac- och Linux. Den senaste versionen av Azure Cosmos DB .NET Core SDK är fullständigt [Xamarin](https://www.xamarin.com) kompatibel och används för att bygga program som är riktade till iOS, Android och Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

Azure Cosmos DB .NET Core Preview SDK låter dig skapa snabb, plattformsoberoende [ASP.NET Core](https://www.asp.net/core) och [.NET Core](https://www.microsoft.com/net/core#windows) program ska köras på Windows-, Mac- och Linux.

Azure Cosmos DB .NET Core Preview SDK har funktionsparitet med den senaste versionen av den [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) och har stöd för följande:
* Alla [anslutningsläge](performance-tips.md#networking): Gateway läge, direkt TCP och direkt HTTPs. 
* Alla [konsekvensnivåer](consistency-levels.md): stark, Session, begränsat föråldrad och Eventual.
* [Partitionerade samlingar](partition-data.md). 
* [Flera regioner databasen konton och geo-replikering](distribute-data-globally.md).

Om du har frågor som rör detta SDK, efter att [StackOverflow](http://stackoverflow.com/questions/tagged/azure-documentdb), eller ett problem i den [github-lagringsplatsen](https://github.com/Azure/azure-documentdb-dotnet/issues). 

## <a name="release--retirement-dates"></a>Versionen & pensionering datum

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [1.9.0](#1.9.0) |05 mars 2018 |--- |
| [1.8.2](#1.8.2) |21 februari 2018 |--- |
| [1.8.1](#1.8.1) |05 februari 2018 |--- |
| [1.7.1](#1.7.1) |16 november 2017 |--- |
| [1.7.0](#1.7.0) |10 november 2017 |--- |
| [1.6.0](#1.6.0) |17 oktober 2017 |--- |
| [1.5.1](#1.5.1) |02 oktober 2017 |--- |
| [1.5.0](#1.5.0) |10 augusti 2017 |--- | 
| [1.4.1](#1.4.1) |07 augusti 2017 |--- |
| [1.4.0](#1.4.0) |02 augusti 2017 |--- |
| [1.3.2](#1.3.2) |12 juni 2017 |--- |
| [1.3.1](#1.3.1) |23 maj 2017 |--- |
| [1.3.0](#1.3.0) |10 maj 2017 |--- |
| [1.2.2](#1.2.2) |19 april 2017 |--- |
| [1.2.1](#1.2.1) |Den 29 mars 2017 |--- |
| [1.2.0](#1.2.0) |25 mars 2017 |--- |
| [1.1.2](#1.1.2) |20 mars 2017 |--- |
| [1.1.1](#1.1.1) |14 mars 2017 |--- |
| [1.1.0](#1.1.0) |16 februari 2017 |--- |
| [1.0.0](#1.0.0) |21 december 2016 |--- |
| [0.1.0-Preview](#0.1.0-preview) |15 november 2016 |Den 31 december 2016 |

## <a name="see-also"></a>Se även
Läs mer om Cosmos-DB i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) sida. 


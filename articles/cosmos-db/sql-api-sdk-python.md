---
title: 'Azure Cosmos DB: SQL Python API, SDK & resurser'
description: Lär dig allt om SQL Python API och SDK, inklusive utgivningsdatum, pensioneringsdatum och ändringar som görs mellan varje version av Azure Cosmos DB Python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: b81a3921ec11d589dadbdebd698ab9ad67d7649c
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982913"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK för SQL API: Versionsanteckningar och resurser
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-ändringsfeed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulkutnrutören - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk executor - Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Ladda ner SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentation**|[Dokumentation för Python API-referens](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Installationsinstruktioner för SDK**|[Installationsinstruktioner för Python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Bidra till SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Kom igång**|[Komma igång med Python SDK](sql-api-python-application.md)|
|**Nuvarande plattform som stöds**|[Python 2.7](https://www.python.org/downloads/) och [Python 3.5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Viktig information

### <a name="302"></a><a name="3.0.2"/>3.0.2
* Lagt till stöd för MultiPolygon-datatyp
* Felkorrigering i principen för återförsök i session
* Buggfix för felaktiga utfyllnadsproblem vid avkodningsbas 64 strängar

### <a name="301"></a><a name="3.0.1"/>3.0.1
* Buggfix i LocationCache
* Felkorrigeringsslutpunkt återförsökslogik
* Fast dokumentation

### <a name="300"></a><a name="3.0.0"/>3.0.0
* Stöd för skrivningar med flera regioner.
* Namnområdet har ändrats till azure.cosmos.
* Samlings- och dokumentkoncept som bytt namn till behållare och objekt document_client bytt namn till cosmos_client. 

### <a name="233"></a><a name="2.3.3"/>2.3.3
* Lagt till stöd för proxy
* Lagt till stöd för läsändringsflöde
* Lagt till stöd för insamlingskvotrubriker
* Bugfix för problem med stora sessionstoken
* Bugfix för ReadMedia API
* Bugfix i partitionsnyckelintervallcachen

### <a name="232"></a><a name="2.3.2"/>2.3.2
* Lade till stöd för standardförsök i anslutningsproblem.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* Uppdaterad dokumentation för att referera till Azure Cosmos DB i stället för Azure DocumentDB.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Den här SDK-versionen kräver den senaste versionen av [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name="221"></a><a name="2.2.1"/>2.2.1
* Buggfix för aggregerad ordlista.
* Buggfix för trimning av snedstreck i resurslänken.
* Lade till tester för Unicode-kodning.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Lade till stöd för en ny konsekvensnivå som kallas ConsistentPrefix.


### <a name="210"></a><a name="2.1.0"/>2.1.0
* Lade till stöd för aggregeringsfrågor (ANTAL, MIN, MAX, SUMMA och AVG).
* Lade till ett alternativ för att inaktivera TLS-verifiering när du kör mot Cosmos DB Emulator.
* Tog bort begränsningen av beroende begäranden modulen vara exakt 2.10.0.
* Sänkt minsta genomströmning på partitionerade samlingar från 10 100 RU/s till 2500 RU/s.
* Lade till stöd för att aktivera skriptloggning under körning av lagrade procedurer.
* REST API-versionen stötte på '2017-01-19' med den här versionen.

### <a name="201"></a><a name="2.0.1"/>2.0.1
* Har redaktionellt gjort redaktionella ändringar i dokumentationskommentarer.

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Lagt till stöd för Python 3.5.
* Lade till stöd för anslutningspoolning med hjälp av en begäransmodul.
* Lagt till stöd för sessionskonsekvens.
* Lade till stöd för TOP/ORDERBY-frågor för partitionerade samlingar.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Lade till principstöd för återförsök för begränsade begäranden. (Begränsade begäranden får en för stor procentsats för begäran, felkod 429.) Som standard försöker Azure Cosmos DB nio gånger för varje begäran när felkod 429 påträffas, vilket bekräftar återförsökEfter tid i svarshuvudet. En fast återförsöksintervalltid kan nu ställas in som en del av egenskapen RetryOptions på ConnectionPolicy-objektet om du vill ignorera återförsökEfter tid som returneras av servern mellan återförsöken. Azure Cosmos DB väntar nu i högst 30 sekunder för varje begäran som begränsas (oavsett antal återförsök) och returnerar svaret med felkod 429. Den här gången kan också åsidosättas i egenskapen RetryOptions på ConnectionPolicy-objektet.
* Cosmos DB returnerar nu x-ms-throttle-retry-count och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran om att beteckna antalet återförsök och den sammanlagda tiden som begäran väntade mellan försöken.
* Tog bort klassen RetryPolicy och motsvarande egenskap (retry_policy) som visas i klassen document_client och introducerade i stället en klassen RetryOptions som exponerar egenskapen RetryOptions i klassen ConnectionPolicy som kan användas för att åsidosätta några av standardalternativen för återförsök.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Lade till stöd för databaskonton med flera regioner.

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Lade till stöd för funktionen Time To Live(TTL) för dokument.

### <a name="161"></a><a name="1.6.1"/>1.6.1
* Buggfixar relaterade till partitionering på serversidan för att tillåta specialtecken i sökvägen till partitionsnyckeln.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Implementerade [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md). 

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Lägg till Hash & Range partition resolvers för att hjälpa till med fragmentering program över flera partitioner.

### <a name="142"></a><a name="1.4.2"/>1.4.2
* Implementera Upsert. Nya UpsertXXX-metoder som lagts till för att stödja Upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar interna.

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Stöder GeoSpatial index.
* Validerar id-egenskap för alla resurser. Id:er för resurser får inte \, innehålla?, /, #, tecken eller med ett blanksteg.
* Lägger till nya rubriken "indexomvandlingsförlopp" i ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementerar V2-indexeringsprincip.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Stöder proxyanslutning.

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Släpp & pensionsdatum
Microsoft meddelar minst **12 månader** innan en SDK går i pension för att underlätta övergången till en nyare/stödd version.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK, som sådan är det rekommendera att du alltid uppgradera till den senaste SDK-versionen så tidigt som möjligt. 

Alla förfrågningar till Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner av Python SDK för SQL API före version **1.0.0** drogs tillbaka den **29 februari 2016**. 
> 
> 

> [!WARNING]
> Alla versioner 1.x och 2.x av Python SDK för SQL API kommer att dras tillbaka den **30 augusti 2020**. 
> 
> 

<br/>

| Version | Utgivningsdatum | Pensionering Datum |
| --- | --- | --- |
| [3.0.2](#3.0.2) |den 15 november 2018 |--- |
| [3.0.1](#3.0.1) |04 oktober 2018 |--- |
| [2.3.3](#2.3.3) |8 september 2018 |den 30 augusti 2020 |
| [2.3.2](#2.3.2) |den 8 maj 2018 |den 30 augusti 2020 |
| [2.3.1](#2.3.1) |den 21 december 2017 |den 30 augusti 2020 |
| [2.3.0](#2.3.0) |den 10 november 2017 |den 30 augusti 2020 |
| [2.2.1](#2.2.1) |29 september 2017 |den 30 augusti 2020 |
| [2.2.0](#2.2.0) |10 maj 2017 |den 30 augusti 2020 |
| [2.1.0](#2.1.0) |den 1 maj 2017 |den 30 augusti 2020 |
| [2.0.1](#2.0.1) |den 30 oktober 2016 |den 30 augusti 2020 |
| [2.0.0](#2.0.0) |den 29 september 2016 |den 30 augusti 2020 |
| [1.9.0](#1.9.0) |den 7 juli 2016 |den 30 augusti 2020 |
| [1.8.0](#1.8.0) |den 14 juni 2016 |den 30 augusti 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |den 30 augusti 2020 |
| [1.6.1](#1.6.1) |den 8 april 2016 |den 30 augusti 2020 |
| [1.6.0](#1.6.0) |den 29 mars 2016 |den 30 augusti 2020 |
| [1.5.0](#1.5.0) |den 3 januari 2016 |den 30 augusti 2020 |
| [1.4.2](#1.4.2) |den 6 oktober 2015 |den 30 augusti 2020 |
| 1.4.1 |den 6 oktober 2015 |den 30 augusti 2020 |
| [1.2.0](#1.2.0) |den 6 augusti 2015 |den 30 augusti 2020 |
| [1.1.0](#1.1.0) |den 9 juli 2015 |den 30 augusti 2020 |
| [1.0.1](#1.0.1) |den 25 maj 2015 |den 30 augusti 2020 |
| [1.0.0](#1.0.0) |den 7 april 2015 |den 30 augusti 2020 |
| 0.9.4-prelease |den 14 januari 2015 |den 29 februari 2016 |
| 0.9.3-prelease |den 9 december 2014 |den 29 februari 2016 |
| 0.9.2-prelease |den 25 november 2014 |den 29 februari 2016 |
| 0.9.1-prelease |den 23 september 2014 |den 29 februari 2016 |
| 0.9.0-prelease |den 21 augusti 2014 |den 29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns på tjänstsidan [för Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/) 


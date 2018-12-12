---
title: 'Azure Cosmos DB: SQL Python API, SDK och resurser'
description: Lär dig allt om SQL Python API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Python SDK för Azure Cosmos DB.
services: cosmos-db
author: rnagpal
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f67d8242eb1b2e29cf96a8065a664fe7f6dd6da5
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52875951"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK för SQL-API: viktig information och resurser
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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Hämta SDK**</td><td>[PyPI](https://pypi.org/project/azure-cosmos)</td></tr>

<tr><td>**API-dokumentation**</td><td>[Python API-referensdokumentation](https://docs.microsoft.com/python/api/overview/azure/cosmosdb?view=azure-python)</td></tr>

<tr><td>**Installationsinstruktioner för SDK**</td><td>[Instruktioner för installation av Python SDK](https://github.com/Azure/azure-cosmos-python)</td></tr>

<tr><td>**Bidra till SDK**</td><td>[GitHub](https://github.com/Azure/azure-cosmos-python)</td></tr>

<tr><td>**Kom igång**</td><td>[Kom igång med Python SDK](sql-api-python-application.md)</td></tr>

<tr><td>**Aktuella plattformar som stöds**</td><td>[Python 2.7](https://www.python.org/downloads/) och [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Stöd har lagts till för MultiPolygon datatyp
* Felkorrigering i sessionen läsa återförsöksprincip
* Felkorrigering för felaktig utfyllnad problem vid avkodning base 64 strängar

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Felkorrigering i LocationCache
* Felkorrigering logik för omprövning av slutpunkt
* Fast dokumentation

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Stöd för flera regioner skrivningar.
* Namespace ändras till azure.cosmos.
* Samling och dokument begrepp som bytt namn till behållare och objekt, document_client bytt namn till cosmos_client. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Stöd har lagts till för proxy
* Stöd har lagts till för att läsa ändringsfeed
* Stöd har lagts till för samlingen kvot rubriker
* Bugfix för stora sessionen tokens problemet
* Bugfix för ReadMedia API
* Bugfix i partitionen nyckelintervall cache

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Stöd har lagts till för standard återförsök på problem med anslutningen.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Uppdaterad dokumentation-referensen Azure Cosmos DB i stället för Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Den SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Buggfix för sammanställd ordlista.
* Buggfix för trimma snedstreck i resurslänken.
* Tester har lagts till för Unicode-kodning.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Stöd har lagts till för en ny konsekvensnivå kallas ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Stöd har lagts till för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig).
* Lägga till ett alternativ för att inaktivera verifiering av SSL när du kör mot Cosmos DB-emulatorn.
* Ta bort begränsningen av beroende begäranden modulen ska vara exakt 2.10.0.
* Sänkte lägsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.
* Stöd har lagts till för att aktivera loggning för skriptet vid körningen av lagrad procedur.
* REST API-version stötar till ' 2017-01-19' med den här versionen.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Ändrat språkliga dokumentation kommentarer.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Tillagt stöd för Python 3.5.
* Stöd har lagts till för anslutningspoolning med hjälp av modulen för begäranden.
* Stöd har lagts till för sessionskonsekvens.
* Stöd har lagts till för TOP/ORDERBY frågor partitionerade samlingar.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Har lagts till återförsök princip stöd för begränsade begäranden. (Begränsade begäranden ta emot en begäran om frekvensen för stor undantag, felkod 429.) Som standard Azure Cosmos DB återförsök nio gånger för varje begäran när felkod 429 påträffas, respekterar retryAfter tiden i svarshuvudet. En fast återförsöksintervallet kan nu ställas in som en del av egenskapen RetryOptions på ConnectionPolicy objektet om du vill ignorera retryAfter tiden mellan återförsöken som returneras av servern. Azure Cosmos DB väntar nu upp till 30 sekunder för varje begäran som har begränsats (oavsett antal nya försök) och returnerar svaret med felkod 429. Nu kan åsidosättas i egenskapen RetryOptions ConnectionPolicy-objektet.
* Cosmos DB Returnerar nu x-ms-begränsning--antalet återförsök och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran att ange begränsningen försök antal och den kumulativ väntetid begäran mellan återförsöken.
* Bort klassen RetryPolicy och motsvarande egenskap (retry_policy) visas på klassen document_client och i stället introducerades en RetryOptions klass exponera egenskapen RetryOptions ConnectionPolicy klass som kan användas för att åsidosätta en del av den standardalternativen för återförsök.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Lagt till stöd för flera regioner.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Lagt till stöd för tid Live(TTL) funktion för dokument.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Felkorrigeringar rör serversidan partitionering för att tillåta specialtecken i partitionen Nyckelsökväg.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementerat [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Lägg till Hash & intervall partitions-matchare som hjälper till med horisontell partitionering program över flera partitioner.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementera Upsert. Nya UpsertXXX-metoder som lagts till stöd för Upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar som är interna.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Har stöd för geospatiala index.
* Verifierar id-egenskapen för alla resurser. ID: n för resurser får inte innehålla?, /, #, \, tecken eller sluta med ett blanksteg.
* Lägger till ny rubrik ”index omvandling status” ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementerar V2 indexeringsprincip.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Stöd för proxyanslutning.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK.

## <a name="release--retirement-dates"></a>Fri & Släpp dras tillbaka datum
Microsoft meddelar minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Varje begäran till Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner av Azure SQL-SDK för Python före version **1.0.0** drogs tillbaka **29 februari 2016**. 
> 
> 

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [3.0.2](#3.0.2) |Den 15 november 2018 |--- |
| [3.0.1](#3.0.1) |04 oktober 2018 |--- |
| [2.3.3](#2.3.3) |08 september 2018 |--- |
| [2.3.2](#2.3.2) |08 maj 2018 |--- |
| [2.3.1](#2.3.1) |Den 21 december 2017 |--- |
| [2.3.0](#2.3.0) |Den 10 november 2017 |--- |
| [2.2.1](#2.2.1) |Sep 29, 2017 |--- |
| [2.2.0](#2.2.0) |10 maj 2017 |--- |
| [2.1.0](#2.1.0) |Maj 01, 2017 |--- |
| [2.0.1](#2.0.1) |Den 30 oktober 2016 |--- |
| [2.0.0](#2.0.0) |Den 29 september 2016 |--- |
| [1.9.0](#1.9.0) |07 juli 2016 |--- |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.1](#1.6.1) |08 april 2016 |--- |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |--- |
| [1.5.0](#1.5.0) |03 januari 2016 |--- |
| [1.4.2](#1.4.2) |06 oktober 2015 |--- |
| [1.4.1](#1.4.1) |06 oktober 2015 |--- |
| [1.2.0](#1.2.0) |06 augusti 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| [1.0.1](#1.0.1) |Den 25 maj 2015 |--- |
| [1.0.0](#1.0.0) |07 april 2015 |--- |
| 0.9.4-prelease |14 januari 2015 |Den 29 februari 2016 |
| 0.9.3-prelease |09 december 2014 |Den 29 februari 2016 |
| 0.9.2-prelease |Den 25 november 2014 |Den 29 februari 2016 |
| 0.9.1-prelease |Den 23 september 2014 |Den 29 februari 2016 |
| 0.9.0-prelease |Den 21 augusti 2014 |Den 29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan. 


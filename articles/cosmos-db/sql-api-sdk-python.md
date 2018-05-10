---
title: 'Azure Cosmos DB: SQL Python API, SDK & resurser | Microsoft Docs'
description: Lär dig mer om SQL Python API och SDK inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version av Azure Cosmos DB Python SDK.
services: cosmos-db
documentationcenter: python
author: rnagpal
manager: kfile
editor: cgronlun
ms.assetid: 3ac344a9-b2fa-4a3f-a4cc-02d287e05469
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 1/4/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 84e9e3b486e3c825e98530a30850e9f21ef41b91
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Python SDK för SQL-API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET ändra Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Ladda ned SDK**</td><td>[PyPI](https://pypi.python.org/pypi/pydocumentdb)</td></tr>

<tr><td>**API-dokumentationen**</td><td>[Python-API-referensdokumentation](http://azure.github.io/azure-documentdb-python/api/pydocumentdb.html)</td></tr>

<tr><td>**Installationsinstruktioner för SDK**</td><td>[Installationsinstruktioner för Python SDK](http://azure.github.io/azure-documentdb-python/)</td></tr>

<tr><td>**Bidra till SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-python)</td></tr>

<tr><td>**Kom igång**</td><td>[Kom igång med Python SDK](sql-api-python-application.md)</td></tr>

<tr><td>**Aktuella plattform som stöds**</td><td>[Python 2.7](https://www.python.org/downloads/) och [Python 3.5](https://www.python.org/downloads/)</td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information
### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Uppdaterad dokumentation till referens Azure Cosmos-DB i stället för Azure DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Den här SDK-versionen kräver den senaste versionen av Azure Cosmos DB emulatorn måste vara tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Buggfix för sammanställd ordlistan.
* Buggfix för trimning snedstreck i resurslänken.
* Tillagda tester för Unicode-kodning.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Tillagt stöd för en ny konsekvensnivå kallas ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Stöd för aggregering frågor (COUNT, MIN, MAX, SUM och AVG) har lagts till.
* Lägga till ett alternativ för att inaktivera SSL-kontroll när du kör mot Cosmos DB-emulatorn.
* Ta bort begränsningar av beroende begäranden modul ska vara exakt 2.10.0.
* Sänks minsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.
* Stöd har lagts till för att aktivera loggning skript under körning av lagrad procedur.
* REST API-version stötar till ' 2017-01-19' med den här versionen.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Ändrat redaktionell dokumentationskommentarer.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Stöd för Python 3.5 har lagts till.
* Stöd för anslutningspooler modulen begäranden har lagts till.
* Stöd för sessionskonsekvens har lagts till.
* Stöd för upp/ORDERBY för partitionerade samlingar har lagts till.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Tillagda försök princip stöd för begränsad begäranden. (Begränsad begäranden får en förfrågan hastighet för stor undantag, felkod 429.) Standard Azure Cosmos DB återförsök nio gånger för varje begäran när felkoden 429 påträffas respektera retryAfter tid i rubriken. En fast försök tidsintervall kan nu anges som en del av egenskapen RetryOptions på ConnectionPolicy-objekt om du vill ignorera retryAfter tiden som returnerades av servern mellan försöken. Azure Cosmos-DB väntar nu högst 30 sekunder för varje begäran som har begränsats (oavsett antal försök) och returnerar svaret med felkoden 429. Nu kan åsidosättas i egenskapen RetryOptions på ConnectionPolicy objekt.
* Cosmos DB Returnerar nu x-ms-begränsning--antal försök och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran att ange begränsningen försök antal och kumulativa tid begäran väntade mellan försöken.
* Bort klassen RetryPolicy och motsvarande egenskap (retry_policy) visas på klassen document_client och introducerades i stället en RetryOptions klass exponera egenskapen RetryOptions ConnectionPolicy klass som kan användas för att åsidosätta en del av du standardalternativen vara försök igen.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Lägga till stöd för flera regioner databasen konton.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Lägga till stöd för tid Live(TTL) funktion för dokument.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Felkorrigeringar rör serversidan partitionering för att tillåta specialtecken i partitionen nyckelsökvägen.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementerad [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Lägg till hash- & intervall partitions-matchare att hjälpa till med horisontell partitionering program över flera partitioner.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementera Upsert. Nya UpsertXXX metoder lägga till stöd för Upsert-funktionen.
* Implementera ID-baserat routning. Inga offentliga API-ändringar, alla ändringar som är interna.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Stöder geospatiala index.
* Verifierar id-egenskapen för alla resurser. ID för resurser kan inte innehålla?, /, #, \, tecken eller sluta med ett blanksteg.
* Lägger till nya rubriken ”index omvandling pågår” ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementerar V2 indexprincip.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Stöd för proxyanslutning.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Versionen & pensionering datum
Microsoft meddelar minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK, som vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt. 

Alla förfrågningar till Cosmos-databasen med en pensionerad SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner av SQL Azure SDK för Python före version **1.0.0** har dragits tillbaka på **29 februari 2016**. 
> 
> 

<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [2.3.1](#2.3.1) |21 december 2017 |--- |
| [2.3.0](#2.3.0) |10 november 2017 |--- |
| [2.2.1](#2.2.1) |Sep 29, 2017 |--- |
| [2.2.0](#2.2.0) |10 maj 2017 |--- |
| [2.1.0](#2.1.0) |01 kan 2017 |--- |
| [2.0.1](#2.0.1) |30 oktober 2016 |--- |
| [2.0.0](#2.0.0) |Den 29 september 2016 |--- |
| [1.9.0](#1.9.0) |07 juli 2016 |--- |
| [1.8.0](#1.8.0) |14 juni 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.1](#1.6.1) |08 april 2016 |--- |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |--- |
| [1.5.0](#1.5.0) |03 januari 2016 |--- |
| [1.4.2](#1.4.2) |06 oktober 2015 |--- |
| [1.4.1](#1.4.1) |06 oktober 2015 |--- |
| [1.2.0](#1.2.0) |06 augusti 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| [1.0.1](#1.0.1) |25 maj 2015 |--- |
| [1.0.0](#1.0.0) |07 april 2015 |--- |
| 0.9.4-prelease |14 januari 2015 |Den 29 februari 2016 |
| 0.9.3-prelease |09 december 2014 |Den 29 februari 2016 |
| 0.9.2-prelease |25 november 2014 |Den 29 februari 2016 |
| 0.9.1-prelease |23 september 2014 |Den 29 februari 2016 |
| 0.9.0-prelease |21 augusti 2014 |Den 29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Läs mer om Cosmos-DB i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) sida. 


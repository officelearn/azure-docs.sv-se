---
title: 'Azure Cosmos DB: SQL python API, SDK &-resurser'
description: Lär dig allt om SQL python API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB python SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: 6bc636b751d12bdb576e54f26536ac0045839229
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70137347"
---
# <a name="azure-cosmos-db-python-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB python SDK för SQL API: Viktig information och resurser
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
> * [Mass utförar – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Mass utförar – Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Hämta SDK**|[PyPI](https://pypi.org/project/azure-cosmos)|
|**API-dokumentation**|[Dokumentation om python API-referens](https://docs.microsoft.com/python/api/azure-cosmos/?view=azure-python)|
|**Instruktioner för SDK-installation**|[Installations anvisningar för python SDK](https://github.com/Azure/azure-cosmos-python)|
|**Bidra till SDK**|[GitHub](https://github.com/Azure/azure-cosmos-python)|
|**Kom igång**|[Kom igång med python SDK](sql-api-python-application.md)|
|**Aktuell plattform som stöds**|[Python 2,7](https://www.python.org/downloads/) och [python 3,5](https://www.python.org/downloads/)|

## <a name="release-notes"></a>Viktig information

### <a name="a-name302302"></a><a name="3.0.2"/>3.0.2
* Stöd har lagts till för data typen multipolygon
* Fel korrigering i arbets princip för att försöka läsa
* Fel korrigering för felaktiga utfyllnads problem vid avkodning av bas 64-strängar

### <a name="a-name301301"></a><a name="3.0.1"/>3.0.1
* Fel korrigering i LocationCache
* Bugg åtgärds fel vid slut punkts omprövning
* Fast dokumentation

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0
* Stöd för skrivningar i flera regioner.
* Namn området ändrades till Azure. Cosmos.
* Samlings-och dokument koncepten har bytt namn till behållare och objekt, document_client har bytt namn till cosmos_client. 

### <a name="a-name233233"></a><a name="2.3.3"/>2.3.3
* Stöd har lagts till för proxy
* Stöd för läsning av ändrings flöde har lagts till
* Stöd har lagts till för samlings kvot rubriker
* Problem med Bugfix för stora sessioner
* Bugfix för ReadMedia-API
* Cache för Bugfix i partitionsnyckel

### <a name="a-name232232"></a><a name="2.3.2"/>2.3.2
* Stöd har lagts till för standard återförsök vid anslutnings problem.

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Uppdaterad dokumentation till referens Azure Cosmos DB i stället för Azure-DocumentDB.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Den SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Fel korrigering för samlings ord lista.
* Fel korrigering för trimning av snedstreck i resurs länken.
* Tester har lagts till för Unicode-kodning.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Stöd har lagts till för en ny konsekvensnivå kallas ConsistentPrefix.


### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Stöd har lagts till för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig).
* Ett alternativ har lagts till för att inaktivera SSL-verifiering när den körs mot Cosmos DB emulator.
* Tog bort begränsningen för modulen för beroende begär Anden så att den är exakt 2.10.0.
* Sänkte lägsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.
* Stöd har lagts till för att aktivera skript loggning under körning av lagrad procedur.
* REST API-versionen har blivit 2017-01-19 med den här versionen.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Gjort redaktionella ändringar i dokumentations kommentarer.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Stöd har lagts till för python 3,5.
* Stöd har lagts till för anslutningspoolen med hjälp av en begär ande modul.
* Stöd har lagts till för konsekvens av sessioner.
* Stöd har lagts till för TOP-/ORDERBY-frågor för partitionerade samlingar.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Princip stöd för återförsök har lagts till för begränsade begär Anden. (Begränsade begär Anden tar emot en begäran om för stort undantag, felkod 429.) Som standard försöker Azure Cosmos DB nio gånger för varje begäran när felkod 429 påträffas, vilket följer retryAfter-tiden i svars huvudet. Du kan nu ange ett intervall med fasta återförsöksintervall som en del av egenskapen RetryOptions i ConnectionPolicy-objektet om du vill ignorera retryAfter-tiden som returnerades av servern mellan Återförsöken. Azure Cosmos DB väntar nu i högst 30 sekunder för varje begäran som begränsas (oavsett antal försök) och returnerar svaret med felkoden 429. Den här tiden kan också åsidosättas i egenskapen RetryOptions på ConnectionPolicy-objektet.
* Cosmos DB Returnerar nu x-MS-begränsning-försök-antal och x-MS-begränsning-försök-wait-Time-MS som svars rubriker i varje begäran om att ange begränsningen för antal försök och den ackumulerade tiden som begäran väntar mellan Återförsöken.
* Borttagning av klassen RetryPolicy och motsvarande egenskap (retry_policy) som exponerats i klassen document_client och i stället introducerade en RetryOptions-klass som visar RetryOptions-egenskapen i ConnectionPolicy-klassen som kan användas för att åsidosätta vissa av standard alternativ för återförsök.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Lagt till stöd för flera regioner.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Har lagt till stöd för TTL-funktionen (Time to Live) för dokument.

### <a name="a-name161161"></a><a name="1.6.1"/>1.6.1
* Fel korrigeringar relaterade till partitionering på Server sidan för att tillåta specialtecken i partitionsnyckel Sök vägs nyckel.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementerat [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md). 

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Lägg till Hash & intervall partitions-matchare som hjälper till med horisontell partitionering program över flera partitioner.

### <a name="a-name142142"></a><a name="1.4.2"/>1.4.2
* Implementera Upsert. Nya UpsertXXX-metoder har lagts till som stöd för upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar som är interna.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Har stöd för GeoSpatialt index.
* Verifierar id-egenskapen för alla resurser. ID: n för resurser får inte innehålla?, /, #, \, tecken eller sluta med ett blanksteg.
* Lägger till ny rubrik ”index omvandling status” ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementerar v2-indexerings princip.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Stöder proxy-anslutning.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK.

## <a name="release--retirement-dates"></a>Frisläpp & indragnings datum
Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK: n, eftersom det rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Alla förfrågningar till Cosmos DB med hjälp av en tillbakadragen SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner av python SDK för SQL API före version **1.0.0** drogs tillbaka den **29 februari 2016**. 
> 
> 

> [!WARNING]
> Alla versioner 1. x och 2. x av python SDK för SQL API kommer att dras tillbaka den **30 augusti 2020**. 
> 
> 

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [3.0.2](#3.0.2) |Den 15 november 2018 |--- |
| [3.0.1](#3.0.1) |Okt 04, 2018 |--- |
| [2.3.3](#2.3.3) |Sept 08, 2018 |30 augusti 2020 |
| [2.3.2](#2.3.2) |08 maj, 2018 |30 augusti 2020 |
| [2.3.1](#2.3.1) |21 december 2017 |30 augusti 2020 |
| [2.3.0](#2.3.0) |Den 10 november 2017 |30 augusti 2020 |
| [2.2.1](#2.2.1) |Sep 29, 2017 |30 augusti 2020 |
| [2.2.0](#2.2.0) |10 maj 2017 |30 augusti 2020 |
| [2.1.0](#2.1.0) |01 maj, 2017 |30 augusti 2020 |
| [2.0.1](#2.0.1) |Den 30 oktober 2016 |30 augusti 2020 |
| [2.0.0](#2.0.0) |29 september 2016 |30 augusti 2020 |
| [1.9.0](#1.9.0) |Den 07 juli 2016 |30 augusti 2020 |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |30 augusti 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |30 augusti 2020 |
| [1.6.1](#1.6.1) |08, 2016 |30 augusti 2020 |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |30 augusti 2020 |
| [1.5.0](#1.5.0) |03 januari 2016 |30 augusti 2020 |
| [1.4.2](#1.4.2) |06 oktober 2015 |30 augusti 2020 |
| 1.4.1 |06 oktober 2015 |30 augusti 2020 |
| [1.2.0](#1.2.0) |06 augusti 2015 |30 augusti 2020 |
| [1.1.0](#1.1.0) |09 juli 2015 |30 augusti 2020 |
| [1.0.1](#1.0.1) |25 maj 2015 |30 augusti 2020 |
| [1.0.0](#1.0.0) |07 april 2015 |30 augusti 2020 |
| 0.9.4-prelease |14 januari 2015 |Den 29 februari 2016 |
| 0.9.3-prelease |Den 9 december 2014 |Den 29 februari 2016 |
| 0.9.2-prelease |25 november 2014 |Den 29 februari 2016 |
| 0.9.1-prelease |23 september 2014 |Den 29 februari 2016 |
| 0.9.0-prelease |21 augusti 2014 |Den 29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan. 


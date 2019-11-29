---
title: 'Azure Cosmos DB: SQL Java API, SDK & resurser'
description: Lär dig allt om SQL Java API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 42dcc0e9bd07f357c17f28dd754e4ae3404e01ff
ms.sourcegitcommit: c31dbf646682c0f9d731f8df8cfd43d36a041f85
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2019
ms.locfileid: "74561878"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK för SQL API: viktig information och resurser
> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [.NET-ändra feed](sql-api-sdk-dotnet-changefeed.md)
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

SQL API Java SDK stöder synkrona åtgärder. För asynkron support använder du [asynkron Java SDK för SQL API](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**SDK-hämtning**|[Maven 3.](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentation**|[Referens dokumentation för Java API](/java/api/com.microsoft.azure.documentdb)|
|**Bidra till SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Komma igång**|[Kom igång med Java SDK](sql-api-java-get-started.md)|
|**Själv studie kurs om webbapp**|[Utveckling av webb program med Azure Cosmos DB](sql-api-java-application.md)|
|**Lägsta körnings tid som stöds**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Viktig information

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Undvik ett nytt försök med ett ogiltigt intervall för partitionsnyckel, om användaren tillhandahåller pkRangeId.

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Optimerade partitionerings nycklar, cache-uppdateringar.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Optimerade samlings-cache-uppdateringar.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Stöd har lagts till för att hämta inre undantags meddelande från begärd diagnostisk sträng.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Introducerade versions-API på PartitionKeyDefinition.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Separat timeout-stöd har lagts till för direkt läge.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Använder null-felmeddelande från tjänst och genererar dokument klient undantag.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Förbättring av socket-anslutning, tillägg av SoKeepAlive default True.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Stöd för förfrågnings diagnos stöd har lagts till.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Fast bugg i PartitionKey för hash v2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Stöd har lagts till för sammansatta index.
* Åtgärdat fel i global slut punkts hanterare för att framtvinga uppdatering.
* Fast bugg för upsertar med för-villkor i direkt läge.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Fast bugg i Gateway-diskcachen.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Skriv stöd i flera regioner har lagts till för direkt läge.
* Stöd har lagts till för hantering av IOExceptions utlöst som ServiceUnavailable-undantag från en proxy.
* Åtgärdade ett fel i återförsöks principen för slut punkts identifiering.
* En bugg har åtgärd ATS för att se till att undantag för null-pekare inte genereras i BaseDatabaseAccountConfigurationProvider.
* En bugg har åtgärd ATS för att säkerställa att QueryIterator inte returnerar null-värden.
* En bugg har åtgärd ATS för att säkerställa att stora PartitionKey tillåts

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Skriv stöd för flera regioner har lagts till i Gateway-läge.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Ett fel har åtgärd ATS i nyckel intervall för Läs partition för en fråga.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Ett fel uppstod vid inställning av huvud storlek för fortsättnings-token i DirectHttps-läge.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Stöd för direkt uppspelning har lagts till.
* Stöd har lagts till för anpassade metadata.
* Förbättrad bearbetnings logik för sessioner.
* Ett fel har åtgärd ATS i cacheminnet för partitionens nyckel intervall.
* Ett NPE-fel har åtgärd ATS i direkt läge.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Stöd har lagts till för unikt index.
* Stöd har lagts till för att begränsa den önskade token-storleken i matnings alternativ.
* En bugg har åtgärd ATS i JSON-serialisering (timestamp).
* En bugg har åtgärd ATS i JSON-serialisering (Enum).
* Beroende av com. fasterxml. Jackson. Core: Jackson-DataBind uppgraderat till 2.9.5.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Förbättrad anslutningspoolen för direkt läge.
* Förbättrad för hämtnings förbättring för icke-OrderBy Cross partition-fråga.
* Förbättrad UUID-generering.
* Förbättrad konsekvens logik för sessioner.
* Stöd har lagts till för multipolygoner.
* Stöd har lagts till för statistik för partitionsnyckel i samlingen.
* Ett fel har åtgärd ATS i stöd för flera regioner.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Förbättrad prestanda för JSON-serialisering.
* Den här SDK-versionen kräver att den senaste versionen av Azure Cosmos DB emulator är tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Interna ändringar för Microsoft-vänners bibliotek.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Ett problem har åtgärd ATS vid läsning av nyckel intervall för enskilda partitioner.
* Ett problem har åtgärd ATS i ResourceID-parsning som påverkar databasen med korta namn.
* En problem åtgärd orsakades av partitionsnyckel.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Viktiga fel korrigeringar för att begära bearbetning under partitions delning.
* Ett problem har åtgärd ATS med starka och BoundedStaleness konsekvens nivåer.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Stöd har lagts till för en ny konsekvens nivå som kallas Konsekvensprincip.
* Ett fel uppstod vid läsning av insamling i sessionsläge.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Aktiverat stöd för partitionerad samling med så låg som 2 500 RU/s och skala i steg om 100 RU/SEK.
* En bugg har åtgärd ATS i den inbyggda sammansättningen som kan orsaka NullRef-undantag i vissa frågor.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* En bugg har åtgärd ATS i konfigurationen för frågemotor som kan orsaka undantag för frågor i Gateway-läge.
* Korrigerade några buggar i sessions-behållaren som kan orsaka att det inte går att hitta en "ägare resurs"-undantag för förfrågningar direkt efter att samlingen har skapats.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Stöd har lagts till för agg regerings frågor (antal, MIN, MAX, SUM och AVG). Se [agg regerings stöd](sql-query-aggregates.md).
* Stöd har lagts till för ändrings flöde.
* Stöd har lagts till för samlings kvot information via RequestOptions. setPopulateQuotaInfo.
* Stöd för skript loggning för lagrad procedur har lagts till via RequestOptions. setScriptLoggingEnabled.
* Ett fel har åtgärd ATS där fråga i DirectHttps-läge kan sluta svara vid upptäckt av begränsnings fel.
* Ett fel har åtgärd ATS i konsekvens läge för sessioner.
* En bugg har åtgärd ATS som kan orsaka NullReferenceException i HttpContext när begär ande frekvens är hög.
* Förbättrad prestanda för DirectHttps-läge.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Stöd för enkel klient instans baserad proxy har lagts till med ConnectionPolicy. setProxy () API.
* Har lagt till DocumentClient. Close () API för att stänga av DocumentClient-instansen korrekt.
* Bättre prestanda för frågor i direkt anslutnings läge genom att härleda frågeuttrycket från den inbyggda sammansättningen i stället för gatewayen.
* Ange FAIL_ON_UNKNOWN_PROPERTIES = falskt så att användarna inte behöver definiera JsonIgnoreProperties i sina POJO.
* Omfaktorad loggning för att använda SLF4J.
* Åtgärdade några andra buggar i konsekvens läsaren.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Ett fel har åtgärd ATS i anslutnings hanteringen för att förhindra anslutnings läckor i direkt anslutnings läge.
* Ett fel har åtgärd ATS i den översta frågan där det kan utlösa NullReference-undantag.
* Bättre prestanda genom att minska antalet nätverks anrop för interna cacheminnen.
* Har lagt till status kod, ActivityID och begärande-URI i DocumentClientException för bättre fel sökning.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Ett problem har åtgärd ATS i anslutnings hanteringen för stabilitet.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Stöd har lagts till för BoundedStaleness konsekvens nivå.
* Stöd har lagts till för direkt anslutning för CRUD-åtgärder för partitionerade samlingar.
* Ett fel har åtgärd ATS i frågan om en databas med SQL.
* En bugg har åtgärd ATS i cachen där sessionstoken kan anges felaktigt.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Stöd har lagts till för parallella frågor över partitioner.
* Stöd har lagts till för överkant/sortera efter frågor för partitionerade samlingar.
* Stöd har lagts till för stark konsekvens.
* Stöd har lagts till för namnbaserade begär anden när direkt anslutning används.
* Åtgärdat för att göra ActivityId överkonsekvent över alla begär Anden om försök.
* Åtgärdade ett fel som är relaterat till sessionstoken när du återskapar en samling med samma namn.
* Tillagda polygon-och lin Est ring-datatyper när du angett samlings indexerings princip för geografiska frågor för geo-avgränsning.
* Problem med java doc för Java 1,8 har åtgärd ATS.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* En bugg har åtgärd ATS i PartitionKeyDefinitionMap för att cachelagra enskilda partitionsuppsättningar och inte göra extra hämtningar av partitionsnyckel.
* Ett fel har åtgärd ATS för att inte försöka igen när ett felaktigt värde angavs för partitionsnyckel.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Stöd har lagts till för databas konton med flera regioner.
* Stöd har lagts till för automatiskt nya försök vid begränsade begär Anden med alternativ för att anpassa de maximala nya försöken och vänte tiden för Max antal försök.  Se RetryOptions och ConnectionPolicy. getRetryOptions ().
* Föråldrad IPartitionResolver-baserad anpassad partitionerings kod. Använd partitionerade samlingar för högre lagring och data flöde.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Princip stöd för återförsök har lagts till för hastighets begränsning.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Stöd för TTL (Time to Live) för dokument har lagts till.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementerade [partitionerade samlingar](partition-data.md) och [användardefinierade prestanda nivåer](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* En bugg har åtgärd ATS i HashPartitionResolver för att generera hash-värden i lite-endian för att vara konsekventa med andra SDK: er.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Lägg till hash & intervall partition matchare för att hjälpa till med horisontell partitionering-program över flera partitioner.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementera upsert. Nya upsertXXX-metoder har lagts till som stöd för upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar internt.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versionen hoppades över för att placera versions numret i justering med andra SDK: er

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Har stöd för GeoSpatialt index
* Verifierar ID-egenskapen för alla resurser. ID: n för resurser får inte innehålla?,/, #, \, tecken eller sluta med ett blank steg.
* Lägger till det nya huvudet "indexera omvandlings förlopp" i ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementerar v2-indexerings princip

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Datum för lansering och indragning
Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK: n, eftersom det rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

Alla förfrågningar till Cosmos DB med hjälp av en tillbakadragen SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner **1. x** av SQL SDK för Java kommer att dras tillbaka den **30 maj 2020**.
> 
>

> [!WARNING]
> Alla versioner av SQL SDK för Java före version **1.0.0** drogs tillbaka den **29 februari 2016**.
> 
> 

<br/>

| Version | Lanserings datum | Datum för indragning |
| --- | --- | --- |
| [2.1.3](#2.1.3) |Mar 13, 2018 |--- |
| [punkt](#2.1.2) |Mar 09, 2018 |--- |
| [punkt](#2.1.1) |Dec 13, 2018 |--- |
| [2.1.0](#2.1.0) |Nov 20, 2018 |--- |
| [2.0.0](#2.0.0) |21 september 2018 |--- |
| [1.16.4](#1.16.4) |10 september 2018 |30 maj 2020 |
| [1.16.3](#1.16.3) |2018 september, |30 maj 2020 |
| [1.16.2](#1.16.2) |29 juni 2018 |30 maj 2020 |
| [1.16.1](#1.16.1) |16 maj 2018 |30 maj 2020 |
| [1.16.0](#1.16.0) |15 mars 2018 |30 maj 2020 |
| [1.15.0](#1.15.0) |Den 14 november 2017 |30 maj 2020 |
| [1.14.0](#1.14.0) |28 oktober 2017 |30 maj 2020 |
| [1.13.0](#1.13.0) |25 augusti 2017 |30 maj 2020 |
| [1.12.0](#1.12.0) |11 juli 2017 |30 maj 2020 |
| [1.11.0](#1.11.0) |10 maj 2017 |30 maj 2020 |
| [1.10.0](#1.10.0) |11 mars 2017 |30 maj 2020 |
| [1.9.6](#1.9.6) |21 februari 2017 |30 maj 2020 |
| [1.9.5](#1.9.5) |31 januari 2017 |30 maj 2020 |
| [1.9.4](#1.9.4) |24 november 2016 |30 maj 2020 |
| [1.9.3](#1.9.3) |30 oktober 2016 |30 maj 2020 |
| [1.9.2](#1.9.2) |28 oktober 2016 |30 maj 2020 |
| [1.9.1](#1.9.1) |26 oktober 2016 |30 maj 2020 |
| [1.9.0](#1.9.0) |Den 03 oktober 2016 |30 maj 2020 |
| [1.8.1](#1.8.1) |30 juni 2016 |30 maj 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 maj 2020 |
| [1.7.1](#1.7.1) |30 april 2016 |30 maj 2020 |
| [1.7.0](#1.7.0) |27 april 2016 |30 maj 2020 |
| [1.6.0](#1.6.0) |29 mars 2016 |30 maj 2020 |
| [1.5.1](#1.5.1) |31 december 2015 |30 maj 2020 |
| [1.5.0](#1.5.0) |Den 04 december 2015 |30 maj 2020 |
| [1.4.0](#1.4.0) |Den 05 oktober 2015 |30 maj 2020 |
| [1.3.0](#1.3.0) |Den 05 oktober 2015 |30 maj 2020 |
| [1.2.0](#1.2.0) |Den 05 augusti 2015 |30 maj 2020 |
| [1.1.0](#1.1.0) |9 juli 2015 |30 maj 2020 |
| 1.0.1 |12 maj 2015 |30 maj 2020 |
| [1.0.0](#1.0.0) |07, 2015 |30 maj 2020 |
| 0.9.5 – för-leasing |Mar 09, 2015 |29 februari 2016 |
| 0.9.4 – för-leasing |17 februari 2015 |29 februari 2016 |
| 0.9.3 – för-leasing |13 januari 2015 |29 februari 2016 |
| 0.9.2 – för-leasing |19 december 2014 |29 februari 2016 |
| 0.9.1 till och – för-leasing |19 december 2014 |29 februari 2016 |
| 0.9.0 – för-leasing |10 december 2014 |29 februari 2016 |

## <a name="faq"></a>FAQ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.


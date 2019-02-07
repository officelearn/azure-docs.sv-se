---
title: 'Azure Cosmos DB: SQL Java API, SDK och resurser'
description: Lär dig allt om SQL Java API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 11/29/2018
ms.author: sngun
ms.openlocfilehash: ebb6fe9f583c2658f187d3cca1185f1525fb1bf5
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55811043"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK för SQL-API: Viktig information och resurser
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
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

SQL API Java SDK har stöd för synkrona åtgärder. Använd för asynkron support den [SQL API Async Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**Hämta SDK**|[Maven 3.](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentation**|[Java API-referensdokumentation](/java/api/com.microsoft.azure.documentdb)|
|**Bidra till SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Kom igång**|[Kom igång med Java SDK](sql-api-java-get-started.md)|
|**Självstudier om webbappen**|[Utveckling av webbappar med Azure Cosmos DB](sql-api-java-application.md)|
|**Lägsta stödda körningar**|[Java Development Kit (JDK 7 +)](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Viktig information

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Stöd för flera regioner skrivning har lagts till för direkt-läge.
* Stöd har lagts till för att hantera IOExceptions genereras som ServiceUnavailable undantag från en proxy.
* Ett fel har åtgärdats i återförsöksprincipen för slutpunkt för identifiering.
* Ett fel om du vill se till att null-pekare undantag inte utlöses i BaseDatabaseAccountConfigurationProvider har åtgärdats.
* En bugg för att säkerställa att fråga Iteratorn inte returnerar null-värden.
* En bugg så stora PartitionKey tillåts

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Stöd för flera regioner skrivning har lagts till för gateway-läge.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Ett fel har åtgärdats i Läs partition nyckel intervall för en fråga.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Ett fel vid konfigurationen fortsättning token huvudstorlek i DirectHttps läge har åtgärdats.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Lägga till strömmande misslyckas över support.
* Tillagt stöd för anpassade metadata.
* Förbättrad hantering av logic-session.
* Ett fel har åtgärdats i partitionen nyckelintervall cache.
* Ett NPE-fel har åtgärdats i direkt-läge.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Stöd har lagts till för unikt Index.
* Stöd har lagts till för att begränsa fortsättning token storlek i feed-alternativ.
* Ett fel har åtgärdats i Json-serialisering (tidsstämpel).
* Ett fel har åtgärdats i Json-serialisering (uppräkning).
* Beroende på com.fasterxml.jackson.core:jackson-databind uppgraderas till 2.9.5.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Förbättrad anslutningspooler för direkt-läge.
* Förbättrat Prefetch förbättring för orderby mellan partitionsfrågan.
* Förbättrad UUID generation.
* Förbättrad logik för sessionen konsekvens.
* Stöd har lagts till för multipolygon.
* Stöd har lagts till för Partitionsstatistik nyckel intervallet för samlingen.
* Ett fel har åtgärdats i stöd för flera regioner.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Förbättrad prestanda för Json-serialisering.
* Den SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Interna ändringar för Microsoft vänner bibliotek.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Ett problem vid läsning av enskild partition nyckelintervall har åtgärdats.
* Ett problem har åtgärdats i ResourceID påverkar parsning som databasen med kortnamn.
* Åtgärdat ett problem orsak av partitionen nyckelkodning.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Viktiga felkorrigeringar för behandling under delar upp partition.
* Ett problem har åtgärdats med konsekvensnivåer stark och BoundedStaleness.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Stöd har lagts till för en ny konsekvensnivå kallas ConsistentPrefix.
* Ett fel vid läsning av samlingen i sessionsläge har åtgärdats.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Aktiverar stöd för partitionerad samling med som låg som 2 500 RU/sek och skala i steg om 100 RU/sek.
* Ett fel har åtgärdats i den interna sammansättningen vilket kan orsaka NullRef undantag i några frågor.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Ett fel har åtgärdats i konfigurationen för frågan-motor som kan orsaka undantag för frågor i Gateway-läge.
* Korrigerat några fel i sessionen behållaren som kan orsaka ett ”ägare resursen hittades inte”-undantag för begäranden omedelbart när samlingen har skapats.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Stöd har lagts till för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig). Se [aggregering support](how-to-sql-query.md#Aggregates).
* Tillagt stöd för ändringsfeed.
* Stöd har lagts till för samlingen kvotinformation via RequestOptions.setPopulateQuotaInfo.
* Stöd har lagts till för lagrad procedur skriptet loggning via RequestOptions.setScriptLoggingEnabled.
* Ett fel har åtgärdats där frågan i DirectHttps läge låser sig när den påträffar begränsning fel.
* Ett fel har åtgärdats i sessionsläge för konsekvens.
* Ett fel som kan orsaka ett Nullreferensundantag i HttpContext när frågehastigheten är för hög har åtgärdats.
* Förbättrad prestanda för DirectHttps läge.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Har lagts till enkel instans-baserade proxyservrar klientstöd med ConnectionPolicy.setProxy() API.
* Har lagts till DocumentClient.close() API till korrekt avstängning DocumentClient-instans.
* Bättre prestanda för frågor i läget för direkt anslutning av som härleds frågeplanen från den interna sammansättningen i stället för gatewayen.
* Ange FAIL_ON_UNKNOWN_PROPERTIES = false så användarna inte behöver definiera JsonIgnoreProperties i sina POJO.
* Omstrukturerade loggning för att använda SLF4J.
* Korrigerat några andra fel i konsekvens reader.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Ett fel har åtgärdats i anslutningshanteringen att förhindra läckage av anslutning i direktanslutning läge.
* Ett fel har åtgärdats i övre frågan där det kan utlösa NullReference undantag.
* Bättre prestanda genom att minska antalet nätverksanrop för det interna cacheminnet.
* Har lagts till statuskoden, ActivityID och begära URI: N i DocumentClientException för bättre felsökning.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Ett problem har åtgärdats i anslutningshanteringen för stabilitet.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Stöd har lagts till för BoundedStaleness konsekvensnivå.
* Tillagt stöd för direkt anslutning för CRUD-åtgärder för partitionerade samlingar.
* En bugg i fråga en databas med SQL.
* Ett fel har åtgärdats i sessionscachen var sessionstoken kan anges felaktigt.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Tillagt stöd för olika plattformar partition parallella frågor.
* Stöd har lagts till för TOP/ORDER BY-frågor för partitionerade samlingar.
* Tillagt stöd för stark konsekvens.
* Stöd har lagts till för namn baserat på begäranden när du använder direkt anslutning.
* Fast att vara konsekvent över alla anropsförsök ActivityId.
* En bugg som rör sessionscachen när återskapa en samling med samma namn.
* Har lagts till Polygon och LineString DataTypes när du anger samling indexeringspolicy för geografiska avgränsningar rumsliga förfrågningar.
* Åtgärdade problem med Java-dokument för Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Ett fel har åtgärdats i PartitionKeyDefinitionMap att cachelagra enskilda partitionssamlingar och inte göra några extra fetch partitionera viktiga förfrågningar.
* Ett fel och försök igen när en felaktig partitionsnyckelvärde tillhandahålls inte har åtgärdats.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Lagt till stöd för flera regioner.
* Tillagt stöd för automatiska återförsök på begränsade begäranden med alternativ för att anpassa max omförsök och försök väntetid.  Se RetryOptions och ConnectionPolicy.getRetryOptions().
* Föråldrad IPartitionResolver baserat Anpassad partitionering kod. Använd partitionerade samlingar för högre lagring och dataflöde.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Har lagts till återförsök princip stöd för hastighetsbegränsning.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Extra tid att live (TTL)-stöd för dokument.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Implementerat [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Ett fel har åtgärdats i HashPartitionResolver att generera hash-värden i little endian för att överensstämma med andra SDK: er.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Lägg till Hash & intervall partitions-matchare som hjälper till med horisontell partitionering program över flera partitioner.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Implementera Upsert. Nya upsertXXX-metoder som lagts till stöd för Upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar som är interna.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Versionen som hoppades över för att lägga till versionsnumret i linje med andra SDK: er

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Har stöd för geospatiala Index
* Verifierar id-egenskapen för alla resurser. ID: n för resurser får inte innehålla?, /, #, \, tecken eller sluta med ett blanksteg.
* Lägger till ny rubrik ”index omvandling status” ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Implementerar V2 indexeringsprincip

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK

## <a name="release-and-retirement-dates"></a>Versionen och dras tillbaka datum
Microsoft meddelar meddelande minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

Varje begäran till Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner av SQL-SDK för Java före version **1.0.0** drogs tillbaka **29 februari 2016**.
> 
> 

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| 2.1.1 |Den 21 november 2018 |--- |
| [2.0.0](#2.0.0) |Den 21 september 2018 |--- |
| [1.16.4](#1.16.4) |Den 10 september 2018 |--- |
| [1.16.3](#1.16.3) |09 september 2018 |--- |
| [1.16.2](#1.16.2) |Den 29 juni 2018 |--- |
| [1.16.1](#1.16.1) |16 maj 2018 |--- |
| [1.16.0](#1.16.0) |Den 15 mars 2018 |--- |
| [1.15.0](#1.15.0) |Den 14 november 2017 |--- |
| [1.14.0](#1.14.0) |Den 28 oktober 2017 |--- |
| [1.13.0](#1.13.0) |Den 25 augusti 2017 |--- |
| [1.12.0](#1.12.0) |11 juli 2017 |--- |
| [1.11.0](#1.11.0) |10 maj 2017 |--- |
| [1.10.0](#1.10.0) |11 mars 2017 |--- |
| [1.9.6](#1.9.6) |Den 21 februari 2017 |--- |
| [1.9.5](#1.9.5) |Den 31 januari 2017 |--- |
| [1.9.4](#1.9.4) |24 november 2016 |--- |
| [1.9.3](#1.9.3) |Den 30 oktober 2016 |--- |
| [1.9.2](#1.9.2) |Den 28 oktober 2016 |--- |
| [1.9.1](#1.9.1) |26 oktober 2016 |--- |
| [1.9.0](#1.9.0) |03 oktober 2016 |--- |
| [1.8.1](#1.8.1) |Den 30 juni 2016 |--- |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |--- |
| [1.7.1](#1.7.1) |Den 30 april 2016 |--- |
| [1.7.0](#1.7.0) |Den 27 april 2016 |--- |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |--- |
| [1.5.1](#1.5.1) |Den 31 december 2015 |--- |
| [1.5.0](#1.5.0) |04 december 2015 |--- |
| [1.4.0](#1.4.0) |05 oktober 2015 |--- |
| [1.3.0](#1.3.0) |05 oktober 2015 |--- |
| [1.2.0](#1.2.0) |05 augusti 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| 1.0.1 |Den 12 maj 2015 |--- |
| [1.0.0](#1.0.0) |07 april 2015 |--- |
| 0.9.5-prelease |09 mars 2015 |Den 29 februari 2016 |
| 0.9.4-prelease |17 februari 2015 |Den 29 februari 2016 |
| 0.9.3-prelease |13 januari 2015 |Den 29 februari 2016 |
| 0.9.2-prelease |19 december 2014 |Den 29 februari 2016 |
| 0.9.1-prelease |19 december 2014 |Den 29 februari 2016 |
| 0.9.0-prelease |10 december 2014 |Den 29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.


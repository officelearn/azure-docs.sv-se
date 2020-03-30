---
title: 'Azure Cosmos DB: SQL Java API, SDK & resurser'
description: Lär dig allt om SQL Java API och SDK inklusive utgivningsdatum, pensioneringsdatum och ändringar som görs mellan varje version av Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 02/21/2020
ms.author: sngun
ms.openlocfilehash: 514982727509788918c159e07f8061962df32336
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77558936"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK för SQL API: Versionsanteckningar och resurser
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

SQL API Java SDK stöder synkronåtgärder. Använd [SQL API Async Java SDK](sql-api-sdk-async-java.md)för asynkront stöd. 

| |  |
|---|---|
|**SDK Ladda ner**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-dokumentation**|[Dokumentation för Java API-referens](/java/api/com.microsoft.azure.documentdb)|
|**Bidra till SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Kom igång**|[Komma igång med Java SDK](sql-api-java-get-started.md)|
|**Självstudiekurs för webbappar**|[Webbprogramutveckling med Azure Cosmos DB](sql-api-java-application.md)|
|**Minsta körning som stöds**|[Java Development Kit (JDK) 7+](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Viktig information

### <a name="247"></a><a name="2.4.7"/>2.4.7
* Åtgärdar timeout-problemet för anslutningspoolen.
* Åtgärdar uppdatering av auth-token vid interna återförsök.

### <a name="246"></a><a name="2.4.6"/>2.4.6
* Uppdaterade korrekt replikprinciptagg på klientsidan på databasenKonto och gjorde att databasenKontokonfiguration läser från cacheminnet.

### <a name="245"></a><a name="2.4.5"/>2.4.5
* Undvika återförsök på ogiltig partition nyckelintervall fel, om användaren ger pkRangeId.

### <a name="244"></a><a name="2.4.4"/>2.4.4
* Optimerad partition nyckelintervall cache uppdateras.
* Åtgärdar scenariot där SDK inte underhåller partitionsdelningstips från servern och resulterar i att felaktiga routningscachar på klientsidan uppdateras.

### <a name="242"></a><a name="2.4.2"/>2.4.2
* Optimerad samlingscache uppdateras.

### <a name="241"></a><a name="2.4.1"/>2.4.1
* Lade till stöd för att hämta inre undantagsmeddelande från diagnostiksträngen för begäran.

### <a name="240"></a><a name="2.4.0"/>2.4.0
* Introducerade versions-API på PartitionKeyDefinition.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* Lade till separat timeout-stöd för direktläge.

### <a name="223"></a><a name="2.2.3"/>2.2.3
* Använda null-felmeddelande från tjänsten och ta fram undantag från dokumentklienten.

### <a name="222"></a><a name="2.2.2"/>2.2.2
* Anslutningsförbättring för socket, lägga till SoKeepAlive standard true.

### <a name="220"></a><a name="2.2.0"/>2.2.0
* Lagt till stöd för sträng för begäranssträng.

### <a name="213"></a><a name="2.1.3"/>2.1.3
* Fast fel i PartitionKey för Hash V2.

### <a name="212"></a><a name="2.1.2"/>2.1.2
* Lagt till stöd för sammansatta index.
* Fast fel i global slutpunktshanterare för att tvinga uppdatera.
* Fast bugg för upserts med förförhållanden i direktläge.

### <a name="211"></a><a name="2.1.1"/>2.1.1
* Fast fel i gateway adresscachen.

### <a name="210"></a><a name="2.1.0"/>2.1.0
* Skrivstöd för flera regioner har lagts till för direktläge.
* Lade till stöd för hantering av IOExceptions som serviceovailable undantag, från en proxy.
* Fixade en felrapport i principen för återförsök för slutpunktsidentifiering.
* Fixade ett fel för att säkerställa att null-pekarundantag inte genereras i BaseDatabaseAccountConfigurationProvider.
* Fixade ett fel för att kontrollera att QueryIterator inte returnerar nulls.
* Fast en bugg för att säkerställa stora PartitionKey är tillåtet

### <a name="200"></a><a name="2.0.0"/>2.0.0
* Skrivstöd för flera regioner har lagts till för gateway-läge.

### <a name="1164"></a><a name="1.16.4"/>1.16.4
* Fixade ett fel i Läs partitionsnyckelintervall för en fråga.

### <a name="1163"></a><a name="1.16.3"/>1.16.3
* Fixade ett fel vid inställning av fortsättningstokenrubrikstorlek i direkthttps-läge.

### <a name="1162"></a><a name="1.16.2"/>1.16.2
* Tillagd direktuppspelning överstöj.
* Lagt till stöd för anpassade metadata.
* Förbättrad sessionshanteringslogik.
* Fixade en bugg i partitionsnyckelintervallcachen.
* Fixade ett NPE-fel i direktläge.

### <a name="1161"></a><a name="1.16.1"/>1.16.1
* Lagt till stöd för Unikt index.
* Lagt till stöd för att begränsa fortsättningstokensstorlek i matningsalternativ.
* Fixade ett fel i Json Serialization (tidsstämpel).
* Fixade en bugg i Json Serialization (uppräkning).
* Beroendet av com.fasterxml.jackson.core:jackson-databind uppgraderas till 2.9.5.

### <a name="1160"></a><a name="1.16.0"/>1.16.0
* Förbättrad anslutningspoolning för direktläge.
* Förbättrad prefetch förbättring för icke-orderav kors partition fråga.
* Förbättrad UUID generation.
* Förbättrad sessionskonsekvenslogik.
* Lagt till stöd för multipolygon.
* Lagt till stöd för partitionsnyckelintervallstatistik för insamling.
* Fixade ett fel i stöd för flera regioner.

### <a name="1150"></a><a name="1.15.0"/>1.15.0
* Förbättrad Json Serialization prestanda.
* Den här SDK-versionen kräver den senaste versionen av https://aka.ms/cosmosdb-emulatorAzure Cosmos DB Emulator som är tillgänglig för hämtning från .

### <a name="1140"></a><a name="1.14.0"/>1.14.0
* Interna ändringar för Microsoft-vänner bibliotek.

### <a name="1130"></a><a name="1.13.0"/>1.13.0
* Åtgärdade ett problem vid läsning av enpartitionsnyckelintervall.
* Åtgärdade ett problem i ResourceID-tolkning som påverkar databasen med korta namn.
* Åtgärdade ett problem orsak genom partition nyckelkodning.

### <a name="1120"></a><a name="1.12.0"/>1.12.0
* Kritiska buggfixar för att begära bearbetning under partitionsdelningar.
* Fixade ett problem med konsekvensnivåerna Stark och Begränsadstyrka.

### <a name="1110"></a><a name="1.11.0"/>1.11.0
* Lade till stöd för en ny konsekvensnivå som kallas ConsistentPrefix.
* Fixade ett fel i läsningen i sessionsläge.

### <a name="1100"></a><a name="1.10.0"/>1.10.0
* Aktiverat stöd för partitionerad samling med så lågt som 2 500 RU/sek och skala i steg om 100 RU/sek.
* Fixade ett fel i den inbyggda sammansättningen som kan orsaka NullRef-undantag i vissa frågor.

### <a name="196"></a><a name="1.9.6"/>1.9.6
* Fixade ett fel i frågemotorkonfigurationen som kan orsaka undantag för frågor i gateway-läge.
* Fixade några fel i sessionsbehållaren som kan orsaka ett undantag för ägarresursen för begäranden direkt efter att samlingen har skapats.

### <a name="195"></a><a name="1.9.5"/>1.9.5
* Lade till stöd för aggregeringsfrågor (ANTAL, MIN, MAX, SUMMA och AVG). Se [Stöd för aggregering](sql-query-aggregates.md).
* Lagt till stöd för ändringsflödet.
* Lade till stöd för information om insamlingskvoter via RequestOptions.setPopulateQuotaInfo.
* Lagt till stöd för lagrad procedurskriptloggning via RequestOptions.setScriptLoggingEnabled.
* Fixade ett fel där frågan i direkthttpsna läge kan sluta svara när du stöter på begränsningsfel.
* Fixade ett fel i sessionskonsekvensläge.
* Fixade ett fel som kan orsaka NullReferenceException i HttpContext när begäranden är hög.
* Förbättrad prestanda för directhttps-läge.

### <a name="194"></a><a name="1.9.4"/>1.9.4
* Lade till enkelt klientinstansbaserat proxystöd med ConnectionPolicy.setProxy()API.
* Lade till DocumentClient.close() API för att korrekt stänga DocumentClient-instansen.
* Förbättrade frågeprestanda i direkt anslutningsläge genom att härleda frågeplanen från den inbyggda sammansättningen i stället för gatewayen.
* Ange FAIL_ON_UNKNOWN_PROPERTIES = falskt så att användarna inte behöver definiera JsonIgnoreProperties i sin POJO.
* Refactored loggning för att använda SLF4J.
* Fast några andra buggar i konsekvens läsare.

### <a name="193"></a><a name="1.9.3"/>1.9.3
* Fixade ett fel i anslutningshanteringen för att förhindra anslutningsläckor i direkt anslutningsläge.
* Fixade ett fel i TOP-frågan där det kan utlösa NullReference-undantag.
* Förbättrad prestanda genom att minska antalet nätverkssamtal för interna cacheminnen.
* Lade till statuskod, ActivityID och Request URI i DocumentClientException för bättre felsökning.

### <a name="192"></a><a name="1.9.2"/>1.9.2
* Fixade ett problem i anslutningshanteringen för stabilitet.

### <a name="191"></a><a name="1.9.1"/>1.9.1
* Lagt till stöd för BoundedStaleness konsekvensnivå.
* Lade till stöd för direkt anslutning för CRUD-åtgärder för partitionerade samlingar.
* Fixade ett fel i att fråga en databas med SQL.
* Fixade ett fel i sessionscachen där sessionstoken kan anges felaktigt.

### <a name="190"></a><a name="1.9.0"/>1.9.0
* Lagt till stöd för parallella frågor mellan partitioner.
* Lade till stöd för TOP/ORDER BY-frågor för partitionerade samlingar.
* Extra stöd för stark konsekvens.
* Lade till stöd för namnbaserade begäranden när du använder direkt anslutning.
* Fast att göra ActivityId hålla konsekvent över alla begärandeförsök.
* Fixade ett fel som var relaterat till sessionscachen när du återskapade en samling med samma namn.
* Lade till Polygon- och LineString DataTypes samtidigt som du anger samlingsindexeringsprincip för rumsliga geostängselfrågor.
* Fasta problem med Java Doc för Java 1.8.

### <a name="181"></a><a name="1.8.1"/>1.8.1
* Fixade en bugg i PartitionKeyDefinitionMap för att cachelagra enpartitionssamlingar och inte göra extra begäranden om hämtning av partitionsnyckel.
* Fixade ett fel för att inte försöka igen när ett felaktigt partitionsnyckelvärde angavs.

### <a name="180"></a><a name="1.8.0"/>1.8.0
* Lade till stöd för databaskonton med flera regioner.
* Lagt till stöd för automatiska återförsök på begränsade begäranden med alternativ för att anpassa max försök att försöka igen och max återförsök väntetid.  Se RetryOptions och ConnectionPolicy.getRetryOptions().
* Inaktuell IPartitionResolver-baserad anpassad partitioneringskod. Använd partitionerade samlingar för högre lagring och dataflöde.

### <a name="171"></a><a name="1.7.1"/>1.7.1
* Lade till principstöd för återförsök för hastighetsbegränsning.  

### <a name="170"></a><a name="1.7.0"/>1.7.0
* Lägga tid att leva (TTL) stöd för dokument.

### <a name="160"></a><a name="1.6.0"/>1.6.0
* Implementerade [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md).

### <a name="151"></a><a name="1.5.1"/>1.5.1
* Fixade ett fel i HashPartitionResolver för att generera hash-värden i little-endian för att vara konsekventa med andra SDK:er.

### <a name="150"></a><a name="1.5.0"/>1.5.0
* Lägg till Hash & Range partition resolvers för att hjälpa till med fragmentering program över flera partitioner.

### <a name="140"></a><a name="1.4.0"/>1.4.0
* Implementera Upsert. Nya upsertXXX-metoder som lagts till för att stödja Upsert-funktionen.
* Implementera ID-baserad routning. Inga offentliga API-ändringar, alla ändringar interna.

### <a name="130"></a><a name="1.3.0"/>1.3.0
* Släpp hoppas över för att få versionsnummer i linje med andra SDK:er

### <a name="120"></a><a name="1.2.0"/>1.2.0
* Stöder geospatialt index
* Validerar ID-egenskap för alla resurser. Id:er för resurser får inte \, innehålla?, /, #, tecken eller med ett blanksteg.
* Lägger till nya rubriken "indexomvandlingsförlopp" i ResourceResponse.

### <a name="110"></a><a name="1.1.0"/>1.1.0
* Implementerar V2-indexeringsprincip

### <a name="100"></a><a name="1.0.0"/>1.0.0
* GA SDK

## <a name="release-and-retirement-dates"></a>Utgivnings- och pensionsdatum
Microsoft kommer att meddela minst **12 månader** innan du går i pension en SDK för att underlätta övergången till en nyare / stöds version.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK, som sådan är det rekommendera att du alltid uppgradera till den senaste SDK-versionen så tidigt som möjligt.

Alla förfrågningar till Cosmos DB med hjälp av en pensionerad SDK kommer att avvisas av tjänsten.

> [!WARNING]
> Alla versioner **1.x** av SQL SDK för Java kommer att dras tillbaka den **30 maj 2020**.
> 
>

> [!WARNING]
> Alla versioner av SQL SDK för Java före version **1.0.0** drogs tillbaka den **29 februari 2016**.
> 
> 

<br/>

| Version | Utgivningsdatum | Pensionering Datum |
| --- | --- | --- |
| [2.4.7](#2.4.7) |Den 20 februari 2020 |--- |
| [2.4.6](#2.4.6) |den 24 januari 2020 |--- |
| [2.4.5](#2.4.5) |den 10 november 2019 |--- |
| [2.4.4](#2.4.4) |den 24 oktober 2019 |--- |
| [2.4.2](#2.4.2) |26 september 2019 |--- |
| [2.4.1](#2.4.1) |den 18 juli 2019 |--- |
| [2.4.0](#2.4.0) |den 4 maj 2019 |--- |
| [2.3.0](#2.3.0) |Den 24 april 2019 |--- |
| [2.2.3](#2.2.3) |Den 16 april 2019 |--- |
| [2.2.2](#2.2.2) |April 05, 2019 |--- |
| [2.2.0](#2.2.0) |den 27 mars 2019 |--- |
| [2.1.3](#2.1.3) |den 13 mars 2019 |--- |
| [2.1.2](#2.1.2) |den 9 mars 2019 |--- |
| [2.1.1](#2.1.1) |den 13 december 2018 |--- |
| [2.1.0](#2.1.0) |den 20 november 2018 |--- |
| [2.0.0](#2.0.0) |den 21 september 2018 |--- |
| [1.16.4](#1.16.4) |den 10 september 2018 |den 30 maj 2020 |
| [1.16.3](#1.16.3) |September 09, 2018 |den 30 maj 2020 |
| [1.16.2](#1.16.2) |den 29 juni 2018 |den 30 maj 2020 |
| [1.16.1](#1.16.1) |den 16 maj 2018 |den 30 maj 2020 |
| [1.16.0](#1.16.0) |den 15 mars 2018 |den 30 maj 2020 |
| [1.15.0](#1.15.0) |den 14 november 2017 |den 30 maj 2020 |
| [1.14.0](#1.14.0) |den 28 oktober 2017 |den 30 maj 2020 |
| [1.13.0](#1.13.0) |den 25 augusti 2017 |den 30 maj 2020 |
| [1.12.0](#1.12.0) |11 juli 2017 |den 30 maj 2020 |
| [1.11.0](#1.11.0) |10 maj 2017 |den 30 maj 2020 |
| [1.10.0](#1.10.0) |den 11 mars 2017 |den 30 maj 2020 |
| [1.9.6](#1.9.6) |den 21 februari 2017 |den 30 maj 2020 |
| [1.9.5](#1.9.5) |den 31 januari 2017 |den 30 maj 2020 |
| [1.9.4](#1.9.4) |den 24 november 2016 |den 30 maj 2020 |
| [1.9.3](#1.9.3) |den 30 oktober 2016 |den 30 maj 2020 |
| [1.9.2](#1.9.2) |den 28 oktober 2016 |den 30 maj 2020 |
| [1.9.1](#1.9.1) |den 26 oktober 2016 |den 30 maj 2020 |
| [1.9.0](#1.9.0) |den 3 oktober 2016 |den 30 maj 2020 |
| [1.8.1](#1.8.1) |den 30 juni 2016 |den 30 maj 2020 |
| [1.8.0](#1.8.0) |den 14 juni 2016 |den 30 maj 2020 |
| [1.7.1](#1.7.1) |den 30 april 2016 |den 30 maj 2020 |
| [1.7.0](#1.7.0) |den 27 april 2016 |den 30 maj 2020 |
| [1.6.0](#1.6.0) |den 29 mars 2016 |den 30 maj 2020 |
| [1.5.1](#1.5.1) |den 31 december 2015 |den 30 maj 2020 |
| [1.5.0](#1.5.0) |den 4 december 2015 |den 30 maj 2020 |
| [1.4.0](#1.4.0) |den 5 oktober 2015 |den 30 maj 2020 |
| [1.3.0](#1.3.0) |den 5 oktober 2015 |den 30 maj 2020 |
| [1.2.0](#1.2.0) |den 5 augusti 2015 |den 30 maj 2020 |
| [1.1.0](#1.1.0) |den 9 juli 2015 |den 30 maj 2020 |
| 1.0.1 |den 12 maj 2015 |den 30 maj 2020 |
| [1.0.0](#1.0.0) |den 7 april 2015 |den 30 maj 2020 |
| 0.9.5-prelease |den 9 mars 2015 |den 29 februari 2016 |
| 0.9.4-prelease |den 17 februari 2015 |den 29 februari 2016 |
| 0.9.3-prelease |den 13 januari 2015 |den 29 februari 2016 |
| 0.9.2-prelease |den 19 december 2014 |den 29 februari 2016 |
| 0.9.1-prelease |den 19 december 2014 |den 29 februari 2016 |
| 0.9.0-prelease |den 10 december 2014 |den 29 februari 2016 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns på tjänstsidan [för Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)


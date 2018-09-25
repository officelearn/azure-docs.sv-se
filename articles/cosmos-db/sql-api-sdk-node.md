---
title: 'Azure Cosmos DB: SQL Node.js API, SDK och resurser | Microsoft Docs'
description: Lär dig allt om SQL Node.js API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB Node.js SDK.
services: cosmos-db
author: deborahc
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6983b49135b5a8adbef1d0cfc5a407cb7b0c7ac
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46960557"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK för SQL-API: viktig information och resurser
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
> * [BulkExecutor – .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor – Java](sql-api-sdk-bulk-executor-java.md)

|Resurs  |Länk  |
|---------|---------|
|Hämta SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-dokumentation  |  [JavaScript SDK referensdokumentation](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Installationsinstruktioner för SDK  |  [Installationsinstruktioner](https://github.com/Azure/azure-cosmos-js#installation)
|Bidra till SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exempel | [Node.js-kodexempel](sql-api-nodejs-samples.md)
| Komma igång-Självstudier | [Kom igång med JavaScript-SDK](sql-api-nodejs-get-started.md)
| Självstudier om webbappen | [Skapa en Node.js-webbprogram med Azure Cosmos DB](sql-api-nodejs-application.md)
| Aktuella plattformar som stöds | [Node.js version 6.x](https://nodejs.org/en/blog/release/v6.10.3/) – krävs för SDK-Version 2.0.0 och senare.<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 

## <a name="release-notes"></a>Viktig information

### <a name="2.0.0"/>2.0.0</a>
* GA för Version 2.0.0 av JavaScript SDK
* Tillagt stöd för flera regioner skrivningar.

### <a name="2.0.0-3"/>2.0.0-3</a>
* RC1 version 2.0.0 av JavaScript SDK för en förhandsversion.
* Ny objektmodell med översta CosmosClient och metoder dela upp på relevanta klasser för databasen, behållare och objekt. 
* Stöd för [lovar](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK konverteras till TypeScript.

### <a name="1.14.4"/>1.14.4</a>
* npm dokumentationen korrigerad.

### <a name="1.14.3"/>1.14.3</a>
* Stöd har lagts till för standard återförsök på problem med anslutningen.
* Stöd har lagts till för att läsa samling ändringen flödet.
* Fast session konsekvens bugg som ibland orsakade ”Läs sessionen som är inte tillgänglig”.
* Stöd har lagts till för frågan mått.
* Ändra http-agenten maximalt antal anslutningar.

### <a name="1.14.2"/>1.14.2</a>
* Uppdaterad dokumentation-referensen Azure Cosmos DB i stället för Azure DocumentDB.
* Stöd har lagts till för proxyUrl inställning i ConnectionPolicy.

### <a name="1.14.1"/>1.14.1</a>
* Mindre korrigering för skiftlägeskänsliga filsystem.

### <a name="1.14.0"/>1.14.0</a>
* Lägger till stöd för Sessionskonsekvens.
* Den SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Dela proofed mellan partition frågor.
* Lägger till stöd för resurslänk med inledande och avslutande snedstreck (och motsvarande tester).

### <a name="1.12.2"/>1.12.2</a>
*   npm dokumentationen korrigerad.

### <a name="1.12.1"/>1.12.1</a>
* Ett fel har åtgärdats i executeStoredProcedure där dokument som är inblandade hade särskilda Unicode-tecken (LS, PS).
* Ett fel vid hantering av dokument med Unicode-tecken i Partitionsnyckeln har åtgärdats.
* Fast stöd för att skapa samlingar med namn på mediet. Github-ärende #114.
* Fast stöd för auktoriseringstoken för behörighet. Github-ärende #178.

### <a name="1.12.0"/>1.12.0</a>
* Lagt till stöd för en ny [konsekvensnivå](consistency-levels.md) kallas ConsistentPrefix.
* Stöd har lagts till för UriFactory.
* En bugg Unicode support. GitHub-ärende #171.

### <a name="1.11.0"/>1.11.0</a>
* Lagt till stöd för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig).
* Lägga till alternativet för att styra graden av parallellitet för mellan partition frågor.
* Lägga till alternativet för att inaktivera verifiering av SSL när du kör mot Azure Cosmos DB-emulatorn.
* Sänkte lägsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.
* Bugg i fortsättningen token för enskilda partitionssamlingar. Github-ärende #107.
* Bugg executeStoredProcedure vid hantering av 0 som enda param. Github-ärende #155.

### <a name="1.10.2"/>1.10.2</a>
* Fast Användaragent-rubriken för att inkludera den SDK-versionen.
* Rensning av mindre kod.

### <a name="1.10.1"/>1.10.1</a>
* Inaktivera verifiering av SSL när du använder SDK: N för att rikta emulator(hostname=localhost).
* Stöd har lagts till för att aktivera loggning för skriptet vid körningen av lagrad procedur.

### <a name="1.10.0"/>1.10.0</a>
* Tillagt stöd för olika plattformar partition parallella frågor.
* Stöd har lagts till för TOP/ORDER BY-frågor för partitionerade samlingar.

### <a name="1.9.0"/>1.9.0</a>
* Har lagts till återförsök princip stöd för begränsade begäranden. (Begränsade begäranden ta emot en begäran om frekvensen för stor undantag, felkod 429.) Som standard Azure Cosmos DB återförsök nio gånger för varje begäran när felkod 429 påträffas, respekterar retryAfter tiden i svarshuvudet. En fast återförsöksintervallet kan nu ställas in som en del av egenskapen RetryOptions på ConnectionPolicy objektet om du vill ignorera retryAfter tiden mellan återförsöken som returneras av servern. Azure Cosmos DB väntar nu upp till 30 sekunder för varje begäran som har begränsats (oavsett antal nya försök) och returnerar svaret med felkod 429. Nu kan åsidosättas i egenskapen RetryOptions ConnectionPolicy-objektet.
* Cosmos DB Returnerar nu x-ms-begränsning--antalet återförsök och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran att ange begränsningen försök antal och den kumulativ väntetid begäran mellan återförsöken.
* Klassen RetryOptions har lagts till, exponera RetryOptions-egenskapen för klassen ConnectionPolicy som kan användas för att åsidosätta en del av standardalternativen för återförsök.

### <a name="1.8.0"/>1.8.0</a>
* Lagt till stöd för flera regioner.

### <a name="1.7.0"/>1.7.0</a>
* Lagt till stöd för tid Live(TTL) funktion för dokument.

### <a name="1.6.0"/>1.6.0</a>
* Implementerat [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Bugg har åtgärdats RangePartitionResolver.resolveForRead där det inte returnerade länkar på grund av en felaktig concat resultat.

### <a name="1.5.5"/>1.5.5</a>
* Fast hashParitionResolver resolveForRead(): när inga partitionsnyckel som angetts utlöser undantag, istället för att returnera en lista över alla registrerade länkar.

### <a name="1.5.4"/>1.5.4</a>
* Åtgärdar problemet [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -dedikerade HTTPS-agenten: undvika att ändra globala agenten för Azure Cosmos DB. Använda en dedikerad agent för alla de lib-begäranden.

### <a name="1.5.3"/>1.5.3</a>
* Åtgärdar problemet [#81](https://github.com/Azure/azure-documentdb-node/issues/81) – korrekt hantera bindestreck i media-ID: n.

### <a name="1.5.2"/>1.5.2</a>
* Åtgärdar problemet [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter lyssnare läcka varning.

### <a name="1.5.1"/>1.5.1</a>
* Åtgärdar problemet [#92](https://github.com/Azure/azure-documentdb-node/issues/90) – Byt namn på Hash till hash för skiftlägeskänsligt system.

### <a name="1.5.0"/>1.5.0</a>
* Implementera horisontell partitionering stöd genom att lägga till hash & rvall partition matchare.

### <a name="1.4.0"/>1.4.0</a>
* Implementera Upsert. Ny upsertXXX metoder på documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Hoppades över för att göra versionsnummer i linje med andra SDK: er.

### <a name="1.2.2"/>1.2.2</a>
* Frågor och dela lovar wrapper till nya lagringsplatsen.
* Uppdatera till paketfil för npm-registret.

### <a name="1.2.1"/>1.2.1</a>
* Implementerar ID baserat routning.
* Åtgärdar problemet [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -aktuella egenskapen står i konflikt med metoden current().

### <a name="1.2.0"/>1.2.0</a>
* Tillagt stöd för geospatiala index.
* Verifierar id-egenskapen för alla resurser. ID: n för resurser får inte innehålla?, /, #, &#47; &#47;, tecken eller sluta med ett blanksteg.
* Lägger till ny rubrik ”index omvandling status” ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementerar V2 indexeringsprincip.

### <a name="1.0.3"/>1.0.3</a>
* Problemet [#40](https://github.com/Azure/azure-documentdb-node/issues/40) – implementeras eslint och trista konfigurationer i kärnan och lovar SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problemet [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -löften omslutning omfattar inte-rubrik med fel.

### <a name="1.0.1"/>1.0.1</a>
* Implementerad möjlighet att fråga efter konflikter genom att lägga till readConflicts och readConflictAsync queryConflicts.
* Uppdaterade API-dokumentationen.
* Problemet [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync fel.

### <a name="1.0.0"/>1.0.0</a>
* GA-SDK.

## <a name="release--retirement-dates"></a>Fri & Släpp dras tillbaka datum
Microsoft meddelar minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

Varje begäran till Cosmos DB med hjälp av en pensionerad SDK är avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.0.0-3 (RC)](#2.0.0-3) |Den 2 augusti 2018 |--- |
| [1.14.4](#1.14.4) |03 maj 2018 |--- |
| [1.14.3](#1.14.3) |03 maj 2018 |--- |
| [1.14.2](#1.14.2) |Den 21 december 2017 |--- |
| [1.14.1](#1.14.1) |Den 10 november 2017 |--- |
| [1.14.0](#1.14.0) |9 november 2017 |--- |
| [1.13.0](#1.13.0) |11 oktober 2017 |--- |
| [1.12.2](#1.12.2) |10 augusti 2017 |--- |
| [1.12.1](#1.12.1) |10 augusti 2017 |--- |
| [1.12.0](#1.12.0) |10 maj 2017 |--- |
| [1.11.0](#1.11.0) |Den 16 mars 2017 |--- |
| [1.10.2](#1.10.2) |27 januari 2017 |--- |
| [1.10.1](#1.10.1) |Den 22 december 2016 |--- |
| [1.10.0](#1.10.0) |03 oktober 2016 |--- |
| [1.9.0](#1.9.0) |07 juli 2016 |--- |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |--- |
| [1.5.6](#1.5.6) |08 mars 2016 |--- |
| [1.5.5](#1.5.5) |02 februari 2016 |--- |
| [1.5.4](#1.5.4) |01 februari 2016 |--- |
| [1.5.2](#1.5.2) |26 januari 2016 |--- |
| [1.5.2](#1.5.2) |Den 22 januari 2016 |--- |
| [1.5.1](#1.5.1) |Den 4 januari 2016 |--- |
| [1.5.0](#1.5.0) |Den 31 december 2015 |--- |
| [1.4.0](#1.4.0) |06 oktober 2015 |--- |
| [1.3.0](#1.3.0) |06 oktober 2015 |--- |
| [1.2.2](#1.2.2) |Den 10 september 2015 |--- |
| [1.2.1](#1.2.1) |Den 15 augusti 2015 |--- |
| [1.2.0](#1.2.0) |05 augusti 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| [1.0.3](#1.0.3) |04 juni 2015 |--- |
| [1.0.2](#1.0.2) |Den 23 maj 2015 |--- |
| [1.0.1](#1.0.1) |Den 15 maj 2015 |--- |
| [1.0.0](#1.0.0) |08 april 2015 |--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan.


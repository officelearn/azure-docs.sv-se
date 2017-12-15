---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & resurser | Microsoft Docs'
description: "Lär dig mer om SQL Node.js API och SDK inklusive frisläppningsdatum, tillbakadragning datum och ändringar mellan varje version av Azure Cosmos DB Node.js SDK."
services: cosmos-db
documentationcenter: nodejs
author: rnagpal
manager: jhubbard
editor: cgronlun
ms.assetid: 9d5621fa-0e11-4619-a28b-a19d872bcf37
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 11/14/2017
ms.author: rnagpal
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6c9b68ee25ae3d675b71ec5543738493216b1212
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK för SQL-API: viktig information och resurser
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET ändra Feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST-resursprovider](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

<table>

<tr><td>**Ladda ned SDK**</td><td>[NPM](https://www.npmjs.com/package/documentdb)</td></tr>

<tr><td>**API-dokumentationen**</td><td>[Node.js API-referensdokumentation](http://azure.github.io/azure-documentdb-node/DocumentClient.html)</td></tr>

<tr><td>**Installationsinstruktioner för SDK**</td><td>[Instruktioner för installation](http://azure.github.io/azure-documentdb-node/)</td></tr>

<tr><td>**Bidra till SDK**</td><td>[GitHub](https://github.com/Azure/azure-documentdb-node/tree/master/source)</td></tr>

<tr><td>**Exempel**</td><td>[Node.js-kodexempel](sql-api-nodejs-samples.md)</td></tr>

<tr><td>**Självstudier för att komma igång**</td><td>[Kom igång med Node.js SDK](sql-api-nodejs-get-started.md)</td></tr>

<tr><td>**Självstudier för Web app**</td><td>[Skapa en Node.js-webbapp med Azure Cosmos DB](sql-api-nodejs-application.md)</td></tr>

<tr><td>**Aktuella plattform som stöds**</td><td> 
[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/)<br/> 
[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)<br/> 
[Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)<br/> 
[Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/) 
</td></tr>
</table></br>

## <a name="release-notes"></a>Viktig information

### <a name="1.14.0"/>1.14.0</a>
* Lägger till stöd för Sessionskonsekvens.
* Den här SDK-versionen kräver den senaste versionen av Azure Cosmos DB emulatorn måste vara tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator.

### <a name="1.13.0"/>1.13.0</a>
* Dela proofed mellan partition frågor.
* Lägger till stöd för resurslänken med inledande och avslutande snedstreck (och motsvarande tester).

### <a name="1.12.2"/>1.12.2</a>
*   fast npm-dokumentationen.

### <a name="1.12.1"/>1.12.1</a>
* Fast ett programfel i executeStoredProcedure där dokument för hade särskilda Unicode-tecken (LS PS).
* Fast ett programfel i dokument med Unicode-tecken i Partitionsnyckeln-hantering.
* Fast stöd för att skapa samlingar med namnet-mediet. Github problemet #114.
* Fast stöd för behörighet autentiseringstoken. Github problemet #178.

### <a name="1.12.0"/>1.12.0</a>
* Tillagt stöd för en ny [konsekvensnivå](consistency-levels.md) kallas ConsistentPrefix.
* Stöd för UriFactory har lagts till.
* Fast ett programfel för Unicode-stöd. GitHub problemet #171.

### <a name="1.11.0"/>1.11.0</a>
* Tillagt stöd för aggregering frågor (COUNT, MIN, MAX, SUM och AVG).
* Lägga till alternativet för att styra graden av parallellitet för mellan partition frågor.
* Lägga till alternativet för att inaktivera SSL-kontroll när du kör mot Azure Cosmos DB-emulatorn.
* Sänks minsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.
* Fast fortsättning token programfel för enskild partition samling. Github problemet #107.
* Fast executeStoredProcedure programfel i 0-hantering som enda param. Github problemet #155.

### <a name="1.10.2"/>1.10.2</a>
* Fast Användaragent-rubriken för att inkludera SDK-version.
* Rensningen av mindre kod.

### <a name="1.10.1"/>1.10.1</a>
* Inaktivera SSL-kontroll när du använder SDK: N till mål i emulator(hostname=localhost).
* Stöd har lagts till för att aktivera loggning skript under körning av lagrad procedur.

### <a name="1.10.0"/>1.10.0</a>
* Tillagt stöd för mellan partition parallella frågor.
* Stöd för upp/ORDER BY-frågor för partitionerade samlingar har lagts till.

### <a name="1.9.0"/>1.9.0</a>
* Tillagda försök princip stöd för begränsad begäranden. (Begränsad begäranden får en förfrågan hastighet för stor undantag, felkod 429.) Standard Azure Cosmos DB återförsök nio gånger för varje begäran när felkoden 429 påträffas respektera retryAfter tid i rubriken. En fast försök tidsintervall kan nu anges som en del av egenskapen RetryOptions på ConnectionPolicy-objekt om du vill ignorera retryAfter tiden som returnerades av servern mellan försöken. Azure Cosmos-DB väntar nu högst 30 sekunder för varje begäran som har begränsats (oavsett antal försök) och returnerar svaret med felkoden 429. Nu kan åsidosättas i egenskapen RetryOptions på ConnectionPolicy objekt.
* Cosmos DB Returnerar nu x-ms-begränsning--antal försök och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran att ange begränsningen försök antal och kumulativa tid begäran väntade mellan försöken.
* Klassen RetryOptions lades exponera RetryOptions-egenskapen för klassen ConnectionPolicy som kan användas för att åsidosätta en del av du standardalternativen vara försök igen.

### <a name="1.8.0"/>1.8.0</a>
* Lägga till stöd för flera regioner databasen konton.

### <a name="1.7.0"/>1.7.0</a>
* Lägga till stöd för tid Live(TTL) funktion för dokument.

### <a name="1.6.0"/>1.6.0</a>
* Implementerad [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md).

### <a name="1.5.6"/>1.5.6</a>
* Fast RangePartitionResolver.resolveForRead bugg där den inte returnerade länkar på grund av en felaktig concat av resultaten.

### <a name="1.5.5"/>1.5.5</a>
* Fast hashParitionResolver resolveForRead(): när inga partitionsnyckel angavs att undantag, i stället för att returnera en lista över alla registrerade länkar.

### <a name="1.5.4"/>1.5.4</a>
* För att rätta till problemet [#100](https://github.com/Azure/azure-documentdb-node/issues/100) -dedikerad HTTPS Agent: undvika att ändra globala agenten för Azure Cosmos DB ändamål. Använd en dedikerad agent för alla de lib begäranden.

### <a name="1.5.3"/>1.5.3</a>
* För att rätta till problemet [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - korrekt hantera bindestreck i media-ID: n.

### <a name="1.5.2"/>1.5.2</a>
* För att rätta till problemet [#95](https://github.com/Azure/azure-documentdb-node/issues/95) -EventEmitter lyssnare läcka varning.

### <a name="1.5.1"/>1.5.1</a>
* För att rätta till problemet [#92](https://github.com/Azure/azure-documentdb-node/issues/90) -Byt namn på Hash till hash för skiftlägeskänsligt system.

### <a name="1.5.0"/>1.5.0</a>
* Implementera stöd för horisontell partitionering genom att lägga till intervallet & hash-matchare för partitionen.

### <a name="1.4.0"/>1.4.0</a>
* Implementera Upsert. Nya upsertXXX-metoder på documentClient.

### <a name="1.3.0"/>1.3.0</a>
* Hoppas över om du vill flytta versionsnummer i enlighet med andra SDK.

### <a name="1.2.2"/>1.2.2</a>
* Dela frågor lovar wrapper till en ny lagringsplats.
* Uppdatera till paketfilen för npm-registret.

### <a name="1.2.1"/>1.2.1</a>
* Implementerar ID baserat routning.
* För att rätta till problemet [#49](https://github.com/Azure/azure-documentdb-node/issues/49) -aktuella egenskapen står i konflikt med metoden current().

### <a name="1.2.0"/>1.2.0</a>
* Stöd för GeoSpatial indexet har lagts till.
* Verifierar id-egenskapen för alla resurser. ID för resurser kan inte innehålla?, /, #, &#47; &#47; tecken eller sluta med ett blanksteg.
* Lägger till nya rubriken ”index omvandling pågår” ResourceResponse.

### <a name="1.1.0"/>1.1.0</a>
* Implementerar V2 indexprincip.

### <a name="1.0.3"/>1.0.3</a>
* Problemet [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - implementerat eslint och grunt konfigurationer i kärnor och lovar SDK.

### <a name="1.0.2"/>1.0.2</a>
* Problemet [#45](https://github.com/Azure/azure-documentdb-node/issues/45) -löftena wrapper inkluderar inte-rubrik med fel.

### <a name="1.0.1"/>1.0.1</a>
* Implementerad möjlighet till frågor för konflikter genom att lägga till readConflicts och readConflictAsync queryConflicts.
* Uppdaterade API-dokumentationen.
* Problemet [#41](https://github.com/Azure/azure-documentdb-node/issues/41) -client.createDocumentAsync fel.

### <a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Versionen & pensionering datum
Microsoft meddelar minst **12 månader** innan du tar bort en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar bara lägga till den aktuella SDK, som vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så snart som möjligt.

Alla förfrågningar till Cosmos-databas med hjälp av en pensionerad SDK är avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Datumet för tillbakadragandet |
| --- | --- | --- |
| [1.14.0](#1.14.0) |9 november 2017 |--- |
| [1.13.0](#1.13.0) |11 oktober 2017 |--- |
| [1.12.2](#1.12.2) |10 augusti 2017 |--- |
| [1.12.1](#1.12.1) |10 augusti 2017 |--- |
| [1.12.0](#1.12.0) |10 maj 2017 |--- |
| [1.11.0](#1.11.0) |16 mars 2017 |--- |
| [1.10.2](#1.10.2) |27 januari 2017 |--- |
| [1.10.1](#1.10.1) |Den 22 december 2016 |--- |
| [1.10.0](#1.10.0) |03 oktober 2016 |--- |
| [1.9.0](#1.9.0) |07 juli 2016 |--- |
| [1.8.0](#1.8.0) |14 juni 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.0](#1.6.0) |Den 29 mars 2016 |--- |
| [1.5.6](#1.5.6) |08 mars 2016 |--- |
| [1.5.5](#1.5.5) |02 februari 2016 |--- |
| [1.5.4](#1.5.4) |01 februari 2016 |--- |
| [1.5.2](#1.5.2) |26 januari 2016 |--- |
| [1.5.2](#1.5.2) |Den 22 januari 2016 |--- |
| [1.5.1](#1.5.1) |4 januari 2016 |--- |
| [1.5.0](#1.5.0) |Den 31 december 2015 |--- |
| [1.4.0](#1.4.0) |06 oktober 2015 |--- |
| [1.3.0](#1.3.0) |06 oktober 2015 |--- |
| [1.2.2](#1.2.2) |10 september 2015 |--- |
| [1.2.1](#1.2.1) |15 augusti 2015 |--- |
| [1.2.0](#1.2.0) |05 augusti 2015 |--- |
| [1.1.0](#1.1.0) |09 juli 2015 |--- |
| [1.0.3](#1.0.3) |04 juni 2015 |--- |
| [1.0.2](#1.0.2) |23 maj 2015 |--- |
| [1.0.1](#1.0.1) |Den 15 maj 2015 |--- |
| [1.0.0](#1.0.0) |08 april 2015 |--- |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Läs mer om Cosmos-DB i [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) sida.


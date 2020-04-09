---
title: 'Azure Cosmos DB: SQL Node.js API, SDK & resurser'
description: Lär dig allt om SQL Node.js API och SDK, inklusive utgivningsdatum, återsändningsdatum och ändringar som gjorts mellan varje version av Azure Cosmos DB Node.js SDK.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: reference
ms.date: 09/24/2018
ms.author: dech
ms.openlocfilehash: 03f79535b3a62fbb4d0309ae86a142bd842cc308
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982896"
---
# <a name="azure-cosmos-db-nodejs-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Node.js SDK för SQL API: Versionsanteckningar och resurser
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

|Resurs  |Länk  |
|---------|---------|
|Ladda ner SDK  |   [NPM](https://www.npmjs.com/package/@azure/cosmos) 
|API-dokumentation  |  [Referensdokumentation för JavaScript SDK](https://docs.microsoft.com/javascript/api/%40azure/cosmos/?view=azure-node-latest)
|Installationsinstruktioner för SDK  |  [Installationsinstruktioner](https://github.com/Azure/azure-cosmos-js#installation)
|Bidra till SDK | [GitHub](https://github.com/Azure/azure-cosmos-js/tree/master)
| Exempel | [Nod.js kodexempel](sql-api-nodejs-samples.md)
| Komma igång självstudiekurs | [Komma igång med JavaScript SDK](sql-api-nodejs-get-started.md)
| Självstudiekurs för webbappar | [Skapa ett node.js-webbprogram med Azure Cosmos DB](sql-api-nodejs-application.md)
| Nuvarande plattform som stöds | [Node.js v12.x](https://nodejs.org/en/blog/release/v12.7.0/) - SDK Version 3.x.x<br/>[Node.js v10.x](https://nodejs.org/en/blog/release/v10.6.0/) - SDK Version 3.x.x<br/>[Node.js v8.x](https://nodejs.org/en/blog/release/v8.16.0/) - SDK Version 3.x.x<br/>[Node.js v6.x](https://nodejs.org/en/blog/release/v6.10.3/) - SDK Version 2.x.x<br/>[Node.js v4.2.0](https://nodejs.org/en/blog/release/v4.2.0/)- SDK Version 1.x.x<br/> [Node.js v0.12](https://nodejs.org/en/blog/release/v0.12.0/)- SDK Version 1.x.x<br/> [Node.js v0.10](https://nodejs.org/en/blog/release/v0.10.0/)- SDK Version 1.x.x

## <a name="release-notes"></a>Viktig information

### <a name=""></a><a name="3.1.0"/>3.1.0</a>
* Ange standardsvarskontinuationtokenLimitInKB till 1kb. Som standard begränsar vi detta till 1kb för att undvika långa rubriker (Node.js har en global rubrikstorleksgräns). En användare kan ställa in det här fältet så att det tillåter längre rubriker, vilket kan hjälpa serverdelsoptimerade frågekörning.
* Ta bort disableSSLVerification. Det här alternativet har nya alternativ som beskrivs i [#388](https://github.com/Azure/azure-cosmos-js/pull/388)

### <a name=""></a><a name="3.0.4"/>3.0.4</a>
* Tillåt initialHeaders att uttryckligen ange partitionsnyckelhuvud
* Använd package.json#files för att förhindra att främmande filer publiceras
* Åtgärda sorteringsfel för routningsmappning på äldre version av nod+v8
* Åtgärdar bugg när användaren tillhandahåller partiella alternativ för återförsök

### <a name=""></a><a name="3.0.3"/>3.0.3</a>
* Förhindra webpack från att lösa moduler som anropas med kräv

### <a name=""></a><a name="3.0.2"/>3.0.2</a>
* Åtgärdar en lång utestående bugg där ru:er alltid rapporterades som 0 för aggregerade frågor

### <a name=""></a><a name="3.0.0"/>3.0.0</a>

🎉 v3 release! 🎉 Många nya funktioner, buggfixar och några bryta förändringar. Primära mål för den här versionen:

* Implementera viktiga nya funktioner
  * DISTINKTa frågor
  * LIMIT/OFFSET-frågor
  * Begäran om att användaren ska avbrytas
* Uppdatera till den senaste COSMOS REST API-versionen där alla behållare har obegränsad skala
* Gör det enklare att använda Cosmos från webbläsaren
* Anpassa dig bättre till de nya Azure JS SDK-riktlinjerna

#### <a name="migration-guide-for-breaking-changes"></a>Migreringsguide för att bryta ändringar
##### <a name="improved-client-constructor-options"></a>Förbättrade alternativ för klientkonstruktor

Konstruktoralternativen har förenklats:

* masterKey döptes om till nyckeln och flyttades till den översta nivån
* Egenskaper som tidigare under options.auth har flyttats till den översta nivån

``` js
// v2
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    auth: {
        masterKey: "your-primary-key"
    }
})

// v3
const client = new CosmosClient({
    endpoint: "https://your-database.cosmos.azure.com",
    key: "your-primary-key"
})
```

##### <a name="simplified-queryiterator-api"></a>Förenklat QueryIterator-API
I v2 fanns det många olika sätt att iterera eller hämta resultat från en fråga. Vi har försökt att förenkla v3 API och ta bort liknande eller duplicera API: er:

* Ta bort iterator.next() och iterator.current(). Använd fetchNext() för att hämta resultatsidor.
* Ta bort iterator.forEach(). Använd asynkrona iteratorer i stället.
* iterator.executeNext() omdöpt till iterator.fetchNext()
* iterator.toArray() bytt namn till iterator.fetchAll()
* Sidor är nu riktiga svarsobjekt i stället för vanliga JS-objekt
* const-behållare = client.database(dbId).container(containerId)

``` js
// v2
container.items.query('SELECT * from c').toArray()
container.items.query('SELECT * from c').executeNext()
container.items.query('SELECT * from c').forEach(({ body: item }) => { console.log(item.id) })

// v3
container.items.query('SELECT * from c').fetchAll()
container.items.query('SELECT * from c').fetchNext()
for await(const { result: item } in client.databases.readAll().getAsyncIterator()) {
    console.log(item.id)
}
```

##### <a name="fixed-containers-are-now-partitioned"></a>Fasta behållare är nu partitionerade
Cosmos-tjänsten stöder nu partitionsnycklar på alla behållare, inklusive de som tidigare har skapats som fasta behållare. V3 SDK uppdateras till den senaste API-versionen som implementerar den här ändringen, men den går inte sönder. Om du inte tillhandahåller en partitionsnyckel för åtgärder, kommer vi som standard att en systemnyckel som fungerar med alla dina befintliga behållare och dokument.

##### <a name="upsert-removed-for-stored-procedures"></a>Upsert borttaget för lagrade procedurer
Tidigare upsert tilläts för icke-partitionerade samlingar, men med API-versionsuppdateringen är alla samlingar partitionerade så vi tog bort den helt.

##### <a name="item-reads-will-not-throw-on-404"></a>Objekt läser kommer inte att kasta på 404
const-behållare = client.database(dbId).container(containerId)

``` js
// v2
try {
    container.items.read(id, undefined)
} catch (e) {
    if (e.code === 404) { console.log('item not found') }
}

// v3
const { result: item }  = container.items.read(id, undefined)
if (item === undefined) { console.log('item not found') }
```

##### <a name="default-multi-region-write"></a>Standardskrivning i flera regioner
SDK skriver nu till flera regioner som standard om Cosmos-konfigurationen stöder den. Detta var tidigare opt-in beteende.

##### <a name="proper-error-objects"></a>Rätt felobjekt
Misslyckade begäranden genererar nu korrekt fel eller underklasser av fel. Tidigare kastade de vanliga JS-föremål.

#### <a name="new-features"></a>Nya funktioner
##### <a name="user-cancelable-requests"></a>Begäran om att avbryta användaren
Flytten att hämta internt tillåter oss att använda webbläsaren AbortController API för att stödja användarupp cancelable åtgärder. När det gäller åtgärder där flera begäranden kan vara på gång (som korspartitionsfrågor) avbryts alla begäranden för åtgärden. Moderna webbläsare användare kommer redan att ha AbortController. Node.js-användare måste använda ett polyfyllbibliotek

``` js
 const controller = new AbortController()
 const {result: item} = await items.query('SELECT * from c', { abortSignal: controller.signal});
 controller.abort()
```

##### <a name="set-throughput-as-part-of-dbcontainer-create-operation"></a>Ange dataflöde som en del av åtgärden för att skapa db/container
``` js
const { database }  = client.databases.create({ id: 'my-database', throughput: 10000 })
database.containers.create({ id: 'my-container', throughput: 10000 })
```

##### <a name="azurecosmos-sign"></a>@azure/cosmos-sign
Rubrik token generation delades upp @azure/cosmos-signi ett nytt bibliotek, . Alla som anropar Cosmos REST API direkt kan använda detta @azure/cosmosför att signera rubriker med samma kod som vi anropar inuti .

##### <a name="uuid-for-generated-ids"></a>UUID för genererade ID:er
v2 hade anpassad kod för att generera artikel-ID. Vi har bytt till det välkända och underhållna samhällsbiblioteket uuid.

##### <a name="connection-strings"></a>Anslutningssträngar
Det är nu möjligt att skicka en anslutningssträng som kopierats från Azure-portalen:

``` js
const client = new CosmosClient("AccountEndpoint=https://test-account.documents.azure.com:443/;AccountKey=c213asdasdefgdfgrtweaYPpgoeCsHbpRTHhxuMsTaw==;")
Add DISTINCT and LIMIT/OFFSET queries (#306)
 const { results } = await items.query('SELECT DISTINCT VALUE r.name FROM ROOT').fetchAll()
 const { results } = await items.query('SELECT * FROM root r OFFSET 1 LIMIT 2').fetchAll()
```

#### <a name="improved-browser-experience"></a>Förbättrad webbläsarupplevelse
Även om det var möjligt att använda v2 SDK i webbläsaren var det inte en idealisk upplevelse. Du behövde polyfill flera node.js inbyggda bibliotek och använda en buntare som Webpack eller Parcel. V3 SDK gör out of the box-upplevelsen mycket bättre för webbläsaranvändare.

* Ersätt interna begäranden med hämtning (#245)
* Ta bort användning av buffert (#330)
* Ta bort inbyggd nodanvändning till förmån för universella paket/API:er (#328)
* Växla till nod-abort-controller (#294)

#### <a name="bug-fixes"></a>Felkorrigeringar
* Fix erbjudande läsa och få tillbaka offer tester (#224)
* Åtgärda EnableEndpointDiscovery (#207)
* Åtgärda saknade ru:er i sidnumrerade resultat (#360)
* Expandera PARAMETERTYP FÖR SQL-frågor (#346)
* Lägg till tl i ItemDefinition (#341)
* Åtgärda cp-frågemått (#311)
* Lägg till activityId i FeedResponse (#293)
* Växla _ts typ från sträng till nummer (#252)(#295)
* Fix begäran avgift aggregering (#289)
* Tillåt tomma strängpartitionsnycklar (#277)
* Lägga till sträng i konfliktfrågetyp (#237)
* Lägg till uniqueKeyPolicy i behållaren (#234)

#### <a name="engineering-systems"></a>Tekniska system
Inte alltid de mest synliga förändringarna, men de hjälper vårt team leverera bättre kod, snabbare.

* Använd sammanslagning för produktionsversioner (#104)
* Uppdatera till Typescript 3.5 (#327)
* Konvertera till TS-projektreferenser. Extrahera testmapp (#270)
* Aktivera noUnusedLocals och noUnusedParameters (#275)
* Azure Pipelines YAML för CI-versioner (#298)

### <a name=""></a><a name="2.1.5"/>2.1.5</a>
* Inga kodändringar. Åtgärdar ett problem där vissa extra filer ingick i 2.1.4-paketet.

### <a name=""></a><a name="2.1.4"/>2.1.4</a>
* Åtgärda regional redundans i återförsöksprincipen
* Fix ChangeFeed har MerResults egenskap
* Uppdateringar av utvecklingsberoende
* Lägg till PolicheckExclusions.txt

### <a name=""></a><a name="2.1.3"/>2.1.3</a>
* Växla _ts typ från sträng till nummer
* Åtgärda standardindexeringstester
* Backport uniqueKeyPolicy till v2
* Korrigeringar för demo- och demofelsökning

### <a name=""></a><a name="2.1.2"/>2.1.2</a>
* Backport erbjuder korrigeringar från v3-gren
* Åtgärda fel i signaturen körNext()
* Stavfel fixar

### <a name=""></a><a name="2.1.1"/>2.1.1</a>
* Bygg omstruktureringar. Gör det möjligt att dra SDK-versionen vid byggtid.

### <a name=""></a><a name="2.1.0"/>2.1.0</a>
#### <a name="new-features"></a>Nya funktioner
* Lagt till Support för ChangeFeed (#196)
* Lade till MultiPolygon-datatyp för indexering (#191)
* Lägg till egenskapen "key" i konstruktorn som alias för masterKey (#202)

#### <a name="fixes"></a>Korrigeringar
* Åtgärda fel där nästa() returnerar felaktigt värde på iterator

#### <a name="engineering-improvements"></a>Tekniska förbättringar
* Lägga till integrationstest för typskriptförbrukning (#199)
* Aktivera installation direkt från GitHub (#194)

### <a name=""></a><a name="2.0.5"/>2.0.5</a>
* Lägger till gränssnitt för nod agenttyp. Typescript-användare behöver inte @types/node längre installera som ett beroende
* Föredragna platser är nu ordentligt hedrade
* Förbättringar av bidragande dokumentation för utvecklare
* Olika stavfel fixar

### <a name=""></a><a name="2.0.4"/>2.0.4</a>
* Åtgärdar typdefinitionsproblem som infördes i 2.0.3

### <a name=""></a><a name="2.0.3"/>2.0.3</a>
* Ta `big-integer` bort beroende
* Växla till referensdirektiv för AsyncIterable-typ. Typescript-användare behöver inte längre anpassa sin "lib"-inställning.
* Korrigeringar av stavfel

### <a name=""></a><a name="2.0.2"/>2.0.2</a>
* Åtgärda länkar för läsning

### <a name=""></a><a name="2.0.1"/>2.0.1</a>
* Åtgärda implementering av gränssnitt för återförsök

### <a name=""></a><a name="2.0.0"/>2.0.0</a>
* GA av version 2.0.0 av JavaScript SDK
* Lagt till stöd för flera regioner skriver.

### <a name=""></a><a name="2.0.0-3"/>2.0.0-3</a>
* RC1 av version 2.0.0 av JavaScript SDK för offentlig förhandsversion.
* Ny objektmodell, med cosmosClient på den översta nivån och metoder som delas över relevanta klasser för databas, behållare och objekt. 
* Stöd för [löften](https://developer.mozilla.org/docs/Web/JavaScript/Guide/Using_promises). 
* SDK konverteras till TypeScript.

### <a name=""></a><a name="1.14.4"/>1.14.4</a>
* npm-dokumentationen har åtgärdats.

### <a name=""></a><a name="1.14.3"/>1.14.3</a>
* Lade till stöd för standardförsök i anslutningsproblem.
* Lade till stöd för att läsa insamlingsändringsflöde.
* Fast session konsekvens bugg som periodvis orsakade "lässession inte tillgänglig".
* Lagt till stöd för frågemått.
* Ändrade http-agentens maximala antal anslutningar.

### <a name=""></a><a name="1.14.2"/>1.14.2</a>
* Uppdaterad dokumentation för att referera till Azure Cosmos DB i stället för Azure DocumentDB.
* Lade till stöd för proxyUrl-inställningen i ConnectionPolicy.

### <a name=""></a><a name="1.14.1"/>1.14.1</a>
* Mindre korrigering för skiftlägeskänsliga filsystem.

### <a name=""></a><a name="1.14.0"/>1.14.0</a>
* Lägger till stöd för Sessionskonsekvens.
* Den här SDK-versionen kräver den senaste versionen av [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator).

### <a name=""></a><a name="1.13.0"/>1.13.0</a>
* Delade korrektursträknade korspartitionsfrågor.
* Lägger till stöd för resurslänk med inledande och avslutande snedstreck (och motsvarande tester).

### <a name=""></a><a name="1.12.2"/>1.12.2</a>
*    npm-dokumentationen har åtgärdats.

### <a name=""></a><a name="1.12.1"/>1.12.1</a>
* Fixade ett fel i executeStoredProcedure där inblandade dokument hade speciella Unicode-tecken (LS, PS).
* Fixade ett fel i hanteringen av dokument med Unicode-tecken i partitionsnyckeln.
* Fast stöd för att skapa samlingar med namnmediet. GitHub-problemet #114.
* Fast stöd för behörighetsauktoriseringstookett. GitHub-problemet #178.

### <a name=""></a><a name="1.12.0"/>1.12.0</a>
* Lade till stöd för en ny [konsekvensnivå](consistency-levels.md) som kallas ConsistentPrefix.
* Lagt till stöd för UriFactory.
* Fixade ett Unicode-supportfel. GitHub-problemet #171.

### <a name=""></a><a name="1.11.0"/>1.11.0</a>
* Lade till stöd för aggregeringsfrågor (ANTAL, MIN, MAX, SUMMA och AVG).
* Lade till alternativet för att kontrollera graden av parallellism för korspartitionsfrågor.
* Lade till alternativet för att inaktivera TLS-verifiering när du kör mot Azure Cosmos DB Emulator.
* Sänkt minsta genomströmning på partitionerade samlingar från 10 100 RU/s till 2500 RU/s.
* Fixade att fel vid fortsättningstoken för enpartitionssamling. GitHub-problemet #107.
* Fixade felet executeStoredProcedure i hanteringen 0 som en param. GitHub-problemet #155.

### <a name=""></a><a name="1.10.2"/>1.10.2</a>
* Fast användaragenthuvud att inkludera SDK-versionen.
* Mindre kodrensning.

### <a name=""></a><a name="1.10.1"/>1.10.1</a>
* Inaktivera TLS-verifiering när du använder SDK för att rikta in sig på emulatorn (hostname=localhost).
* Lade till stöd för att aktivera skriptloggning under körning av lagrade procedurer.

### <a name=""></a><a name="1.10.0"/>1.10.0</a>
* Lagt till stöd för parallella frågor mellan partitioner.
* Lade till stöd för TOP/ORDER BY-frågor för partitionerade samlingar.

### <a name=""></a><a name="1.9.0"/>1.9.0</a>
* Lade till principstöd för återförsök för begränsade begäranden. (Begränsade begäranden får en för stor procentsats för begäran, felkod 429.) Som standard försöker Azure Cosmos DB nio gånger för varje begäran när felkod 429 påträffas, vilket bekräftar återförsökEfter tid i svarshuvudet. En fast återförsöksintervalltid kan nu ställas in som en del av egenskapen RetryOptions på ConnectionPolicy-objektet om du vill ignorera återförsökEfter tid som returneras av servern mellan återförsöken. Azure Cosmos DB väntar nu i högst 30 sekunder för varje begäran som begränsas (oavsett antal återförsök) och returnerar svaret med felkod 429. Den här gången kan också åsidosättas i egenskapen RetryOptions på ConnectionPolicy-objektet.
* Cosmos DB returnerar nu x-ms-throttle-retry-count och x-ms-throttle-retry-wait-time-ms som svarshuvuden i varje begäran om att beteckna antalet återförsök och den sammanlagda tiden som begäran väntade mellan försöken.
* Klassen RetryOptions lades till och egenskapen RetryOptions visas i klassen ConnectionPolicy som kan användas för att åsidosätta några av standardalternativen för återförsök.

### <a name=""></a><a name="1.8.0"/>1.8.0</a>
* Lade till stöd för databaskonton med flera regioner.

### <a name=""></a><a name="1.7.0"/>1.7.0</a>
* Lade till stöd för funktionen Time To Live(TTL) för dokument.

### <a name=""></a><a name="1.6.0"/>1.6.0</a>
* Implementerade [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md).

### <a name=""></a><a name="1.5.6"/>1.5.6</a>
* Fixed RangePartitionResolver.resolveForRead bug where it was not returning links due to a bad concat of results.

### <a name=""></a><a name="1.5.5"/>1.5.5</a>
* Fast hashPartitionResolver resolveForRead(): När ingen partitionsnyckel som angavs kastade undantag, i stället för att returnera en lista över alla registrerade länkar.

### <a name=""></a><a name="1.5.4"/>1.5.4</a>
* Åtgärdar problemet [#100](https://github.com/Azure/azure-documentdb-node/issues/100) - Dedikerad HTTPS-agent: Undvik att ändra den globala agenten för Azure Cosmos DB-ändamål. Använd en dedikerad agent för alla lib begäranden.

### <a name=""></a><a name="1.5.3"/>1.5.3</a>
* Åtgärdar problemet [#81](https://github.com/Azure/azure-documentdb-node/issues/81) - Korrekt hantera streck i medie-ID.

### <a name=""></a><a name="1.5.2"/>1.5.2</a>
* Åtgärdar problemet [#95](https://github.com/Azure/azure-documentdb-node/issues/95) - EventEmitter lyssnare läcka varning.

### <a name=""></a><a name="1.5.1"/>1.5.1</a>
* Åtgärdar problemet [#92](https://github.com/Azure/azure-documentdb-node/issues/90) - byt namn på mappen Hash till hash för skiftlägeskänsliga system.

### <a name=""></a><a name="1.5.0"/>1.5.0</a>
* Implementera sharding-stöd genom att lägga till hash-& intervallpartitionslösare.

### <a name=""></a><a name="1.4.0"/>1.4.0</a>
* Implementera Upsert. Nya upsertXXX metoder på documentClient.

### <a name=""></a><a name="1.3.0"/>1.3.0</a>
* Hoppas att få versionsnummer i linje med andra SDK: er.

### <a name=""></a><a name="1.2.2"/>1.2.2</a>
* Split Q lovar omslag till ny databas.
* Uppdatera till paketfilen för npm-registret.

### <a name=""></a><a name="1.2.1"/>1.2.1</a>
* Implementerar ID-baserad routning.
* Åtgärdar problem [#49](https://github.com/Azure/azure-documentdb-node/issues/49) - aktuella egenskapskonflikter med metoden current().

### <a name=""></a><a name="1.2.0"/>1.2.0</a>
* Lagt till stöd för GeoSpatial index.
* Validerar id-egenskap för alla resurser. ID:er för resurser kan inte innehålla?, /, #, &#47;&#47;, tecken eller med ett blanksteg.
* Lägger till nya rubriken "indexomvandlingsförlopp" i ResourceResponse.

### <a name=""></a><a name="1.1.0"/>1.1.0</a>
* Implementerar V2-indexeringsprincip.

### <a name=""></a><a name="1.0.3"/>1.0.3</a>
* Issue [#40](https://github.com/Azure/azure-documentdb-node/issues/40) - Implementerade eslint- och grymtande konfigurationer i kärnan och lovar SDK.

### <a name=""></a><a name="1.0.2"/>1.0.2</a>
* Problem [#45](https://github.com/Azure/azure-documentdb-node/issues/45) - Löften omslag innehåller inte huvudet med fel.

### <a name=""></a><a name="1.0.1"/>1.0.1</a>
* Implementerad möjligheten att fråga efter konflikter genom att lägga till readConflicts, readConflictAsync och queryConflicts.
* Uppdaterad API-dokumentation.
* Problem [#41](https://github.com/Azure/azure-documentdb-node/issues/41) - client.createDocumentAsync fel.

### <a name=""></a><a name="1.0.0"/>1.0.0</a>
* GA SDK.

## <a name="release--retirement-dates"></a>Släpp & pensioneringsdatum
Microsoft meddelar minst **12 månader** innan en SDK går i pension för att underlätta övergången till en nyare/stödd version.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK, som sådan rekommenderas att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt.

Alla förfrågningar till Cosmos DB med hjälp av en pensionerad SDK kommer att avvisas av tjänsten.

> [!WARNING]
> Alla versioner **1.x** av Nod-klienten SDK för SQL API kommer att dras tillbaka den **30 augusti 2020**. Detta påverkar bara nod-SDK på klientsidan och påverkar inte skript på serversidan (lagrade procedurer, utlösare och UDF:er).
> 
>
<br/>

| Version | Utgivningsdatum | Pensionering Datum |
| --- | --- | --- |
| [3.1.0](#3.1.0) |den 26 juli 2019 |--- |
| [3.0.4](#3.0.4) |den 22 juli 2019 |--- |
| [3.0.3](#3.0.3) |den 17 juli 2019 |--- |
| [3.0.2](#3.0.2) |den 9 juli 2019 |--- |
| [3.0.0](#3.0.0) |den 28 juni 2019 |--- |
| [2.1.5](#2.1.5) |den 20 mars 2019 |--- |
| [2.1.4](#2.1.4) |den 15 mars 2019 |--- |
| [2.1.3](#2.1.3) |den 8 mars 2019 |--- |
| [2.1.2](#2.1.2) |den 28 januari 2019 |--- |
| [2.1.1](#2.1.1) |den 5 december 2018 |--- |
| [2.1.0](#2.1.0) |4 december 2018 |--- |
| [2.0.5](#2.0.5) |den 7 november 2018 |--- |
| [2.0.4](#2.0.4) |den 30 oktober 2018 |--- |
| [2.0.3](#2.0.3) |den 30 oktober 2018 |--- |
| [2.0.2](#2.0.2) |den 10 oktober 2018 |--- |
| [2.0.1](#2.0.1) |25 september 2018 |--- |
| [2.0.0](#2.0.0) |September 24, 2018 |--- |
| [2.0.0-3 (RC)](#2.0.0-3) |den 2 augusti 2018 |--- |
| [1.14.4](#1.14.4) |den 3 maj 2018 |den 30 augusti 2020 |
| [1.14.3](#1.14.3) |den 3 maj 2018 |den 30 augusti 2020 |
| [1.14.2](#1.14.2) |den 21 december 2017 |den 30 augusti 2020 |
| [1.14.1](#1.14.1) |den 10 november 2017 |den 30 augusti 2020 |
| [1.14.0](#1.14.0) |den 9 november 2017 |den 30 augusti 2020 |
| [1.13.0](#1.13.0) |den 11 oktober 2017 |den 30 augusti 2020 |
| [1.12.2](#1.12.2) |den 10 augusti 2017 |den 30 augusti 2020 |
| [1.12.1](#1.12.1) |den 10 augusti 2017 |den 30 augusti 2020 |
| [1.12.0](#1.12.0) |10 maj 2017 |den 30 augusti 2020 |
| [1.11.0](#1.11.0) |den 16 mars 2017 |den 30 augusti 2020 |
| [1.10.2](#1.10.2) |den 27 januari 2017 |den 30 augusti 2020 |
| [1.10.1](#1.10.1) |den 22 december 2016 |den 30 augusti 2020 |
| [1.10.0](#1.10.0) |den 3 oktober 2016 |den 30 augusti 2020 |
| [1.9.0](#1.9.0) |den 7 juli 2016 |den 30 augusti 2020 |
| [1.8.0](#1.8.0) |den 14 juni 2016 |den 30 augusti 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |den 30 augusti 2020 |
| [1.6.0](#1.6.0) |den 29 mars 2016 |den 30 augusti 2020 |
| [1.5.6](#1.5.6) |den 8 mars 2016 |den 30 augusti 2020 |
| [1.5.5](#1.5.5) |den 2 februari 2016 |den 30 augusti 2020 |
| [1.5.4](#1.5.4) |den 1 februari 2016 |den 30 augusti 2020 |
| [1.5.2](#1.5.2) |den 26 januari 2016 |den 30 augusti 2020 |
| [1.5.2](#1.5.2) |den 22 januari 2016 |den 30 augusti 2020 |
| [1.5.1](#1.5.1) |den 4 januari 2016 |den 30 augusti 2020 |
| [1.5.0](#1.5.0) |den 31 december 2015 |den 30 augusti 2020 |
| [1.4.0](#1.4.0) |den 6 oktober 2015 |den 30 augusti 2020 |
| [1.3.0](#1.3.0) |den 6 oktober 2015 |den 30 augusti 2020 |
| [1.2.2](#1.2.2) |den 10 september 2015 |den 30 augusti 2020 |
| [1.2.1](#1.2.1) |den 15 augusti 2015 |den 30 augusti 2020 |
| [1.2.0](#1.2.0) |den 5 augusti 2015 |den 30 augusti 2020 |
| [1.1.0](#1.1.0) |den 9 juli 2015 |den 30 augusti 2020 |
| [1.0.3](#1.0.3) |den 4 juni 2015 |den 30 augusti 2020 |
| [1.0.2](#1.0.2) |den 23 maj 2015 |den 30 augusti 2020 |
| [1.0.1](#1.0.1) |den 15 maj 2015 |den 30 augusti 2020 |
| [1.0.0](#1.0.0) |den 8 april 2015 |den 30 augusti 2020 |

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se även
Mer information om Cosmos DB finns på tjänstsidan [för Microsoft Azure Cosmos DB.](https://azure.microsoft.com/services/cosmos-db/)


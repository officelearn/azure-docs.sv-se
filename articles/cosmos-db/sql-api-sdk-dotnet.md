---
title: 'Azure Cosmos DB: SQL .NET API, SDK och resurser'
description: Lär dig allt om SQL .NET API och SDK, inklusive frisläppningsdatum, dras tillbaka datum och ändringar som gjorts mellan varje version av Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: e9378fd1e7fd0d8c6b3f913ec3d8f629a2c3be37
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770694"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK för SQL-API: Ladda ned och viktig information
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

| |  |
|---|---|
|**Hämta SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API-dokumentation**|[.NET API-referensdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exempel**|[.NET-kodexempel](sql-api-dotnet-samples.md)|
|**Kom igång**|[Kom igång med Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Självstudier om webbappen**|[Utveckling av webbappar med Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuella framework som stöds**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Viktig information

### <a name="a-name3001-preview3001-preview"></a><a name="3.0.0.1-preview"/>3.0.0.1-Preview
* Förhandsversion 1 av [Version 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/) av .NET SDK för en förhandsversion.
* Target .NET Standard som har stöd för .NET framework 4.6.1+ och .NET Core 2.0 +
* Ny objektmodell med översta CosmosClient och metoder dela upp på relevanta CosmosDatabases, CosmosContainers och CosmosItems klasser. 
* Stöd för strömmar. 
* Uppdatera CosmosResponseMessage från servern för att returnera statuskod och utlöser undantag endast när inget svar returneras. 

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Förbättringar i logik för omprövning under en redundansväxling för StoredProcedure utföra anrop.

* Gjort DocumentClientEventSource singleton. 

* Åtgärda GatewayAddressCache tidsgräns som inte respekterar ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Lagt till TransportException, en typ av interna undantag av SDK direkt/TCP-transport diagnostik. När det finns i undantag meddelanden, skriver den här typen ytterligare information för felsökning av anslutningsproblem för klienten.

* Har lagts till nya konstruktorn överlagring som tar en HttpMessageHandler, en HTTP-hanteraren stack ska användas för att skicka HttpClient-förfrågningar (t.ex. HttpClientHandler).

* Åtgärda fel där huvud med null-värden har inte hanteras korrekt.

* Förbättrad samling cache-verifiering.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Uppdaterade System.Net.Security till 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Diagnostikspårning förbättringar

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Lägga till mer flexibilitet vid tillfälliga fel i flera regioner begäran.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Att skriva stöd har lagts till flera regioner.
* Mellan partition frågeprestandaförbättringar med ÖVERKANT och MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Har lagts till behöver support för avbrott.
* Har lagts till SetCurrentLocation till ConnectionPolicy som automatiskt fyller de önskade platser baserat på regionen.
* En bugg har åtgärdats i mellan Partition frågor med Min/Max- och ett filter som matchar inga dokument på en enskild partition.
* DocumentClient metoderna har nu paritet med IDocumentClient.
* Uppdaterade direkt TCP transport stackutrymme för att minska antalet anslutningar som upprättats.
* Tillagt stöd för direkt TCP för läge för icke-Windows-klienter.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Har lagts till behöver support för avbrott.
* Har lagts till SetCurrentLocation till ConnectionPolicy som automatiskt fyller de önskade platser baserat på regionen.
* En bugg har åtgärdats i mellan Partition frågor med Min/Max- och ett filter som matchar inga dokument på en enskild partition.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* DocumentClient metoderna har nu paritet med IDocumentClient.
* Uppdaterade direkt TCP transport stackutrymme för att minska antalet anslutningar som upprättats.
* Tillagt stöd för direkt TCP för läge för icke-Windows-klienter.

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Egenskapen har lagts ConsistencyLevel till FeedOptions.
* Har lagts till JsonSerializerSettings RequestOptions och FeedOptions.
* Har lagts till EnableReadRequestsFallback till ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Fast KeyNotFoundException för mellan partition sortering av frågor i specialfall.
* En bugg har åtgärdats där JsonProperty attribut i select-satsen för LINQ-frågor inte som lösts in.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* En bugg har åtgärdats som uppnås under vissa konkurrenstillstånd som resulterar i återkommande ”Microsoft.Azure.Documents.NotFoundException: Läs sessionen är inte tillgänglig för inkommande sessionstoken ”fel när du använder Session konsekvensnivå.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Fast regression där FeedOptions.MaxItemCount = -1 utlöste ett System.ArithmeticException: sidstorlek är negativt.
* Lägga till en ny toString ()-funktion till QueryMetrics.
* Exponerade partitionsstatistik vid läsning av samlingar.
* Tillagda PartitionKey-egenskapen till ChangeFeedOptions.
* Mindre felkorrigeringar.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Lägger till möjligheten att ange unika index för dokument med hjälp av UniqueKeyPolicy egenskap på dokumentsamling.
* Ett fel där de anpassade inställningarna för JsonSerializer inte har att användas för vissa frågor och lagrade procedurkörning har åtgärdats.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Anpassning av ändra från Azure DocumentDB till Azure Cosmos DB i API-referensen dokumentation, metadatainformation i sammansättningar och NuGet-paketet. 
* Visa diagnostikinformation och svarstid från svaret antalet begäranden som skickas med direktanslutning läge. Egenskapsnamnen är RequestDiagnosticsString och RequestLatency ResourceResponse klassen.
* Den SDK-versionen kräver att den senaste versionen av Azure Cosmos DB-emulatorn tillgänglig för nedladdning från https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Interna ändringar för Microsoft vänner sammansättningar.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Lägga till flera tillförlitlighet korrigeringar och förbättringar.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Stöd har lagts till för PartitionKeyRangeId som en FeedOption för att samla frågeresultaten till en specifik partition nyckelintervall värde. 
* Stöd har lagts till för StartTime som en ChangeFeedOption att börja titta för att ändringarna efter den tidpunkten.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Ett problem har åtgärdats i klassen JsonSerializable som kan orsaka ett dataspillsundantag.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Ett problem har åtgärdats som nödvändiga för att kompilera av programmet på grund av introduktionen av JsonSerializerSettings som en valfri parameter i DocumentClient-konstruktorn.
* Markerade DocumentClient-konstruktorn föråldrade den nödvändiga JsonSerializerSettings som den sista parametern så att standardvärdena för ConnectionPolicy och ConsistencyLevel parametrar vid sändning av i JsonSerializerSettings-parametern.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Lagt till stöd för att ange anpassade JsonSerializerSettings vid instansiering [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Ett problem som påverkade x64 har åtgärdats datorer som inte stöder SSE4 instruktion och utlöser en SEHException när du kör Azure Cosmos DB SQL-frågor.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Stöd har lagts till för en ny konsekvensnivå kallas ConsistentPrefix.
*   Stöd har lagts till för frågan mått för enskilda partitioner.
*   Stöd har lagts till för att begränsa storleken på fortsättningstoken för frågor.
*   Tillagt stöd för mer detaljerad spårning för misslyckade förfrågningar.
*   Gjort några prestandaförbättringar i SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Funktionellt samma som 1.13.3. Gjort några interna ändringar.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Funktionellt samma som 1.13.2. Gjort några interna ändringar.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Ett problem som ignoreras PartitionKey-värde som angavs i FeedOptions för mängdfrågor har åtgärdats.
* Ett problem har åtgärdats i transparent hantering av partitionen management under mitten flygning flera partitioner Order By frågekörning.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Ett problem som orsakade låsningar i några av async API: er när de används i kontexten för ASP.NET har åtgärdats.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Åtgärdar att göra SDK mer robust för automatisk redundans under vissa omständigheter.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Korrigering för ett problem som ibland medför en WebException: Fjärrnamnet kunde inte lösas.
* Lagt till stöd för att läsa in ett skrivet dokument genom att lägga till nya överlagringar till ReadDocumentAsync API direkt.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* LINQ-stöd för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig) har lagts till.
* Korrigering för problem med en minnesläcka för objektet ConnectionPolicy beror på användning av händelsehanterare.
* Åtgärda ett problem där UpsertAttachmentAsync inte fungerar när en ETag har använts.
* Åtgärda ett problem där mellan partition ordning av frågan fortsättning inte fungerar vid sortering på strängfält.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Stöd har lagts till för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig). Se [aggregering support](how-to-sql-query.md#Aggregates).
* Sänkte lägsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Åtgärda ett problem där vissa av frågorna som flera partitioner misslyckas i 32-bitars värdprocess.
* Åtgärda ett problem där behållaren session inte uppdaterades med token för misslyckade förfrågningar i Gateway-läge.
* Åtgärda ett problem där en fråga med UDF-anrop i projektion misslyckades kontrollerades i vissa fall.
* Klientprestanda åtgärdar för att öka dataflödet för läsning och skrivning av begäranden.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Åtgärda ett problem där behållaren session inte uppdaterades med token för misslyckade förfrågningar.
* Stöd har lagts till för SDK för att arbeta i en 32-bitars värdprocess. Observera att om du använder flera partition frågor rekommenderas 64-bitars värd bearbetning för bättre prestanda.
* Förbättrad prestanda för scenarier som omfattar frågor med ett stort antal partitionsnyckelvärdena i ett uttryck i Indien.
* Fylls i olika resource kvot statistik i ResourceResponse för dokumentsamling diskläsningsbegäranden när PopulateQuotaInfo begäran om alternativet är inställt.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Lägre prestanda korrigering för CreateDocumentCollectionIfNotExistsAsync API lanserades 1.11.0.
* Prestanda åtgärda i SDK för scenarier som innefattar hög grad av samtidiga begäranden.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Stöd för nya klasser och metoder för att bearbeta den [ändringsflödet](change-feed.md) över dokument i en samling.
* Stöd för fråga över partitioner fortsättning och några perf-förbättringar för flera partitioner frågor.
* Tillägg av CreateDatabaseIfNotExistsAsync och CreateDocumentCollectionIfNotExistsAsync metoder.
* LINQ-stöd för systemfunktioner: IsDefined, IsNull och IsPrimitive.
* Korrigering för automatisk binplacing av Microsoft.Azure.Documents.ServiceInterop.dll och DocumentDB.Spatial.Sql.dll sammansättningar till bin-mappen för programmets när du använder Nuget-paketet med projekt som har project.json verktyg.
* Stöd för avger klienten sida ETW-spårning som kan vara användbara vid felsökning av scenarier.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Har lagts till direktanslutning stöd för partitionerade samlingar.
* Förbättrad prestanda för föråldring konsekvensnivå.
* Har lagts till Polygon och LineString DataTypes när du anger samling indexeringspolicy för geografiska avgränsningar rumsliga förfrågningar.
* Stöd har lagts till LINQ för StringEnumConverter, IsoDateTimeConverter och UnixDateTimeConverter vid översättning av predikat.
* Felkorrigeringar för olika SDK: N.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Ett problem som orsakade följande NotFoundException har åtgärdats: Läs sessionen är inte tillgänglig för inkommande sessionstoken. Det här undantaget inträffade i vissa fall vid fråga för Läs-regionen för ett konto med geo-distribuerad.
* Tillgängliga ResponseStream-egenskap i klassen ResourceResponse, vilket möjliggör direkt åtkomst till den underliggande dataströmmen från ett svar.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Ändrat ResourceResponse, FeedResponse, StoredProcedureResponse och MediaResponse klasser för att implementera gränssnittet för motsvarande offentliga så att de kan vara mockade för testning driven distribution (TDD).
* Ett problem som orsakade ett felaktigt partition viktiga huvud när du använder ett anpassat JsonSerializerSettings-objekt för serialisering av data har åtgärdats.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Ett problem som orsakade långvariga frågor misslyckas med felet har åtgärdats: Auktoriseringstoken är inte giltigt vid aktuell tid.
* Ett problem som har tagit bort den ursprungliga SqlParameterCollection från mellan partition top/sortering efter frågor har åtgärdats.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Stöd har lagts till för parallella frågor partitionerade samlingar.
* Tillagt stöd för skriptkörning över flera partition ORDER BY och upp frågor partitionerade samlingar.
* Fasta saknas referenser till DocumentDB.Spatial.Sql.dll och Microsoft.Azure.Documents.ServiceInterop.dll som krävs när du refererar till ett Azure Cosmos DB-projekt med en referens till Azure Cosmos DB Nuget-paketet.
* Fasta möjligheten att använda parametrar av olika typer när du använder användardefinierade funktioner i LINQ. 
* Ett fel har åtgärdats för globalt replikerade konton där Upsert anrop har dirigeras till läsplatser i stället för att skriva platser.
* Har lagts till metoder för att gränssnittet IDocumentClient som saknades: 
  * UpsertAttachmentAsync-metod som använder mediaStream och alternativ som parametrar
  * CreateAttachmentAsync-metod som tar alternativ som en parameter
  * CreateOfferQuery metod som tar querySpec som en parameter.
* Oförseglade offentliga klasser som visas i gränssnittet IDocumentClient.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Lagt till stöd för flera regioner.
* Stöd har lagts till för nytt försök för begränsade begäranden.  Användare kan anpassa antalet nya försök och Maximal väntetid genom att konfigurera ConnectionPolicy.RetryOptions-egenskapen.
* Lägga till ett nytt IDocumentClient gränssnitt som definierar signaturer för alla DocumentClient egenskaper och metoder.  Som en del av den här ändringen kan också ändra tilläggsmetoder som skapar IQueryable och IOrderedQueryable till metoder i DocumentClient-klassen.
* Har lagts till med konfigurationsalternativ för att ange ServicePoint.ConnectionLimit för en viss Azure Cosmos DB-slutpunkt Uri.  Använd ConnectionPolicy.MaxConnectionLimit om du vill ändra standardvärdet, vilket anges till 50.
* Föråldrad IPartitionResolver och dess implementering.  Stöd för IPartitionResolver är nu föråldrad. Du rekommenderas att du använder partitionerade samlingar för högre lagring och dataflöde.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Lägga till en överlagring till URI: N baserat ExecuteStoredProcedureAsync-metod som tar RequestOptions som en parameter.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Extra tid att live (TTL)-stöd för dokument.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Ett fel har åtgärdats i Nuget paketering av .NET SDK för att paketera den som en del av en Azure Cloud Service-lösning.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementerat [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Fast]**  Genererar fråga Azure Cosmos DB-slutpunkt: 'System.Net.Http.HttpRequestException: Fel vid kopiering av innehåll till en dataström '.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Utökade LINQ stöd, inklusive nya operatorer för växling, villkorlig uttryck och intervall jämförelse.
  * Ta operatorn som ska aktivera väljer upp beteenden i LINQ
  * CompareTo operatorn som ska aktivera strängjämförelser för IP-intervall
  * Villkorlig (?) och slå samman operatorer (?)
* **[Fast]**  ArgumentOutOfRangeException när du kombinerar modellen projektion med var i en LINQ-fråga. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Fast]**  Om Välj inte är det senaste uttrycket LINQ-providern antas inga projektion och produceras väljer * felaktigt.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementerad Upsert, har lagts till UpsertXXXAsync metoder
* Prestandaförbättringar för alla begäranden
* LINQ-providern stöd för villkorlig, coalesce, och CompareTo metoder för strängar
* **[Fast]**  LINQ-provider--> Implementera innehåller metoden i listan om du vill generera samma SQL som på IEnumerable och matrisen
* **[Fast]**  BackoffRetryUtility använder samma HttpRequestMessage igen istället för att skapa ett nytt lösenord vid återförsök
* **[Inaktuell]**  UriFactory.CreateCollection--> nu ska använda UriFactory.CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Fast]**  Lokalisering problem när du använder inte är en kultur info nl-NL, t.ex. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Lagt till ID-baserad Routning
  * Ny UriFactory helper som hjälper till med att konstruera ID-baserad resurslänkar
  * Nya överlagringar på DocumentClient i URI: N
* Har lagts till IsValid() och IsValidDetailed() i LINQ för geospatiala
* LINQ providerstöd förbättrad:
  * **Matematiska** -Abs, funktionerna Acos, Asin, Atan, taket, Cos, Exp, våning, Log, Log10, Pow, resursallokering, logga, Sin, Sqrt, Tan, trunkera
  * **Sträng** -Concat, innehåller EndsWith, IndexOf, antal, ToLower, TrimStart, ersätta, omvänd, TrimEnd, StartsWith, delsträngen, ToUpper
  * **Matris** -Concat, innehåller, antal
  * **Indien** operator

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Stöd har lagts till för att ändra principer för indexering.
  * Ny ReplaceDocumentCollectionAsync-metod i DocumentClient
  * Ny IndexTransformationProgress egenskap i ResourceResponse<T> för att spåra procent förloppet för index principändringar
  * DocumentCollection.IndexingPolicy är nu föränderliga
* Stöd har lagts till för spatial indexerings- och.
  * Den nya Microsoft.Azure.Documents.Spatial namnområdet för serialisering/deserialisering av spatial typer som Point och Polygon
  * Ny SpatialIndex klass för indexering GeoJSON-data som lagras i Cosmos DB
* **[Fast]**  Felaktig SQL-fråga som genereras från en LINQ-uttrycket [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Lagt till ett beroende på Newtonsoft.Json v5.0.7.
* Ändrat stöd för Order By:
  
  * LINQ-providerstöd för OrderBy() eller OrderByDescending()
  * IndexingPolicy för Order By 
    
    **Möjliga icke-bakåtkompatibel ändring** 
    
    Om du har befintlig kod som etablerar samlingar med en anpassad princip för indexering, måste din befintliga kod som ska uppdateras för att stödja den nya IndexingPolicy-klassen. Om du har ingen anpassad indexeringsprincip sedan påverkar den här ändringen inte du.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Stöd har lagts till för att partitionera data med hjälp av de nya HashPartitionResolver och RangePartitionResolver klasserna och IPartitionResolver.
* Har lagts till DataContract serialisering.
* Har stöd har lagts till GUID i LINQ-providern.
* Har lagts till UDF stöd i LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK

## <a name="release--retirement-dates"></a>Fri & Släpp dras tillbaka datum
Microsoft meddelar minst **12 månader** förväg dra tillbaka en SDK för att utjämna övergången till en nyare/stöds version.

Nya funktioner och funktionalitet och optimeringar läggs endast till den aktuella SDK, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Alla begäranden till Azure Cosmos DB med hjälp av en pensionerad SDK avvisas av tjänsten.

<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.2.1](#2.2.1) |24 december 2018 |--- |
| [2.2.0](#2.2.0) |07 december 2018 |--- |
| [2.1.3](#2.1.3) |15 oktober 2018 |--- |
| [2.1.2](#2.1.2) |04 oktober 2018 |--- |
| [2.1.1](#2.1.1) |27 september 2018 |--- |
| [2.1.0](#2.1.0) |21 september 2018 |--- |
| [2.0.0](#2.0.0) |07 september 2018 |--- |
| [1.22.0](#1.22.0) |19 april 2018 |--- |
| [1.21.1](#1.20.1) |09 mars 2018 |--- |
| [1.20.2](#1.20.1) |21 februari 2018 |--- |
| [1.20.1](#1.20.1) |05 februari 2018 |--- |
| [1.19.1](#1.19.1) |16 november 2017 |--- |
| [1.19.0](#1.19.0) |Den 10 november 2017 |--- |
| [1.18.1](#1.18.1) |07 november 2017 |--- |
| [1.18.0](#1.18.0) |Den 17 oktober 2017 |--- |
| [1.17.0](#1.17.0) |10 augusti 2017 |--- |
| [1.16.1](#1.16.1) |07 augusti 2017 |--- |
| [1.16.0](#1.16.0) |02 augusti 2017 |--- |
| [1.15.0](#1.15.0) |Den 30 juni 2017 |--- |
| [1.14.1](#1.14.1) |Den 23 maj 2017 |--- |
| [1.14.0](#1.14.0) |10 maj 2017 |--- |
| [1.13.4](#1.13.4) |09 maj 2017 |--- |
| [1.13.3](#1.13.3) |06 maj 2017 |--- |
| [1.13.2](#1.13.2) |19 april 2017 |--- |
| [1.13.1](#1.13.1) |Den 29 mars 2017 |--- |
| [1.13.0](#1.13.0) |Den 24 mars 2017 |--- |
| [1.12.2](#1.12.2) |20 mars 2017 |--- |
| [1.12.1](#1.12.1) |14 mars 2017 |--- |
| [1.12.0](#1.12.0) |Den 15 februari 2017 |--- |
| [1.11.4](#1.11.4) |06 februari 2017 |--- |
| [1.11.3](#1.11.3) |26 januari 2017 |--- |
| [1.11.1](#1.11.1) |Den 21 december 2016 |--- |
| [1.11.0](#1.11.0) |08 december 2016 |--- |
| [1.10.0](#1.10.0) |27 september 2016 |--- |
| [1.9.5](#1.9.5) |01 september 2016 |--- |
| [1.9.4](#1.9.4) |24 augusti 2016 |--- |
| [1.9.3](#1.9.3) |Den 15 augusti 2016 |--- |
| [1.9.2](#1.9.2) |Den 23 juli 2016 |--- |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |--- |
| [1.7.1](#1.7.1) |06 maj 2016 |--- |
| [1.7.0](#1.7.0) |26 april 2016 |--- |
| [1.6.3](#1.6.3) |08 april 2016 |--- |
| [1.6.2](#1.6.2) |Den 29 mars 2016 |--- |
| [1.5.3](#1.5.3) |Den 19 februari 2016 |--- |
| [1.5.2](#1.5.2) |14 december 2015 |--- |
| [1.5.1](#1.5.1) |Den 23 november 2015 |--- |
| [1.5.0](#1.5.0) |05 oktober 2015 |--- |
| [1.4.1](#1.4.1) |Den 25 augusti 2015 |--- |
| [1.4.0](#1.4.0) |13 augusti 2015 |--- |
| [1.3.0](#1.3.0) |05 augusti 2015 |--- |
| [1.2.0](#1.2.0) |06 juli 2015 |--- |
| [1.1.0](#1.1.0) |April 30, 2015 |--- |
| [1.0.0](#1.0.0) |08 april 2015 |--- |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan. 


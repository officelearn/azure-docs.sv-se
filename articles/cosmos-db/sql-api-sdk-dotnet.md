---
title: 'Azure Cosmos DB: SQL .NET API, SDK &-resurser'
description: Lär dig allt om SQL .NET API och SDK, inklusive versions datum, indragnings datum och ändringar som gjorts mellan varje version av Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: f8c38c46bd60834c166721f62088d8edb2c722a9
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949585"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK för SQL API: Information om hämtning och version
> [!div class="op_single_selector"]
> * [NET](sql-api-sdk-dotnet.md)
> * [NET](sql-api-sdk-dotnet-standard.md)
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
|**Hämta SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API-dokumentation**|[.NET API-referensdokumentation](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Exempel**|[.NET-kodexempel](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Kom igång**|[Kom igång med Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Självstudier om webbappen**|[Utveckling av webbappar med Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Aktuella framework som stöds**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Viktig information

> [!NOTE]
> Om du använder .NET Framework, se den senaste versionen 3. x av [.NET SDK](sql-api-sdk-dotnet-standard.md), som är riktad mot .net standard. 

### <a name="a-name270270"></a><a name="2.7.0"/>2.7.0

* Stöd har lagts till för matriser och objekt i order by-frågor
* Hantera effektiva kollisioner i en partitionsnyckel
* LINQ-stöd har lagts till för flera OrderBy-operatorer med ThenBy-operatorn
* Anpassade inställningar för serialisering används nu för alla upsert och ersättnings åtgärder
* Problem med fast AysncCache-deadlock så att det fungerar med en entrådad Schemaläggaren

### <a name="a-name260260"></a><a name="2.6.0"/>2.6.0

* Lade till PortReusePolicy till ConnectionPolicy
* Fast ntdll! RtlGetVersion TypeLoadException-problem när SDK används i en UWP-app

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* SDK: s system .net. http-version matchar nu vad som definieras i NuGet-paketet.
* Tillåt Skriv förfrågningar för att återgå till en annan region om den ursprungliga misslyckades.
* Lägg till princip för återförsök av session för Skriv förfrågan.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Åtgärdar villkor för att spåra tävling för frågor som orsakade tomma sidor

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Ökad decimal precisions storlek för LINQ-frågor.
* Nya klasser har lagts till CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType och PartitionKeyDefinitionVersion
* Lade till TimeToLivePropertyPath till DocumentCollection
* Lade till CompositeIndexes och SpatialIndexes till IndexPolicy
* Version har lagts till PartitionKeyDefinition
* Ingen har lagts till i PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection och MaxTcpConnectionsPerEndpoint har lagts till i ConnectionPolicy.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Förbättringar av diagnostik

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* Miljö variabel inställningen "POCOSerializationOnly" har lagts till.

* Tog bort DocumentDB. spatial. SQL. dll och ingår nu i Microsoft. Azure. Documents. ServiceInterop. dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Förbättring av omprövnings logik under redundans för StoredProcedure-körnings anrop.

* Gjort DocumentClientEventSource singleton. 

* Åtgärda GatewayAddressCache-timeout och ConnectionPolicy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* För direkt-/TCP-transportprovidern, lades till TransportException, en intern undantags typ för SDK. När det förekommer i undantags meddelanden skriver den här typen ytterligare information för att felsöka problem med klient anslutningen.

* Ny överlagring för konstruktor lades till som tar en HttpMessageHandler, en HTTP-hanterare som används för att skicka HttpClient-förfrågningar (t. ex. HttpClientHandler).

* Åtgärda fel där huvudet med null-värden inte hanteras korrekt.

* Förbättrad verifiering av samlings-cache.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Uppdaterade System.Net.Security till 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Förbättringar av diagnostisk spårning

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
* Åtgärdat fel där attributet JsonProperty i SELECT-satsen för LINQ-frågor inte har lösts.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Åtgärdat fel som träffas under vissa tävlings villkor, vilket resulterar i ett tillfälligt "Microsoft. Azure. Documents. NotFoundException: Read-sessionen är inte tillgänglig för fel i åtkomsttokenbegäran när du använder konsekvens nivån för sessioner.

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

* Interna ändringar för Microsoft-vänners sammansättningar.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Lägga till flera tillförlitlighet korrigeringar och förbättringar.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Stöd har lagts till för PartitionKeyRangeId som en FeedOption för att samla frågeresultaten till en specifik partition nyckelintervall värde. 
* Stöd har lagts till för StartTime som en ChangeFeedOption att börja titta för att ändringarna efter den tidpunkten.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Ett problem har åtgärdats i klassen JsonSerializable som kan orsaka ett dataspillsundantag.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Ett problem som krävde omkompilering av programmet har åtgärd ATS på grund av införandet av JsonSerializerSettings som en valfri parameter i DocumentClient-konstruktorn.
* Markerade DocumentClient-konstruktorn föråldrad som krävde JsonSerializerSettings som den sista parametern för att tillåta standardvärden för parametrarna ConnectionPolicy och ConsistencyLevel vid överföring i JsonSerializerSettings-parametern.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Stöd har lagts till för att ange anpassade JsonSerializerSettings vid instansiering av [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Åtgärdade ett problem som påverkade x64-datorer som inte stöder SSE4-instruktion och genererar en SEHException när Azure Cosmos DB SQL-frågor körs.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Stöd har lagts till för en ny konsekvensnivå kallas ConsistentPrefix.
*   Stöd har lagts till för frågan mått för enskilda partitioner.
*   Stöd har lagts till för att begränsa storleken på fortsättningstoken för frågor.
*   Tillagt stöd för mer detaljerad spårning för misslyckade förfrågningar.
*   Gjort några prestandaförbättringar i SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Fungerar likadant som 1.13.3. Gjort interna ändringar.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Fungerar likadant som 1.13.2. Gjort interna ändringar.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Ett problem som ignoreras PartitionKey-värde som angavs i FeedOptions för mängdfrågor har åtgärdats.
* Ett problem har åtgärdats i transparent hantering av partitionen management under mitten flygning flera partitioner Order By frågekörning.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Ett problem som orsakade låsningar i några av async API: er när de används i kontexten för ASP.NET har åtgärdats.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Åtgärdar att göra SDK mer robust för automatisk redundans under vissa omständigheter.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Åtgärda ett problem som ibland orsakar en WebException: Det gick inte att matcha fjärrnamnet.
* Lagt till stöd för att läsa in ett skrivet dokument genom att lägga till nya överlagringar till ReadDocumentAsync API direkt.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* LINQ-stöd för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig) har lagts till.
* Korrigering för problem med en minnesläcka för objektet ConnectionPolicy beror på användning av händelsehanterare.
* Åtgärda ett problem där UpsertAttachmentAsync inte fungerar när en ETag har använts.
* Åtgärda ett problem där mellan partition ordning av frågan fortsättning inte fungerar vid sortering på strängfält.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Stöd har lagts till för mängdfrågor (COUNT, MIN, MAX, SUM och Genomsnittlig). Se [aggregering support](sql-query-aggregates.md).
* Sänkte lägsta dataflöde på partitionerade samlingar från 10,100 RU/s till 2500 RU/s.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Åtgärda ett problem där det inte gick att köra några frågor över flera partitioner i 32-bitars värd processen.
* Åtgärda ett problem där-sessionens behållare inte uppdaterades med token för misslyckade förfrågningar i Gateway-läge.
* Åtgärda ett problem där en fråga med UDF-anrop i projektion misslyckades i vissa fall.
* Prestanda korrigeringar på klient sidan för att öka data flödet för läsning och skrivning av begär Anden.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Åtgärda ett problem där-sessionens behållare inte uppdaterades med token för misslyckade förfrågningar.
* Stöd har lagts till för SDK för att fungera i en 32-bitars värd process. Observera att om du använder kors partitions frågor rekommenderas 64-bitars värd bearbetning för bättre prestanda.
* Bättre prestanda för scenarier som innefattar frågor med ett stort antal nyckel värden i ett i-uttryck.
* Fyllde i olika resurs kvot statistik i ResourceResponse för läsnings begär Anden för dokument samling när alternativet PopulateQuotaInfo-begäran har angetts.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Mindre prestanda korrigering för CreateDocumentCollectionIfNotExistsAsync-API: et introducerades i 1.11.0.
* Prestanda korrigering i SDK för scenarier som innefattar hög grad av samtidiga begär Anden.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Stöd för nya klasser och metoder för bearbetning av [ändrings flödet](change-feed.md) för dokument i en samling.
* Stöd för frågor om flera partitioner och vissa prestanda förbättringar för frågor över flera partitioner.
* Tillägg av CreateDatabaseIfNotExistsAsync-och CreateDocumentCollectionIfNotExistsAsync-metoder.
* LINQ-stöd för system funktioner: IsDefined, IsNull och IsPrimitive.
* Korrigering för automatisk binplacing av Microsoft. Azure. Documents. ServiceInterop. dll och DocumentDB. spatial. SQL. dll-sammansättningar till programmets bin-mapp när du använder NuGet-paketet med projekt som har Project. JSON-verktyg.
* Stöd för att sända ETW-spårningar på klient sidan, vilket kan vara till hjälp vid fel söknings scenarier.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Direkt anslutnings stöd har lagts till för partitionerade samlingar.
* Bättre prestanda för den begränsade inaktuella konsekvens nivån.
* Har lagts till Polygon och LineString DataTypes när du anger samling indexeringspolicy för geografiska avgränsningar rumsliga förfrågningar.
* LINQ-stöd har lagts till för StringEnumConverter, IsoDateTimeConverter och UnixDateTimeConverter vid översättning av predikat.
* Olika SDK-fel korrigeringar.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Åtgärdat ett problem som orsakade följande NotFoundException: Read-sessionen är inte tillgänglig för token för insession. Detta undantag uppstod i vissa fall vid frågor om läsnings regionen för ett geo-distribuerat konto.
* Exponerade egenskapen ResponseStream i klassen ResourceResponse, som ger direkt åtkomst till den underliggande data strömmen från ett svar.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Ändrade klasserna ResourceResponse, FeedResponse, StoredProcedureResponse och MediaResponse för att implementera motsvarande offentliga gränssnitt så att de kan användas för test driven distribution (TDD).
* Ett problem som orsakade ett felaktigt huvud av partitionsnyckel har åtgärd ATS vid användning av ett anpassat JsonSerializerSettings-objekt för serialisering av data.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Åtgärdat ett problem som gjorde att tids krävande frågor inte gick att utföra: Autentiseringstoken är inte giltig vid aktuell tidpunkt.
* Ett problem har åtgärd ATS som tog bort den ursprungliga SqlParameterCollection från kors partitionens topp-/sorterings frågor.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Stöd har lagts till för parallella frågor för partitionerade samlingar.
* Stöd har lagts till för att korsa partitions ordning efter och TOP-frågor för partitionerade samlingar.
* De saknade referenserna till DocumentDB. spatial. SQL. dll och Microsoft. Azure. Documents. ServiceInterop. dll som krävs vid referenser till ett Azure Cosmos DB-projekt med en referens till Azure Cosmos DB NuGet-paketet.
* Fast möjligheten att använda parametrar av olika typer när du använder användardefinierade funktioner i LINQ. 
* En bugg har åtgärd ATS för globalt replikerade konton där upsert-anrop dirigerades för att läsa platser i stället för Skriv platser.
* Metoder har lagts till i IDocumentClient-gränssnittet som saknades: 
  * UpsertAttachmentAsync-metod som använder mediaStream och Options som parametrar
  * CreateAttachmentAsync-metod som tar alternativ som parameter
  * CreateOfferQuery-metoden som tar querySpec som parameter.
* Icke-förseglade offentliga klasser som exponeras i IDocumentClient-gränssnittet.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* Lagt till stöd för flera regioner.
* Stöd har lagts till för nya försök på begränsade begär Anden.  Användaren kan anpassa antalet återförsök och Max vänte tiden genom att konfigurera egenskapen ConnectionPolicy. RetryOptions.
* Lade till ett nytt IDocumentClient-gränssnitt som definierar signaturerna för alla DocumentClient egenskaper och metoder.  Som en del av den här ändringen har även ändrade tilläggs metoder som skapar IQueryable och IOrderedQueryable till metoder i själva DocumentClient-klassen.
* Konfigurations alternativet har lagts till för att ange ServicePoint. ConnectionLimit för en angiven Azure Cosmos DB slut punkts-URI.  Använd ConnectionPolicy. MaxConnectionLimit om du vill ändra standardvärdet, som är inställt på 50.
* Inaktuell IPartitionResolver och dess implementering.  Support för IPartitionResolver är nu föråldrad. Vi rekommenderar att du använder partitionerade samlingar för högre lagring och data flöde.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Lade till en överlagring till URI-baserad ExecuteStoredProcedureAsync-metod som tar RequestOptions som en parameter.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Extra tid att live (TTL)-stöd för dokument.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Ett fel har åtgärd ATS i NuGet-paketering av .NET SDK för paketering av det som en del av en Azure Cloud Service-lösning.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Implementerat [partitionerade samlingar](partition-data.md) och [användardefinierade prestandanivåer](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Fast]** Fråga Azure Cosmos DB slut punkten genererar: 'System.Net.Http.HttpRequestException: Fel vid kopiering av innehåll till en data ström.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Utökad LINQ-support inklusive nya operatorer för växling, villkors uttryck och jämförelse av intervall.
  * Ta operator för att aktivera SELECT TOP Behavior i LINQ
  * CompareTo-operator för att aktivera jämförelser av sträng intervall
  * Villkorliga (?) och sammanslagnings operatörer (??)
* **[Fast]** ArgumentOutOfRangeException vid kombination av modell projektion med WHERE-in i en LINQ-fråga. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Fast]** Om Select inte är det sista uttrycket, förmodade LINQ-providern ingen projektion och genererade SELECT * felaktigt.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Implementerade upsert, tillagda UpsertXXXAsync-metoder
* Prestanda förbättringar för alla begär Anden
* LINQ Provider-stöd för metoder för villkorlig, sammanslagning och CompareTo för strängar
* **[Fast]** LINQ-Provider – > implementera innehåller metoden på listan för att generera samma SQL som på IEnumerable och matris
* **[Fast]** BackoffRetryUtility använder samma HttpRequestMessage igen i stället för att skapa en ny vid ett nytt försök
* **[Inaktuell]** UriFactory. CreateCollection--> bör nu använda UriFactory. CreateDocumentCollection

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Fast]** Lokaliserings problem när du använder icke-kultur information som nl-NL osv. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Tillagd ID-baserad routning
  * Ny UriFactory-hjälp som hjälper till med att konstruera ID-baserade resurs länkar
  * Nya överlagringar på DocumentClient som ska tas i URI
* IsValid () och IsValidDetailed () har lagts till i LINQ för geospatial
* LINQ-providerns stöd har förbättrats:
  * **Matematik** -ABS, arccos, arcsin, ARCTAN, tak, cos, exp, våning, log, Log10, POW, Round, sign, sin, SQRT, Tan, trunkering
  * **Sträng** – concat, innehåller, EndsWith, indexOf, Count, ToLower, TrimStart, replace, reversed, TrimEnd, del sträng, StartsWith
  * **Matris** – concat, innehåller, antal
  * **In** -operator

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Stöd har lagts till för att ändra indexerings principer.
  * Ny ReplaceDocumentCollectionAsync-metod i DocumentClient
  * Ny IndexTransformationProgress-egenskap i ResourceResponse @ no__t-0T > för spårning av procent förloppet för index princip ändringar
  * DocumentCollection. IndexingPolicy är nu föränderligt
* Stöd har lagts till för rums indexering och frågor.
  * Nytt namn område för Microsoft. Azure. Documents. spatial för serialisering/deserialisering av spatiala typer som punkt och polygon
  * Ny SpatialIndex-klass för indexering av inloggade interjson-data lagrade i Cosmos DB
* **[Fast]** En felaktig SQL-fråga genererades från ett LINQ-uttryck [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Ett beroende har lagts till i Newtonsoft. JSON v 5.0.7.
* Ändrade support order efter:
  
  * LINQ-providerns stöd för OrderBy () eller OrderByDescending ()
  * IndexingPolicy för att stödja order by 
    
    **Möjlig brytar ändring** 
    
    Om du har en befintlig kod som etablerar samlingar med en anpassad indexerings princip måste din befintliga kod uppdateras för att stödja den nya IndexingPolicy-klassen. Om du inte har någon anpassad indexerings princip påverkar den här ändringen inte dig.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Stöd har lagts till för att partitionera data med hjälp av de nya HashPartitionResolver-och RangePartitionResolver-klasserna och IPartitionResolver.
* Tillagd DataContract-serialisering.
* Stöd för GUID i LINQ-providern har lagts till.
* UDF-stöd har lagts till i LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK

## <a name="release--retirement-dates"></a>Frisläpp & indragnings datum
Microsoft tillhandahåller ett meddelande minst **12 månader** i förväg för att dra tillbaka en SDK för att utjämna över gången till en nyare/version som stöds.

Nya funktioner och funktioner och optimeringar läggs bara till i den aktuella SDK: n, så vi rekommenderar att du alltid uppgraderar till den senaste SDK-versionen så tidigt som möjligt. 

Förfrågningar till Azure Cosmos DB med hjälp av en tillbakadragen SDK avvisas av tjänsten.

> [!WARNING]
> Alla versioner **1. x** av .NET SDK för SQL API kommer att dras tillbaka den **30 augusti 2020**.
> 
>
<br/>

| Version | Utgivningsdatum | Slutdatum |
| --- | --- | --- |
| [2.7.0](#2.7.0) |23 september 2019 |--- |
| [2.6.0](#2.6.0) |30 augusti 2019 |--- |
| [2.5.1](#2.5.1) |Den 02 juli 2019 |--- |
| [2.4.1](#2.4.1) |20 juni 2019 |--- |
| [2.4.0](#2.4.0) |Maj 05, 2019 |--- |
| [2.3.0](#2.3.0) |04 april 2019 |--- |
| [2.2.3](#2.2.3) |11 februari 2019 |--- |
| [2.2.2](#2.2.2) |06 februari 2019 |--- |
| [2.2.1](#2.2.1) |24 december 2018 |--- |
| [2.2.0](#2.2.0) |Den 07 december 2018 |--- |
| [2.1.3](#2.1.3) |15 oktober 2018 |--- |
| [2.1.2](#2.1.2) |04 oktober 2018 |--- |
| [2.1.1](#2.1.1) |27 september 2018 |--- |
| [2.1.0](#2.1.0) |21 september 2018 |--- |
| [2.0.0](#2.0.0) |07 september 2018 |--- |
| [1.22.0](#1.22.0) |19 april 2018 | 30 augusti 2020 |
| [1.21.1](#1.20.1) |09 mars 2018 |30 augusti 2020 |
| [1.20.2](#1.20.1) |21 februari 2018 |30 augusti 2020 |
| [1.20.1](#1.20.1) |05 februari 2018 |30 augusti 2020 |
| [1.19.1](#1.19.1) |16 november 2017 |30 augusti 2020 |
| [1.19.0](#1.19.0) |Den 10 november 2017 |30 augusti 2020 |
| [1.18.1](#1.18.1) |Den 07 november 2017 |30 augusti 2020 |
| [1.18.0](#1.18.0) |Den 17 oktober 2017 |30 augusti 2020 |
| [1.17.0](#1.17.0) |10 augusti 2017 |30 augusti 2020 |
| [1.16.1](#1.16.1) |07 augusti 2017 |30 augusti 2020 |
| [1.16.0](#1.16.0) |02 augusti 2017 |30 augusti 2020 |
| [1.15.0](#1.15.0) |30 juni 2017 |30 augusti 2020 |
| [1.14.1](#1.14.1) |Den 23 maj 2017 |30 augusti 2020 |
| [1.14.0](#1.14.0) |10 maj 2017 |30 augusti 2020 |
| [1.13.4](#1.13.4) |09 maj 2017 |30 augusti 2020 |
| [1.13.3](#1.13.3) |Maj 06, 2017 |30 augusti 2020 |
| [1.13.2](#1.13.2) |19 april 2017 |30 augusti 2020 |
| [1.13.1](#1.13.1) |Den 29 mars 2017 |30 augusti 2020 |
| [1.13.0](#1.13.0) |24 mars 2017 |30 augusti 2020 |
| [1.12.2](#1.12.2) |20 mars 2017 |30 augusti 2020 |
| [1.12.1](#1.12.1) |14 mars 2017 |30 augusti 2020 |
| [1.12.0](#1.12.0) |15 februari 2017 |30 augusti 2020 |
| [1.11.4](#1.11.4) |06 februari 2017 |30 augusti 2020 |
| [1.11.3](#1.11.3) |26 januari 2017 |30 augusti 2020 |
| [1.11.1](#1.11.1) |Den 21 december 2016 |30 augusti 2020 |
| [1.11.0](#1.11.0) |Den 08 december 2016 |30 augusti 2020 |
| [1.10.0](#1.10.0) |27 september 2016 |30 augusti 2020 |
| [1.9.5](#1.9.5) |01 september 2016 |30 augusti 2020 |
| [1.9.4](#1.9.4) |24 augusti 2016 |30 augusti 2020 |
| [1.9.3](#1.9.3) |15 augusti 2016 |30 augusti 2020 |
| [1.9.2](#1.9.2) |23 juli 2016 |30 augusti 2020 |
| [1.8.0](#1.8.0) |Den 14 juni 2016 |30 augusti 2020 |
| [1.7.1](#1.7.1) |Maj 06, 2016 |30 augusti 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |30 augusti 2020 |
| [1.6.3](#1.6.3) |08, 2016 |30 augusti 2020 |
| [1.6.2](#1.6.2) |Den 29 mars 2016 |30 augusti 2020 |
| [1.5.3](#1.5.3) |19 februari 2016 |30 augusti 2020 |
| [1.5.2](#1.5.2) |14 december 2015 |30 augusti 2020 |
| [1.5.1](#1.5.1) |23 november 2015 |30 augusti 2020 |
| [1.5.0](#1.5.0) |05 oktober 2015 |30 augusti 2020 |
| [1.4.1](#1.4.1) |25 augusti 2015 |30 augusti 2020 |
| [1.4.0](#1.4.0) |13 augusti 2015 |30 augusti 2020 |
| [1.3.0](#1.3.0) |05 augusti 2015 |30 augusti 2020 |
| [1.2.0](#1.2.0) |06 juli 2015 |30 augusti 2020 |
| [1.1.0](#1.1.0) |30 april 2015 |30 augusti 2020 |
| [1.0.0](#1.0.0) |08, 2015 | 30 augusti 2020 |


## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Se också
Mer information om Cosmos DB finns [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-sidan. 


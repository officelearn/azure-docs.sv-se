---
title: Uppgradera till Azure Search .NET SDK version 1.1 | Microsoft Docs
description: Uppgradera till Azure Search .NET SDK version 1.1
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.openlocfilehash: ccefd21e2aa89a2b46129956b3c4417d548cbf32
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
ms.locfileid: "31796752"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Uppgradera till Azure Search .NET SDK version 1.1

Om du använder version 1.0.2-preview eller tidigare av den [Azure Search .NET SDK](https://aka.ms/search-sdk), den här artikeln hjälper dig att uppgradera ditt program att använda version 1.1.

En mer allmän genomgång av SDK inklusive exempel finns [hur du använder Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

> [!NOTE]
> När du har uppgraderat till version 1.1, eller om du redan använder en version mellan 1.1 och 2.0-preview inklusiva, bör du uppgradera till version 3. Se [uppgradera till Azure Search .NET SDK version 3](search-dotnet-sdk-migration.md) anvisningar.
>

Uppdatera först NuGet-referens för `Microsoft.Azure.Search` NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

När NuGet har laddat ned nya paket och deras beroenden, återskapa projektet.

Om du använde tidigare version 1.0.0-preview, 1.0.1-preview eller 1.0.2-preview, bygga ska lyckas och du är redo att sätta igång!

Om du använde tidigare version 0.13.0-preview eller äldre, bör du se Skapa fel på följande:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Nästa steg är att åtgärda build-fel i taget. De flesta kräver att ändra vissa klass- och metoden namn som har bytt i SDK. [Lista över bryta ändringar i version 1.1](#ListOfChangesV1) innehåller en lista över ändringarna namn.

Om du använder egna klasser för att modellera dokumenten, och dessa klasser har egenskaper av icke-nullbar primitiva typer (till exempel `int` eller `bool` i C#), det finns en felkorrigering i 1.1-versionen av SDK som bör du vara medveten om. Se [felkorrigeringar i version 1.1](#BugFixesV1) mer information.

Slutligen när du har åtgärdat eventuella build-fel, kan du göra ändringar i programmet för att dra nytta av nya funktioner, om du vill.

<a name="ListOfChangesV1"></a>

### <a name="list-of-breaking-changes-in-version-11"></a>Lista över bryta ändringar i version 1.1
I följande lista är sorterade efter sannolikheten att påverkas av programkoden.

#### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch och IndexAction ändringar
`IndexBatch.Create` har bytt namn till `IndexBatch.New` och har inte längre en `params` argumentet. Du kan använda `IndexBatch.New` för batchar som blanda olika typer av åtgärder (sammanslagningar, borttagningar osv.). Dessutom finns nya statiska metoder för att skapa batchar där alla åtgärder är samma: `Delete`, `Merge`, `MergeOrUpload`, och `Upload`.

`IndexAction` Offentliga konstruktorer har inte längre och dess egenskaper är nu inte ändras. Du bör använda de nya statiska metoderna för att skapa åtgärder för olika ändamål: `Delete`, `Merge`, `MergeOrUpload`, och `Upload`. `IndexAction.Create` har tagits bort. Om du använde överlagring som tar endast ett dokument, se till att använda `Upload` i stället.

##### <a name="example"></a>Exempel
Om din kod ser ut så här:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Du kan ändra den till det rätta till eventuella build-fel:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Om du vill kan förenklar du ytterligare det till den här:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

#### <a name="indexbatchexception-changes"></a>IndexBatchException ändringar
Den `IndexBatchException.IndexResponse` egenskapen har bytt namn till `IndexingResults`, och dess typ är nu `IList<IndexingResult>`.

##### <a name="example"></a>Exempel
Om din kod ser ut så här:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Du kan ändra den till det rätta till eventuella build-fel:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

#### <a name="operation-method-changes"></a>Åtgärden metoden ändringar
Varje åtgärd i Azure Search .NET SDK visas som en uppsättning metoden överlagringar för synkrona och asynkrona anropare. Signaturerna och factoring av dessa metoden överlagringar har ändrats i version 1.1.

Till exempel visas ”hämta indexstatistik”-åtgärd i äldre versioner av SDK dessa signaturer:

I `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

I `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Metoden signaturer för samma åtgärd i version 1.1 se ut så här:

I `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

I `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

Från och med version 1.1, ordnar .NET SDK för Azure Search åtgärden metoder på olika sätt:

* Valfria parametrar finns nu modelleras som standard parametrar snarare än ytterligare en metod överlagringar. Detta minskar antalet metoden överlagringar ibland dramatiskt.
* Tilläggsmetoder dölja nu mycket extra information om HTTP från anroparen. Till exempel äldre versioner av SDK returnerade ett svarsobjekt med en HTTP-statuskod som ofta inte behöver du kontrollera eftersom åtgärden metoder som resulterar i `CloudException` för varje statuskod som indikerar ett fel. De nya metoderna för tillägget returnera bara modellobjekt, vilket sparar dig att packa upp dem i din kod.
* Grundläggande gränssnitt däremot nu exponera metoder som ger dig större kontroll på HTTP-nivå om det behövs. Du kan nu skicka in egna HTTP-rubriker som ska ingå i begäranden och den nya `AzureOperationResponse<T>` returtyp ger direktåtkomst till det `HttpRequestMessage` och `HttpResponseMessage` för åtgärden. `AzureOperationResponse` har definierats i den `Microsoft.Rest.Azure` namnområde och ersätter `Hyak.Common.OperationResponse`.

#### <a name="scoringparameters-changes"></a>ScoringParameters ändringar
En ny klass med namnet `ScoringParameter` har lagts till i den senaste SDK: N gör det lättare att ange parametrar för att poängberäkningen profiler i en sökfråga. Tidigare den `ScoringProfiles` -egenskapen för den `SearchParameters` klassen angavs som `IList<string>`; Nu det skrivs som `IList<ScoringParameter>`.

##### <a name="example"></a>Exempel
Om din kod ser ut så här:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Du kan ändra den till det rätta till eventuella build-fel: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

#### <a name="model-class-changes"></a>Klassen Modelländringar
På grund av signatur-ändringar som beskrivs i [igen metoden ändringar](#OperationMethodChanges), många klasser i den `Microsoft.Azure.Search.Models` namnområde har ändrats eller tagits bort. Exempel:

* `IndexDefinitionResponse` har ersatts av `AzureOperationResponse<Index>`
* `DocumentSearchResponse` har bytt namn till `DocumentSearchResult`
* `IndexResult` har bytt namn till `IndexingResult`
* `Documents.Count()` Returnerar nu en `long` med dokumentantal i stället för en `DocumentCountResponse`
* `IndexGetStatisticsResponse` har bytt namn till `IndexGetStatisticsResult`
* `IndexListResponse` har bytt namn till `IndexListResult`

Sammanfattningsvis `OperationResponse`-härledda klasser som fanns endast för att omsluta en model-objektet har tagits bort. De återstående klasserna har fått sina suffix har ändrats från `Response` till `Result`.

##### <a name="example"></a>Exempel
Om din kod ser ut så här:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Du kan ändra den till det rätta till eventuella build-fel:

    IndexerExecutionInfo status = null;

    try
    {
        status = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = status.LastResult;

##### <a name="response-classes-and-ienumerable"></a>Svaret klasser och IEnumerable
En ytterligare ändring som kan påverka din kod är att implementera svar klasser som innehåller samlingar längre `IEnumerable<T>`. Du kan i stället öppna samlingsegenskapen direkt. Om exempelvis koden ser ut så här:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Du kan ändra den till det rätta till eventuella build-fel:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

##### <a name="special-case-for-web-applications"></a>Specialfall för webbprogram
Om du har ett webbprogram som Serialiserar `DocumentSearchResponse` direkt om du vill skicka sökresultaten till webbläsaren, behöver du ändra koden eller resultatet kommer inte att serialisera korrekt. Om exempelvis koden ser ut så här:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q)
        };
    }

Du kan ändra den genom att hämta den `.Results` egenskapen för search-svar för att åtgärda Sök resultatet återgivning:

    public ActionResult Search(string q = "")
    {
        // If blank search, assume they want to search everything
        if (string.IsNullOrWhiteSpace(q))
            q = "*";

        return new JsonResult
        {
            JsonRequestBehavior = JsonRequestBehavior.AllowGet,
            Data = _featuresSearch.Search(q).Results
        };
    }

Du behöver titta i sådana fall i koden själv. **Kompileraren varnar dig inte** eftersom `JsonResult.Data` är av typen `object`.

#### <a name="cloudexception-changes"></a>CloudException ändringar
Den `CloudException` klass har flyttats från den `Hyak.Common` namnområde och den `Microsoft.Rest.Azure` namnområde. Dessutom dess `Error` egenskapen har bytt namn till `Body`.

#### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient och SearchIndexClient ändringar
Typ av den `Credentials` egenskap har ändrats från `SearchCredentials` till dess basklass `ServiceClientCredentials`. Om du behöver åtkomst till den `SearchCredentials` av en `SearchIndexClient` eller `SearchServiceClient`, Använd den nya `SearchCredentials` egenskapen.

I tidigare versioner av SDK, `SearchServiceClient` och `SearchIndexClient` hade konstruktorer som tog en `HttpClient` parameter. Dessa har ersatts av konstruktorer som tar en `HttpClientHandler` och en matris med `DelegatingHandler` objekt. På så sätt blir det lättare att installera egna hanterare för att Förbearbeta HTTP-begäranden om det behövs.

Slutligen konstruktorer som tog en `Uri` och `SearchCredentials` har ändrats. Till exempel om du har kod som ser ut så här:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Du kan ändra den till det rätta till eventuella build-fel:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Även Observera att typen för parametern autentiseringsuppgifter har ändrats till `ServiceClientCredentials`. Det är inte sannolikt att påverka din kod sedan `SearchCredentials` är härledd från `ServiceClientCredentials`.

#### <a name="passing-a-request-id"></a>Skicka en begäran-ID
I äldre versioner av SDK kan du ange en begärande-ID på den `SearchServiceClient` eller `SearchIndexClient` och det skulle ingå i varje begäran REST-API: et. Detta är användbart för felsökning av problem med din söktjänst om du behöver kontakta support. Det är mer användbar för att ange en unik begäran-ID för varje åtgärd i stället för att använda samma ID för alla åtgärder. Därför kan den `SetClientRequestId` metoder för `SearchServiceClient` och `SearchIndexClient` har tagits bort. I stället du kan skicka en begärande-ID till varje åtgärdsmetod via det valfria `SearchRequestOptions` parameter.

> [!NOTE]
> I en framtida version av SDK, kommer vi lägga till en ny mekanism för att ange ID för förfrågan globalt på klienten objekt som är kompatibel med den metod som används av andra Azure-SDK.
> 
> 

#### <a name="example"></a>Exempel
Om du har kod som ser ut så här:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Du kan ändra den till det rätta till eventuella build-fel:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

#### <a name="interface-name-changes"></a>Gränssnittet namnändringar
Namnen på åtgärden gränssnitt har ändrats för att överensstämma med motsvarande egenskapsnamn:

* Typ av `ISearchServiceClient.Indexes` har ändrats från `IIndexOperations` till `IIndexesOperations`.
* Typ av `ISearchServiceClient.Indexers` har ändrats från `IIndexerOperations` till `IIndexersOperations`.
* Typ av `ISearchServiceClient.DataSources` har ändrats från `IDataSourceOperations` till `IDataSourcesOperations`.
* Typ av `ISearchIndexClient.Documents` har ändrats från `IDocumentOperations` till `IDocumentsOperations`.

Den här ändringen är inte troligt att påverka din kod om du har skapat mocks av dessa gränssnitt för testning.

<a name="BugFixesV1"></a>

### <a name="bug-fixes-in-version-11"></a>Felkorrigeringar i version 1.1
Det uppstod ett fel i äldre versioner av Azure Search .NET SDK som är relaterat till serialisering av anpassade modellen klasser. Felet kan inträffa om du har skapat en anpassad modellklass med en egenskap för en icke-nullbar värdetyp.

#### <a name="steps-to-reproduce"></a>Steg för att återskapa
Skapa en anpassad modellklass med en egenskap av typen icke-kan ha värdet null. Till exempel lägga till en offentlig `UnitCount` egenskap av typen `int` i stället för `int?`.

Om du indexera ett dokument med standardvärdet för den aktuella typen (till exempel 0 för `int`), fältet är null i Azure Search. Om du senare söker efter dokument, den `Search` anropet kommer att kasta `JsonSerializationException` klagande som det går inte att konvertera `null` till `int`.

Filter får dessutom inte fungerar som förväntat eftersom null har skrivits till index i stället för det avsedda värdet.

#### <a name="fix-details"></a>Åtgärda information
Vi har löst problemet i version 1.1 av SDK. Nu om du har en modellklass så här:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

och du ställer in `IntValue` 0 värdet är nu korrekt serialiserad som 0 på kabeln och lagras som 0 i indexet. Avrunda utlösning också fungerar som förväntat.

Det finns en potentiella problem vara medveten om med den här metoden: Om du använder en modell av typen med en icke-nullbar egenskap, måste du **garantera** att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller REST API för Azure Search hjälper dig att göra detta.

Detta är inte bara ett hypotetiskt problem. Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan vara null i Azure Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Därför kan rekommenderar vi ändå att du använder kan ha värdet null typer i din modell-klasser som bästa praxis.

Mer information om det här felet och korrigering finns [problemet på GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).


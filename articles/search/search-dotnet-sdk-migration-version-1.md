---
title: Uppgradera till Azure Search .NET SDK version 1.1 - Azure Search
description: Migrera kod till Azure Search .NET SDK version 1.1 från äldre API-versioner. Läs mer om nyheterna och vilka kodändringar krävs.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 3f47656bb13d08ea56cf25a2a29897722abb1cdb
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024155"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-11"></a>Uppgradera till Azure Search .NET SDK version 1.1

> [!Important]
> Det här innehållet är fortfarande under utveckling. Azure Search .NET SDK version 9.0 är tillgängliga på NuGet. Vi arbetar på denna artikel som förklarar hur du uppgraderar till 9.0. 
> 

Om du använder version 1.0.2-preview eller äldre av den [Azure Search .NET SDK](https://aka.ms/search-sdk), den här artikeln hjälper dig att uppgradera programmet att använda version 1.1.

En mer allmän genomgång av SDK inklusive exempel finns i [hur du använder Azure Search från .NET-program](search-howto-dotnet-sdk.md).

> [!NOTE]
> När du uppgraderar till version 1.1, eller om du redan använder en version mellan 1.1 och 2.0-preview inkluderande, bör du uppgradera till version 3. Se [uppgradering till Azure Search .NET SDK version 3](search-dotnet-sdk-migration.md) anvisningar.
>

Först uppdatera dina NuGet-referens för `Microsoft.Azure.Search` med hjälp av NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

Återskapa ditt projekt när NuGet har laddats ned nya paket och deras beroenden.

Om du använde tidigare version 1.0.0-preview, 1.0.1-preview eller 1.0.2-preview, versionen ska lyckas och du är redo att sätta igång!

Om du använde tidigare version 0.13.0-preview eller äldre, bör du se Skapa fel som liknar följande:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Nästa steg är att åtgärda kompileringsfel en i taget. De flesta kräver att ändra vissa namn på klassen och metoden som har bytt i SDK. [Lista över större ändringar i version 1.1](#ListOfChangesV1) innehåller en lista över dessa ändringar.

Om du använder egna klasser för att modellera dina dokument och dessa klasser har egenskaperna för icke-nullbar primitiva typer (till exempel `int` eller `bool` i C#), det finns en felkorrigering i 1.1-versionen av SDK: N som du bör känna. Se [felkorrigeringar i version 1.1](#BugFixesV1) för mer information.

När du har åtgärdat eventuella build-fel, kan du slutligen gör ändringar i ditt program för att dra nytta av nya funktioner om du vill.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista över större ändringar i version 1.1
I följande lista är sorterade efter sannolikheten att programkoden påverkas.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch och IndexAction ändringar
`IndexBatch.Create` har bytt namn till `IndexBatch.New` och har inte längre en `params` argumentet. Du kan använda `IndexBatch.New` för batchar som blanda olika typer av åtgärder (sammanslagningar, tar bort osv.). Dessutom finns nya statiska metoderna för att skapa batchar där alla åtgärder är samma: `Delete`, `Merge`, `MergeOrUpload`, och `Upload`.

`IndexAction` har inte längre publika konstruktorer och dess egenskaper är nu inte kan ändras. Du bör använda de nya statiska metoderna för att skapa åtgärder för olika syften: `Delete`, `Merge`, `MergeOrUpload`, och `Upload`. `IndexAction.Create` har tagits bort. Om du använde överlagring som inte tar bara ett dokument, se till att använda `Upload` i stället.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Du kan ändra det till det här för att åtgärda eventuella build-fel:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Om du vill kan förenklar du ytterligare den så här:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException ändringar
Den `IndexBatchException.IndexResponse` egenskapen har bytt namn till `IndexingResults`, och dess typ är nu `IList<IndexingResult>`.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Du kan ändra det till det här för att åtgärda eventuella build-fel:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Åtgärden metoden ändringar
Varje åtgärd i Azure Search .NET SDK visas som en uppsättning metoden överlagringar för synkrona och asynkrona anrop. Signaturerna och ta hänsyn till av dessa metoden överlagringar har ändrats i version 1.1.

Till exempel visas ”hämta Indexstatistiken”-åtgärd i äldre versioner av SDK: N dessa signaturer:

Följande gäller i `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

Följande gäller i `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

Metodsignaturer för samma åtgärd i version 1.1 ut så här:

Följande gäller i `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

Följande gäller i `IndexesOperationsExtensions`:

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

Från och med version 1.1, organiserar Azure Search .NET SDK åtgärdsmetoder på olika sätt:

* Valfria parametrar modelleras nu som standard parametrar snarare än överlagringar av ytterligare en metod. Detta minskar antalet metoden överlagringar ibland dramatiskt.
* Tilläggsmetoder Dölj nu en massa extra information om HTTP från anroparen. Till exempel äldre versioner av SDK returnerade ett svarsobjekt med en HTTP-statuskod som du ofta inte behöver kontrollera eftersom åtgärdsmetoder som resulterar i `CloudException` för alla statuskod som visar ett fel. De nya metoderna för tillägget returnera bara modellobjekt, sparar du slipper besväret med att behöva packa upp dem i din kod.
* Däremot gränssnitt kärnan nu exponerar metoder som ger dig större kontroll på HTTP-nivå om du behöver den. Du kan nu skicka in anpassade HTTP-huvuden som ska ingå i begäranden och den nya `AzureOperationResponse<T>` returtyp ger direktåtkomst till det `HttpRequestMessage` och `HttpResponseMessage` för åtgärden. `AzureOperationResponse` har definierats i den `Microsoft.Rest.Azure` namnområde och ersätter `Hyak.Common.OperationResponse`.

### <a name="scoringparameters-changes"></a>ScoringParameters ändringar
En ny klass med namnet `ScoringParameter` har lagts till i den senaste SDK att göra det enklare att ange parametrar till poängprofiler i en sökfråga. Tidigare den `ScoringProfiles` egenskapen för den `SearchParameters` klass har skrivits som `IList<string>`; Nu det skrivs som `IList<ScoringParameter>`.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Du kan ändra det till det här för att åtgärda eventuella build-fel: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Förändras i modellen
På grund av signatur-ändringar som beskrivs i [igen metoden ändringar](#OperationMethodChanges), många klasser i den `Microsoft.Azure.Search.Models` namnområde har bytt namn eller tagits bort. Exempel:

* `IndexDefinitionResponse` har ersatts av `AzureOperationResponse<Index>`
* `DocumentSearchResponse` har bytt namn till `DocumentSearchResult`
* `IndexResult` har bytt namn till `IndexingResult`
* `Documents.Count()` Returnerar nu en `long` med antalet dokument i stället för en `DocumentCountResponse`
* `IndexGetStatisticsResponse` har bytt namn till `IndexGetStatisticsResult`
* `IndexListResponse` har bytt namn till `IndexListResult`

Sammanfattningsvis `OperationResponse`-härledda klasser som fanns endast för att omsluta en model-objektet har tagits bort. De återstående klasserna har haft deras suffix som har ändrats från `Response` till `Result`.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

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

Du kan ändra det till det här för att åtgärda eventuella build-fel:

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

#### <a name="response-classes-and-ienumerable"></a>Svaret klasser och IEnumerable
En ytterligare ändring som kan påverka din kod är att det inte längre ska implementera svar klasser som innehåller samlingar `IEnumerable<T>`. Du kan i stället öppna samlingsegenskapen direkt. Exempel: om koden ser ut så här:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Du kan ändra det till det här för att åtgärda eventuella build-fel:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Specialfall för webbprogram
Om du har ett webbprogram som Serialiserar `DocumentSearchResponse` direkt för att skicka sökresultaten till webbläsaren, behöver du ändra din kod eller resultaten kommer inte att serialisera korrekt. Exempel: om koden ser ut så här:

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

Du kan ändra den genom att hämta den `.Results` egenskapen för search-svar att åtgärda rendering för search-resultat:

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

Du behöver leta efter sådana fall i koden själv. **Kompilatorn varnar dig inte** eftersom `JsonResult.Data` är av typen `object`.

### <a name="cloudexception-changes"></a>CloudException ändringar
Den `CloudException` klass har flyttats från den `Hyak.Common` namnområdet för att den `Microsoft.Rest.Azure` namnområde. Dessutom dess `Error` egenskapen har bytt namn till `Body`.

### <a name="searchserviceclient-and-searchindexclient-changes"></a>Ändringar av SearchServiceClient och SearchIndexClient
Vilken typ av den `Credentials` egenskapen har ändrats från `SearchCredentials` till dess basklass `ServiceClientCredentials`. Om du behöver komma åt den `SearchCredentials` av en `SearchIndexClient` eller `SearchServiceClient`, Använd den nya `SearchCredentials` egenskapen.

I tidigare versioner av SDK, `SearchServiceClient` och `SearchIndexClient` hade konstruktorer tog en `HttpClient` parametern. Dessa har ersatts med-konstruktorer som tar en `HttpClientHandler` och en matris med `DelegatingHandler` objekt. Detta gör det enklare att installera anpassad hanterare för att Förbearbeta HTTP-begäranden om det behövs.

Slutligen konstruktorer tog en `Uri` och `SearchCredentials` har ändrats. Exempel: Om du har kod som ser ut så här:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Du kan ändra det till det här för att åtgärda eventuella build-fel:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Observera också att typ av autentiseringsuppgifter-parametern har ändrats till `ServiceClientCredentials`. Det är inte sannolikt att påverka din kod eftersom `SearchCredentials` härleds från `ServiceClientCredentials`.

### <a name="passing-a-request-id"></a>Skicka en begäran-ID
I tidigare versioner av SDK, du kan ange ett ID för begäran på den `SearchServiceClient` eller `SearchIndexClient` och det skulle tas med i varje begäran till REST-API. Detta är användbart för felsökning av problem med din search-tjänst om du behöver kontakta supporten. Det är dock mer användbart att ange ett unikt begäran-ID för varje åtgärd i stället för att använda samma ID för alla åtgärder. Därför måste den `SetClientRequestId` metoderna i `SearchServiceClient` och `SearchIndexClient` har tagits bort. I stället du kan skicka en begäran-ID till varje åtgärdsmetod via den valfria `SearchRequestOptions` parametern.

> [!NOTE]
> I en framtida version av SDK: N ska vi lägga till en ny mekanism för att ange ett ID för förfrågan globalt på klienten objekt som är kompatibel med den metod som används av andra Azure-SDK: er.
> 
> 

### <a name="example"></a>Exempel
Om du har kod som ser ut så här:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Du kan ändra det till det här för att åtgärda eventuella build-fel:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Gränssnittet namnändringar
Namnen på åtgärden gränssnitt har ändrats för att överensstämma med deras motsvarande egenskapsnamn:

* Vilken typ av `ISearchServiceClient.Indexes` har ändrats från `IIndexOperations` till `IIndexesOperations`.
* Vilken typ av `ISearchServiceClient.Indexers` har ändrats från `IIndexerOperations` till `IIndexersOperations`.
* Vilken typ av `ISearchServiceClient.DataSources` har ändrats från `IDataSourceOperations` till `IDataSourcesOperations`.
* Vilken typ av `ISearchIndexClient.Documents` har ändrats från `IDocumentOperations` till `IDocumentsOperations`.

Den här ändringen är inte troligt att påverka din kod, såvida inte du har skapat mocks i de här gränssnitten för testning.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Felkorrigeringar i version 1.1
Det uppstod ett fel i äldre versioner av Azure Search .NET SDK för serialisering av anpassade modellklasser. Felet kan inträffa om du har skapat en anpassad modellklass med en egenskap för en icke-nullbara värdetypen.

### <a name="steps-to-reproduce"></a>Steg för att återskapa
Skapa en anpassad modellklass med en egenskap för icke-nullbara värdetypen. Till exempel lägga till en offentlig `UnitCount` egenskap av typen `int` i stället för `int?`.

Om du indexera ett dokument med den typen standardvärdet (till exempel 0 för `int`), fältet är null i Azure Search. Om du därefter söka efter dokument, den `Search` anrop genereras `JsonSerializationException` klagar som det går inte att konvertera `null` till `int`.

Dessutom kanske filter inte fungerar som förväntat eftersom null har skrivits till indexet i stället för det avsedda värdet.

### <a name="fix-details"></a>Åtgärda information
Vi har löst detta i version 1.1 av SDK. Nu, om du har en modellklass så här:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

och du ställer in `IntValue` till 0, det här värdet är nu korrekt serialiserad som 0 för anslutningen och lagras som 0 i indexet. Avrunda utlösning även fungerar som förväntat.

Det finns ett potentiellt problem som behöver känna till med den här metoden: Om du använder en modelltyp med en icke-nullbar egenskap kan du behöva **garanterar** att inga dokument i indexet innehåller ett null-värde till motsvarande fält. Varken SDK eller REST-API för Azure Search hjälper dig att åstadkomma detta.

Detta är inte bara ett hypotetiskt problem: Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan vara null i Azure Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Därför rekommenderar vi ändå att du använder nullbara typer i dina modellklasser som bästa praxis.

Mer information om den här buggen och korrigeringen finns i [problemet på GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).


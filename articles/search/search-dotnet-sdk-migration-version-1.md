---
title: Uppgradera till Azure Search .NET SDK version 1.1
titleSuffix: Azure Cognitive Search
description: Migrera kod till Azure Search .NET SDK version 1.1 från äldre API-versioner. Läs om nyheter och vilka kodändringar som krävs.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 159aaa8424c3d7a711b587464b80696929f02186
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72792381"
---
# <a name="upgrade-to-azure-search-net-sdk-version-11"></a>Uppgradera till Azure Search .NET SDK version 1.1

Om du använder version 1.0.2-förhandsversionen eller äldre av [Azure Search .NET SDK](https://aka.ms/search-sdk)hjälper den här artikeln dig att uppgradera programmet så att det använder version 1.1.

En mer allmän genomgång av SDK:s exempel finns i [Så här använder du Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

> [!NOTE]
> När du har uppgraderat till version 1.1, eller om du redan använder en version mellan 1.1 och 2.0-förhandsversionen, bör du uppgradera till version 3. Instruktioner [finns i Uppgradera till Azure Search .NET SDK version 3.](search-dotnet-sdk-migration.md)
>

Uppdatera först din NuGet-referens för `Microsoft.Azure.Search` att använda antingen NuGet Package Manager Console eller genom att högerklicka på dina projektreferenser och välja "Hantera NuGet-paket..." i Visual Studio.

När NuGet har laddat ned de nya paketen och deras beroenden kan du återskapa projektet.

Om du tidigare använde version 1.0.0-preview, 1.0.1-preview eller 1.0.2-preview, bör bygget lyckas och du är redo att gå!

Om du tidigare använde version 0.13.0-förhandsversion eller äldre bör du se byggfel som följande:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

Nästa steg är att åtgärda byggfel en efter en. De flesta kräver att ändra vissa klass- och metodnamn som har bytt namn i SDK. [Listan över brytningsändringar i version 1.1](#ListOfChangesV1) innehåller en lista över dessa namnändringar.

Om du använder anpassade klasser för att modellera dina dokument, och dessa klasser har `int` `bool` egenskaper för icke-nullable primitiva typer (till exempel, eller i C#), finns det en buggfix i 1.1-versionen av SDK som du bör vara medveten om. Se [Buggfixar i version 1.1](#BugFixesV1) för mer information.

Slutligen, när du har åtgärdat några byggfel, kan du göra ändringar i ditt program för att dra nytta av nya funktioner om du vill.

<a name="ListOfChangesV1"></a>

## <a name="list-of-breaking-changes-in-version-11"></a>Lista över brytande ändringar i version 1.1
Följande lista ordnas av sannolikheten för att ändringen påverkar din programkod.

### <a name="indexbatch-and-indexaction-changes"></a>IndexBatch och IndexAction förändringar
`IndexBatch.Create`har bytt namn `IndexBatch.New` till och `params` inte längre har något argument. Du kan `IndexBatch.New` använda för batchar som blandar olika typer av åtgärder (sammanslagningar, borttagningar osv.). Dessutom finns det nya statiska metoder för att skapa batchar `MergeOrUpload`där `Upload`alla åtgärder är desamma: `Delete`, `Merge`, och .

`IndexAction`inte längre har offentliga konstruktörer och dess egenskaper är nu oföränderliga. Du bör använda de nya statiska metoderna `Delete` `Merge`för `MergeOrUpload`att `Upload`skapa åtgärder för olika ändamål: , , och . `IndexAction.Create`har tagits bort. Om du använde överbelastningen som bara tar `Upload` ett dokument måste du använda i stället.

#### <a name="example"></a>Exempel
Om koden ser ut så här:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Du kan ändra den till detta för att åtgärda eventuella byggfel:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Om du vill kan du ytterligare förenkla det till detta:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### <a name="indexbatchexception-changes"></a>IndexBatchException ändras
Egenskapen `IndexBatchException.IndexResponse` har bytt `IndexingResults`namn till , `IList<IndexingResult>`och dess typ är nu .

#### <a name="example"></a>Exempel
Om koden ser ut så här:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Du kan ändra den till detta för att åtgärda eventuella byggfel:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>

### <a name="operation-method-changes"></a>Ändringar av operationsmetoden
Varje åtgärd i Azure Search .NET SDK visas som en uppsättning metodöverbelastningar för synkrona och asynkrona anropare. Signaturerna och factoring av dessa metodöverbelastningar har ändrats i version 1.1.

Åtgärden "Hämta indexstatistik" i äldre versioner av SDK visade till exempel dessa signaturer:

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

Metodsignaturerna för samma åtgärd i version 1.1 ser ut så här:

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

Från och med version 1.1 organiserar Azure Search .NET SDK olika åtgärdsmetoder:

* Valfria parametrar modelleras nu som standardparametrar i stället för ytterligare metodöverbelastningar. Detta minskar antalet metodöverbelastningar, ibland dramatiskt.
* Förlängningen metoder döljer nu en hel del av de främmande detaljerna i HTTP från den som ringer. Äldre versioner av SDK returnerade till exempel ett svarsobjekt med en HTTP-statuskod, som `CloudException` du ofta inte behövde kontrollera eftersom åtgärdsmetoderna genererar för en statuskod som indikerar ett fel. De nya tilläggsmetoderna returnerar bara modellobjekt, vilket ger dig besväret att behöva packa upp dem i koden.
* Omvänt visar kärngränssnitten nu metoder som ger dig mer kontroll på HTTP-nivå om du behöver det. Du kan nu skicka in anpassade HTTP-huvuden som `AzureOperationResponse<T>` ska inkluderas i begäranden, och den nya returtypen ger dig direkt åtkomst till `HttpRequestMessage` och `HttpResponseMessage` för åtgärden. `AzureOperationResponse`definieras i `Microsoft.Rest.Azure` namnområdet och `Hyak.Common.OperationResponse`ersätter .

### <a name="scoringparameters-changes"></a>ScoringParameters ändras
En ny `ScoringParameter` klass med namnet har lagts till i den senaste SDK för att göra det enklare att tillhandahålla parametrar för bedömning av profiler i en sökfråga. Tidigare `ScoringProfiles` har egenskapen för `SearchParameters` klassen `IList<string>`skrivits som ; Nu är det `IList<ScoringParameter>`skrivit som .

#### <a name="example"></a>Exempel
Om koden ser ut så här:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Du kan ändra den till detta för att åtgärda eventuella byggfel: 

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### <a name="model-class-changes"></a>Ändringar i modellklass
På grund av signaturändringarna som beskrivs `Microsoft.Azure.Search.Models` i [åtgärdsmetodändringar](#OperationMethodChanges)har många klasser i namnområdet bytt namn eller tagits bort. Ett exempel:

* `IndexDefinitionResponse`har ersatts av`AzureOperationResponse<Index>`
* `DocumentSearchResponse` har bytt namn till `DocumentSearchResult`
* `IndexResult` har bytt namn till `IndexingResult`
* `Documents.Count()`returnerar `long` nu en med antalet dokument i stället för en`DocumentCountResponse`
* `IndexGetStatisticsResponse` har bytt namn till `IndexGetStatisticsResult`
* `IndexListResponse` har bytt namn till `IndexListResult`

Sammanfattningsvis `OperationResponse`har -härledda klasser som bara fanns för att radbrytas ett modellobjekt tagits bort. De återstående klasserna har fått `Response` sitt `Result`suffix ändrat från till .

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

Du kan ändra den till detta för att åtgärda eventuella byggfel:

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

#### <a name="response-classes-and-ienumerable"></a>Svarsklasser och IEnumerable
En ytterligare ändring som kan påverka koden är att `IEnumerable<T>`svarsklasser som innehåller samlingar inte längre implementerar . I stället kan du komma åt samlingsegenskapen direkt. Om koden till exempel ser ut så här:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Du kan ändra den till detta för att åtgärda eventuella byggfel:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### <a name="special-case-for-web-applications"></a>Specialfall för webbapplikationer
Om du har ett webbprogram `DocumentSearchResponse` som serialiserar direkt för att skicka sökresultat till webbläsaren måste du ändra koden eller så serialiseras inte resultatet korrekt. Om koden till exempel ser ut så här:

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

Du kan ändra den `.Results` genom att hämta söksvarets egenskap för att åtgärda sökresultatåtergivningen:

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

Du måste själv leta efter sådana fall i din kod. **Kompilatorn kommer inte att varna dig** eftersom `JsonResult.Data` är av typen `object`.

### <a name="cloudexception-changes"></a>CloudException-ändringar
Klassen `CloudException` har flyttats `Hyak.Common` från namnområdet till `Microsoft.Rest.Azure` namnområdet. Dessutom har `Error` dess egenskap bytt `Body`namn till .

### <a name="searchserviceclient-and-searchindexclient-changes"></a>SearchServiceClient och SearchIndexClient ändringar
Egenskapens `Credentials` typ har `SearchCredentials` ändrats från `ServiceClientCredentials`basklassen . Om du behöver `SearchCredentials` komma `SearchIndexClient` åt `SearchServiceClient`en eller `SearchCredentials` , vänligen använd den nya fastigheten.

I äldre versioner av SDK, `SearchServiceClient` och `SearchIndexClient` hade `HttpClient` konstruktorer som tog en parameter. Dessa har ersatts med konstruktörer som tar en `HttpClientHandler` och en rad `DelegatingHandler` objekt. Detta gör det enklare att installera anpassade hanterare för att förbehandla HTTP-begäranden om det behövs.

Slutligen, konstruktörerna `Uri` `SearchCredentials` som tog en och har förändrats. Om du till exempel har kod som ser ut så här:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Du kan ändra den till detta för att åtgärda eventuella byggfel:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Observera också att typen av parametern `ServiceClientCredentials`credentials har ändrats till . Det är osannolikt att `SearchCredentials` koden påverkas `ServiceClientCredentials`eftersom den härleds från .

### <a name="passing-a-request-id"></a>Skicka ett id-förfrågan
I äldre versioner av SDK kan du ange `SearchServiceClient` ett `SearchIndexClient` begärande-ID på eller och det skulle inkluderas i varje begäran till REST API. Detta är användbart för felsökning av problem med söktjänsten om du behöver kontakta supporten. Det är dock mer användbart att ange ett unikt begärande-ID för varje åtgärd i stället för att använda samma ID för alla åtgärder. Av `SetClientRequestId` `SearchServiceClient` denna anledning, metoderna för och `SearchIndexClient` har tagits bort. I stället kan du skicka ett begärande-ID till varje åtgärdsmetod via den valfria `SearchRequestOptions` parametern.

> [!NOTE]
> I en framtida version av SDK lägger vi till en ny mekanism för att ange ett begärande-ID globalt på klientobjekt som är förenligt med den metod som används av andra Azure SDK:er.
> 
> 

### <a name="example"></a>Exempel
Om du har kod som ser ut så här:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Du kan ändra den till detta för att åtgärda eventuella byggfel:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### <a name="interface-name-changes"></a>Ändringar av gränssnittsnamn
Namn på gränssnittsnamn för operationsgrupp har alla ändrats så att de överensstämmer med motsvarande egenskapsnamn:

* Typen av `ISearchServiceClient.Indexes` har bytt `IIndexOperations` namn `IIndexesOperations`från till .
* Typen av `ISearchServiceClient.Indexers` har bytt `IIndexerOperations` namn `IIndexersOperations`från till .
* Typen av `ISearchServiceClient.DataSources` har bytt `IDataSourceOperations` namn `IDataSourcesOperations`från till .
* Typen av `ISearchIndexClient.Documents` har bytt `IDocumentOperations` namn `IDocumentsOperations`från till .

Det är osannolikt att den här ändringen påverkar koden om du inte har skapat utkast av dessa gränssnitt för teständamål.

<a name="BugFixesV1"></a>

## <a name="bug-fixes-in-version-11"></a>Buggfixar i version 1.1
Det uppstod ett fel i äldre versioner av Azure Search .NET SDK som relaterar till serialisering av anpassade modellklasser. Felet kan uppstå om du har skapat en anpassad modellklass med en egenskap av en värdetyp som inte kan null.The bug could occur if you created a custom model class with a property of a non-nullable value type.

### <a name="steps-to-reproduce"></a>Steg för att reproducera
Skapa en anpassad modellklass med en egenskap av värdetyp som inte kan null. Lägg till exempel `UnitCount` till en `int` offentlig `int?`egenskap av typ i stället för .

Om du indexerar ett dokument med standardvärdet av `int`den typen (till exempel 0 för) är fältet null i Azure Search. Om du sedan söker efter `Search` det `JsonSerializationException` dokumentet, kommer samtalet kasta `null` `int`klagar över att det inte kan konvertera till .

Filter kanske inte heller fungerar som förväntat eftersom null skrevs till indexet i stället för det avsedda värdet.

### <a name="fix-details"></a>Åtgärda information
Vi har åtgärdat problemet i version 1.1 av SDK. Nu, om du har en modell klass som denna:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

och du `IntValue` ställer in på 0, är det värdet nu korrekt serialiserad som 0 på tråden och lagras som 0 i indexet. Rund tripping fungerar också som förväntat.

Det finns ett potentiellt problem att vara medveten om med den här metoden: Om du använder en modelltyp med en egenskap som inte kan null-ska kunna **garanteras** måste du garantera att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller Azure Search REST API hjälper dig att genomdriva detta.

Detta är inte bara ett hypotetiskt problem. Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan vara null i Azure Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Därför rekommenderar vi fortfarande att du använder nullable-typer i modellklasserna som bästa praxis.

Mer information om felet och korrigeringen finns i [det här problemet på GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).


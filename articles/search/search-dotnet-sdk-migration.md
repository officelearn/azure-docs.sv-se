---
title: Uppgradera till Azure Search .NET SDK version 1.1 | Microsoft Docs
description: Uppgradera till Azure Search .NET SDK version 1.1
services: search
documentationcenter: 
author: brjohnstmsft
manager: pablocas
editor: 
ms.assetid: 66f89958-a320-4a24-87f9-69315848909f
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/11/2017
ms.author: brjohnst
ms.openlocfilehash: 9782454e3bfc697b63cde8aa28a14be0c393c36b
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Uppgradera till Azure Search .NET SDK version 3
Om du använder version 2.0 Förhandsgranska eller äldre av den [Azure Search .NET SDK](https://aka.ms/search-sdk), den här artikeln hjälper dig att uppgradera ditt program att använda version 3.

En mer allmän genomgång av SDK inklusive exempel finns [hur du använder Azure Search från ett .NET-program](search-howto-dotnet-sdk.md).

Azure Search .NET SDK-version 3 innehåller vissa ändringar från tidigare versioner. Detta är oftast mindre, så ändrar koden kräver endast minsta möjliga ansträngning. Se [steg för att uppgradera](#UpgradeSteps) för instruktioner om hur du ändrar koden att använda den nya SDK-versionen.

> [!NOTE]
> Om du använder version 1.0.2-preview eller äldre, bör du uppgradera till version 1.1 först och därefter uppgradera till version 3. Se [bilaga: steg för att uppgradera till version 1.1](#UpgradeStepsV1) anvisningar.
>
> Din Azure Search-tjänstinstansen stöder flera REST API-versioner, inklusive det senaste. Du kan fortsätta att använda en version när det är inte längre den senaste, men vi rekommenderar att du migrerar din kod för att använda den senaste versionen. När du använder REST-API, måste du ange API-versionen i varje begäran via parametern api-version. När du använder .NET SDK, anger versionen av du använder SDK motsvarande version av REST API. Om du använder en äldre SDK kan fortsätta du att köra koden utan ändringar, även om tjänsten uppgraderas för att stödja en nyare API-version.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>Vad är nytt i version 3
Version 3 av .NET SDK för Azure Search riktar sig till senast allmänt tillgänglig version av Azure Search-REST-API, särskilt 2016-09-01. Detta gör det möjligt att använda många nya funktioner i Azure Search från ett .NET-program, inklusive följande:

* [Anpassade analysverktyg](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) och [Azure Table Storage](search-howto-indexing-azure-tables.md) stöd för indexerare
* Indexerare anpassning via [fältet mappningar](search-indexer-field-mappings.md)
* ETags som stöd för att aktivera säker samtidig uppdatering av definitioner av index och indexerare datakällor
* Stöd för att skapa index fältdefinitioner deklarativt för pynta modellklass och använda den nya `FieldBuilder` klass.
* Stöd för .NET Core och portabla .NET-profil 111

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Instruktioner för uppgradering
Uppdatera först NuGet-referens för `Microsoft.Azure.Search` NuGet Package Manager-konsolen eller genom att högerklicka på projektreferenserna och välja ”hantera NuGet-paket...” i Visual Studio.

När NuGet har laddat ned nya paket och deras beroenden, återskapa projektet. Beroende på hur koden är strukturerad, kan den återskapa har. I så fall, är du redo att sätta igång!

Om din build misslyckas, bör du se ett build-fel som liknar följande:

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

Nästa steg är att korrigera felet build. Se [bryta ändringar i version 3](#ListOfChanges) för information om vad som orsakar felet och hur du åtgärdar den.

Du kan se ytterligare build-varningar som rör föråldrade metoder eller egenskaper. Varningar innehåller information om vad du ska använda i stället för föråldrad funktion. Om programmet använder till exempel den `IndexingParameters.Base64EncodeKeys` egenskapen som du bör få en varning om att`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`

När du har åtgärdat eventuella build-fel, kan du göra ändringar i programmet för att dra nytta av nya funktioner, om du vill. Nya funktioner i SDK beskrivs i [vad är nytt i version 3](#WhatsNew).

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>Gör ändringar i version 3
Ett litet antal bryta ändringar i version 3 som kan kräva kod ändrar det förutom återskapar ditt program.

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient returtyp
Den `Indexes.GetClient` metoden har en ny returtyp. Tidigare returnerade `SearchIndexClient`, men detta har ändrats till `ISearchIndexClient` i version 2.0-preview och att ändra följer med till version 3. Detta är att stödja kunder som vill mock den `GetClient` metod för kontroller genom att returnera en fingerad implementering av `ISearchIndexClient`.

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

Du kan ändra den till det rätta till eventuella build-fel:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>AnalyzerName och datatyp är inte längre implicit omvandlas till strängar
Det finns många typer i Azure Search .NET SDK som härleds från `ExtensibleEnum`. Tidigare var dessa typer av alla implicit omvandlas till typen `string`. Men ett fel upptäcktes i den `Object.Equals` implementering för dessa klasser och åtgärda felet krävs om du inaktiverar den här implicit konvertering. Explicit konvertering till `string` fortfarande är tillåtet.

#### <a name="example"></a>Exempel
Om din kod ser ut så här:

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

Du kan ändra den till det rätta till eventuella build-fel:

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>Ta bort föråldrade medlemmar

Du kan visa build-fel relaterade till metoder eller egenskaper som har markerats som föråldrade i version 2.0-förhandsgranskning och därefter tas bort i version 3. Om du stöter på dessa fel, är här hur du löser dem:

- Om du använder den här konstruktorn: `ScoringParameter(string name, string value)`, Använd den här i stället:`ScoringParameter(string name, IEnumerable<string> values)`
- Om du använder den `ScoringParameter.Value` anger den `ScoringParameter.Values` egenskapen eller `ToString` metod i stället.
- Om du använder den `SearchRequestOptions.RequestId` anger den `ClientRequestId` egenskapen i stället.

### <a name="removed-preview-features"></a>Borttagna förhandsgranskningsfunktioner

Om du uppgraderar från version 2.0-preview till version 3, Tänk på att JSON och CSV parsning stöd för Blob indexerare har tagits bort eftersom dessa funktioner är fortfarande under förhandsgranskning. Mer specifikt kan följande metoder för den `IndexingParametersExtensions` klass har tagits bort:

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

Om programmet har en fast beroende på de här funktionerna, kan du inte uppgradera till version 3 av Azure Search .NET SDK. Du kan fortsätta att använda version 2.0-preview. Men du tänka på att **vi rekommenderar inte att använda Förhandsgranska SDK: er i produktionsprogram**. Förhandsgranskningsfunktioner gäller enbart och kan ändras.

## <a name="conclusion"></a>Slutsats
Om du vill ha mer information om hur du använder Azure Search .NET SDK finns i vår nyligen uppdaterat [anvisningar](search-howto-dotnet-sdk.md).

Vi uppskattar din feedback om SDK. Om du får problem passa på att be om hjälp oss på den [Azure Search MSDN-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Om du hittar ett programfel kan du filen ett problem i den [Azure .NET SDK GitHub-lagringsplatsen](https://github.com/Azure/azure-sdk-for-net/issues). Se till att prefixet problemet med rubriken ”Sök SDK”:.

Tack för att använda Azure Search!

<a name="UpgradeStepsV1"></a>

## <a name="appendix-steps-to-upgrade-to-version-11"></a>Bilaga: Steg för att uppgradera till version 1.1
> [!NOTE]
> Det här avsnittet gäller enbart för användare i Azure Search .NET SDK version 1.0.2-preview och äldre.
> 
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
`IndexBatch.Create`har bytt namn till `IndexBatch.New` och har inte längre en `params` argumentet. Du kan använda `IndexBatch.New` för batchar som blanda olika typer av åtgärder (sammanslagningar, borttagningar osv.). Dessutom finns nya statiska metoder för att skapa batchar där alla åtgärder är samma: `Delete`, `Merge`, `MergeOrUpload`, och `Upload`.

`IndexAction`Offentliga konstruktorer har inte längre och dess egenskaper är nu inte ändras. Du bör använda de nya statiska metoderna för att skapa åtgärder för olika ändamål: `Delete`, `Merge`, `MergeOrUpload`, och `Upload`. `IndexAction.Create`har tagits bort. Om du använde överlagring som tar endast ett dokument, se till att använda `Upload` i stället.

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
* Grundläggande gränssnitt däremot nu exponera metoder som ger dig större kontroll på HTTP-nivå om det behövs. Du kan nu skicka in egna HTTP-rubriker som ska ingå i begäranden och den nya `AzureOperationResponse<T>` returtyp ger direktåtkomst till det `HttpRequestMessage` och `HttpResponseMessage` för åtgärden. `AzureOperationResponse`har definierats i den `Microsoft.Rest.Azure` namnområde och ersätter `Hyak.Common.OperationResponse`.

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

* `IndexDefinitionResponse`har ersatts av`AzureOperationResponse<Index>`
* `DocumentSearchResponse` har bytt namn till `DocumentSearchResult`
* `IndexResult` har bytt namn till `IndexingResult`
* `Documents.Count()`Returnerar nu en `long` med dokumentantal i stället för en`DocumentCountResponse`
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


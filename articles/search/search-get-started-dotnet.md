---
title: 'Snabb start: skapa ett Sök index i .NET'
titleSuffix: Azure Cognitive Search
description: I den här snabb starten för C#, lär du dig hur du skapar ett index, läser in data och kör frågor med hjälp av klient biblioteket för Azure.Search.Documents.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f3e43a6b72d8de25de3220a9a6ac4e0b3986a467
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94701814"
---
# <a name="quickstart-create-a-search-index-using-the-azuresearchdocuments-client-library"></a>Snabb start: skapa ett sökindex med hjälp av klient biblioteket för Azure.Search.Documents

Använd det nya [ klient biblioteket förAzure.Search.Documents (version 11)](/dotnet/api/overview/azure/search.documents-readme) för att skapa ett .net Core-konsolprogram i C# som skapar, läser in och skickar frågor till ett sökindex.

[Ladda ned käll koden](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart/v11) för att börja med ett färdigt projekt eller följ stegen i den här artikeln för att skapa en egen.

> [!NOTE]
> Letar du efter en tidigare version? Se [skapa ett sökindex med hjälp av Microsoft. Azure. search v10](search-get-started-dotnet-v10.md) i stället.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar har du följande verktyg och tjänster:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/).

+ En Azure Kognitiv sökning-tjänst. [Skapa en tjänst](search-create-service-portal.md) eller [Sök efter en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Du kan använda en kostnads fri tjänst för den här snabb starten. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst. Exempel koden har testats i den kostnads fria community-versionen av Visual Studio 2019.

+ [Azure.Search.Documents NuGet-paket](https://www.nuget.org/packages/Azure.Search.Documents/)

## <a name="set-up-your-project"></a>Konfigurera projektet

Sätt samman anslutnings information för tjänsten och starta sedan Visual Studio för att skapa ett nytt konsol program som kan köras på .NET Core.

<a name="get-service-info"></a>

### <a name="copy-a-key-and-endpoint"></a>Kopiera en nyckel och slut punkt

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomst nyckel på varje begäran. Som ett första steg hittar du API-nyckeln och URL: en som ska läggas till i projektet. Du kommer att ange båda värdena när du skapar klienten i ett senare steg.

1. [Logga](https://portal.azure.com/)in på Azure Portal och hämta URL: en på sidan **Översikt över** Sök tjänsten. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **Inställningar**  >  **nycklar**, hämtar du en administratörs nyckel för fullständiga rättigheter till tjänsten, som krävs om du skapar eller tar bort objekt. Det finns två utbytbara primär-och sekundär nycklar. Du kan använda något av alternativen.

   ![Hämta en HTTP-slutpunkt och åtkomst nyckel](media/search-get-started-rest/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomst nyckel")

Alla begär Anden kräver en API-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

### <a name="install-the-nuget-package"></a>Installera NuGet-paketet

När projektet har skapats lägger du till klient biblioteket. [Azure.Search.Documents-paketet](https://www.nuget.org/packages/Azure.Search.Documents/) består av ett klient bibliotek som innehåller alla API: er som används för att arbeta med en Sök tjänst i .net.

1. I **verktyg**  >  **NuGet Package Manager** väljer du **Hantera NuGet-paket för lösning.**... 

1. Klicka på **Browse** (Bläddra).

1. Sök efter `Azure.Search.Documents` och välj version 11,0 eller senare.

1. Klicka på **Installera** till höger för att lägga till sammansättningen i projektet och lösningen.

### <a name="create-a-search-client"></a>Skapa en sökklient

1. I **program.cs** ändrar du namn området till `AzureSearch.SDK.Quickstart.v11` och lägger sedan till följande `using` direktiv.

   ```csharp
   using Azure;
   using Azure.Search.Documents;
   using Azure.Search.Documents.Indexes;
   using Azure.Search.Documents.Indexes.Models;
   using Azure.Search.Documents.Models;
   ```

1. Skapa två klienter: [SearchIndexClient](/dotnet/api/azure.search.documents.indexes.searchindexclient) skapar indexet och [SearchClient](/dotnet/api/azure.search.documents.searchclient) läser in och skickar frågor till ett befintligt index. Båda behöver tjänstens slut punkt och en administrations-API-nyckel för autentisering med behörigheterna Skapa/ta bort.

   ```csharp
   static void Main(string[] args)
   {
       string serviceName = "<YOUR-SERVICE-NAME>";
       string indexName = "hotels-quickstart-v11";
       string apiKey = "<YOUR-ADMIN-API-KEY>";

       // Create a SearchIndexClient to send create/delete index commands
       Uri serviceEndpoint = new Uri($"https://{serviceName}.search.windows.net/");
       AzureKeyCredential credential = new AzureKeyCredential(apiKey);
       SearchIndexClient idxclient = new SearchIndexClient(serviceEndpoint, credential);

       // Create a SearchClient to load and query documents
       SearchClient srchclient = new SearchClient(serviceEndpoint, indexName, credential);
    ```

## <a name="1---create-an-index"></a>1 – Skapa ett index

Den här snabb starten skapar ett hotell index som du läser in med hotell data och kör frågor mot. I det här steget definierar du fälten i indexet. Varje fält definition innehåller ett namn, data typ och attribut som avgör hur fältet används.

I det här exemplet används synkrona metoder för Azure.Search.Documents-biblioteket för enkelhet och läsbarhet. I produktions scenarier bör du dock använda asynkrona metoder för att hålla din app skalbar och svarar. Du kan till exempel använda [CreateIndexAsync](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindexasync) i stället för [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex).

1. Lägg till en tom klass definition i projektet: **Hotel.cs**

1. Definiera strukturen för ett hotell dokument i **Hotel.cs**.

    ```csharp
    using System;
    using System.Text.Json.Serialization;

    namespace AzureSearch.SDK.Quickstart.v11
    {
        public class Hotel
        {
            [JsonPropertyName("hotelId")]
            public string Id { get; set; }

            [JsonPropertyName("hotelName")]
            public string Name { get; set; }

            [JsonPropertyName("hotelCategory")]
            public string Category { get; set; }

            [JsonPropertyName("baseRate")]
            public Int32 Rate { get; set; }

            [JsonPropertyName("lastRenovationDate")]
            public DateTime Updated { get; set; }
        }
    }
    ```

1. I **program.cs** skapar du ett [SearchIndex](/dotnet/api/azure.search.documents.indexes.models.searchindex) -objekt och anropar sedan [CreateIndex](/dotnet/api/azure.search.documents.indexes.searchindexclient.createindex) -metoden för att uttrycka indexet i din Sök tjänst.

   ```csharp
    // Define an index schema using SearchIndex
    // Create the index using SearchIndexClient
    SearchIndex index = new SearchIndex(indexName)
    {
        Fields =
            {
                new SimpleField("hotelId", SearchFieldDataType.String) { IsKey = true, IsFilterable = true, IsSortable = true },
                new SearchableField("hotelName") { IsFilterable = true, IsSortable = true },
                new SearchableField("hotelCategory") { IsFilterable = true, IsSortable = true },
                new SimpleField("baseRate", SearchFieldDataType.Int32) { IsFilterable = true, IsSortable = true },
                new SimpleField("lastRenovationDate", SearchFieldDataType.DateTimeOffset) { IsFilterable = true, IsSortable = true }
            }
    };

    Console.WriteLine("{0}", "Creating index...\n");
    idxclient.CreateIndex(index);
   ```

Attributen i fältet avgör hur det används i ett program. Till exempel `IsFilterable` måste attributet tilldelas till alla fält som har stöd för ett filter uttryck.

I klient biblioteket för Azure.Search.Documents kan du använda [SearchableField](/dotnet/api/azure.search.documents.indexes.models.searchablefield) och [SimpleField](/dotnet/api/azure.search.documents.indexes.models.simplefield) för att förenkla fält definitionerna. Båda är derivat av en [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) och kan eventuellt förenkla koden:

+ `SimpleField` kan vara vilken datatyp som helst, är alltid icke-sökbar (ignoreras för fullständiga texts öknings frågor) och kan hämtas (den är inte dold). Andra attribut är inaktiverade som standard, men kan aktive ras. Du kan använda en `SimpleField` för dokument-ID: n eller fält som endast används i filter, ansikts eller bedömnings profiler. I så fall, se till att använda alla attribut som krävs för scenariot, till exempel `IsKey = true` för ett dokument-ID. Mer information finns i [SimpleFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SimpleFieldAttribute.cs) i käll koden.

+ `SearchableField` måste vara en sträng och är alltid sökbar och hämtnings bar. Andra attribut är inaktiverade som standard, men kan aktive ras. Eftersom den här fält typen är sökbar, stöder den synonymer och hela komplementet till analys egenskaperna. Mer information finns i [SearchableFieldAttribute.cs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/src/Indexes/SearchableFieldAttribute.cs) i käll koden.

Oavsett om du använder Basic `SearchField` -API: et eller någon av hjälp modeller måste du uttryckligen aktivera attributen filter, aspekt och sortering. Till exempel måste [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable), [IsSortable](/dotnet/api/azure.search.documents.indexes.models.searchfield.issortable)och [IsFacetable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfacetable) uttryckligen vara attribut som visas i exemplet ovan. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 Läs in dokument

Azure Kognitiv sökning söker efter innehåll som lagras i tjänsten. I det här steget ska du läsa in JSON-dokument som stämmer överens med det hotell index som du nyss skapade.

I Azure Kognitiv sökning är Sök dokument data strukturer som båda är indata för indexering och utdata från frågor. Som hämtas från en extern data källa kan dokument indata vara rader i en databas, blobbar i blob-lagring eller JSON-dokument på disk. I det här exemplet ska vi ta en genväg och bädda in JSON-dokument för fem hotell i själva koden. 

När du överför dokument måste du använda ett [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) -objekt. Ett `IndexDocumentsBatch` -objekt innehåller en samling [åtgärder](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1.actions)som innehåller ett dokument och en egenskap som talar om för Azure kognitiv sökning vilken åtgärd som ska utföras ([Ladda upp, sammanfoga, ta bort och mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. I **program.cs** skapar du en matris med dokument-och index åtgärder och skickar sedan matrisen till `IndexDocumentsBatch` . Dokumenten nedan följer V11-indexet Hotels-snabb start, som definieras av hotellet-klassen.

    ```csharp
    // Load documents (using a subset of fields for brevity)
    IndexDocumentsBatch<Hotel> batch = IndexDocumentsBatch.Create(
        IndexDocumentsAction.Upload(new Hotel { Id = "78", Name = "Upload Inn", Category = "hotel", Rate = 279, Updated = new DateTime(2018, 3, 1, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "54", Name = "Breakpoint by the Sea", Category = "motel", Rate = 162, Updated = new DateTime(2015, 9, 12, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "39", Name = "Debug Motel", Category = "motel", Rate = 159, Updated = new DateTime(2016, 11, 11, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "48", Name = "NuGet Hotel", Category = "hotel", Rate = 238, Updated = new DateTime(2016, 5, 30, 7, 0, 0) }),
        IndexDocumentsAction.Upload(new Hotel { Id = "12", Name = "Renovated Ranch", Category = "motel", Rate = 149, Updated = new DateTime(2020, 1, 24, 7, 0, 0) }));

    IndexDocumentsOptions idxoptions = new IndexDocumentsOptions { ThrowOnAnyError = true };

    Console.WriteLine("{0}", "Loading index...\n");
    srchclient.IndexDocuments(batch, idxoptions);
    ```

    När du har initierat [IndexDocumentsBatch](/dotnet/api/azure.search.documents.models.indexdocumentsbatch-1) -objektet kan du skicka det till indexet genom att anropa [IndexDocuments](/dotnet/api/azure.search.documents.searchclient.indexdocuments) på [SearchClient](/dotnet/api/azure.search.documents.searchclient) -objektet.

1. Eftersom det här är en konsol app som kör alla kommandon i tur och ordning, lägger du till en vänte tid på 2 sekunder mellan indexering och frågor.

    ```csharp
    // Wait 2 seconds for indexing to complete before starting queries (for demo and console-app purposes only)
    Console.WriteLine("Waiting for indexing...\n");
    System.Threading.Thread.Sleep(2000);
    ```

    Den 2 sekunderade fördröjningen kompenserar för indexering, som är asynkron, så att alla dokument kan indexeras innan frågorna körs. Att koda i en fördröjning är vanligt vis bara nödvändigt i demonstrationer, tester och exempel program.

## <a name="3---search-an-index"></a>3 – Söka i ett index

Du kan få frågeresultat så snart det första dokumentet har indexerats, men den faktiska testningen av indexet ska vänta tills alla dokument har indexerats.

I det här avsnittet läggs två delar av funktionalitet: fråga efter logik och resultat. Använd [Sök](/dotnet/api/azure.search.documents.searchclient.search) metoden för frågor. Den här metoden tar Sök text (frågesträngen) och andra [alternativ](/dotnet/api/azure.search.documents.searchoptions).

[SearchResults](/dotnet/api/azure.search.documents.models.searchresults-1) -klassen representerar resultatet.

1. I **program.cs** skapar du en WriteDocuments-metod som skriver ut Sök resultat till-konsolen.

    ```csharp
    private static void WriteDocuments(SearchResults<Hotel> searchResults)
    {
        foreach (SearchResult<Hotel> response in searchResults.GetResults())
        {
            Hotel doc = response.Document;
            var score = response.Score;
            Console.WriteLine($"Name: {doc.Name}, Type: {doc.Category}, Rate: {doc.Rate}, Last-update: {doc.Updated}, Score: {score}");
        }

        Console.WriteLine();
    }
    ```

1. Skapa en RunQueries-metod för att köra frågor och returnera resultat. Resultaten är hotell objekt.

    ```csharp
    private static void RunQueries(SearchClient srchclient)
    {
        SearchOptions options;
        SearchResults<Hotel> response;

        Console.WriteLine("Query #1: Search on the term 'motel' and list the relevance score for each match...\n");

        options = new SearchOptions()
        {
            Filter = "",
            OrderBy = { "" }
        };

        response = srchclient.Search<Hotel>("motel", options);
        WriteDocuments(response);

        Console.WriteLine("Query #2: Find hotels where 'type' equals hotel...\n");

        options = new SearchOptions()
        {
            Filter = "hotelCategory eq 'hotel'",
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);

        Console.WriteLine("Query #3: Filter on rates less than $200 and sort by when the hotel was last updated...\n");

        options = new SearchOptions()
        {
            Filter = "baseRate lt 200",
            OrderBy = { "lastRenovationDate desc" }
        };

        response = srchclient.Search<Hotel>("*", options);
        WriteDocuments(response);
    }
    ```

1. Lägg till RunQueries i `Main()` .

    ```csharp
    Console.WriteLine("Starting queries...\n");
    RunQueries(srchclient);
    ```

I det här exemplet visas två [olika sätt att matcha termer i en fråga](search-query-overview.md#types-of-queries): full texts ökning och filter:

+ Full texts öknings frågor för en eller flera termer i sökbara fält i ditt index. Den första frågan är full texts ökning. Full texts ökning ger relevans-Poäng som används för att rangordna resultaten.

+ Filter är ett booleskt uttryck som utvärderas över [IsFilterable](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) fält i ett index. Filtrera frågor, antingen inkludera eller exkludera värden. Därför finns det inga relevans-Poäng för en filter fråga. De två sista frågorna demonstrerar filter sökningen.

Du kan använda full texts ökning och filter tillsammans eller separat.

Både sökningar och filter utförs med metoden [SearchClient. search](/dotnet/api/azure.search.documents.searchclient.search) . En Sök fråga kan skickas i `searchText` strängen, medan ett filter uttryck kan skickas i egenskapen [filter](/dotnet/api/azure.search.documents.searchoptions.filter) i klassen [SearchOptions](/dotnet/api/azure.search.documents.searchoptions) . Om du vill filtrera utan att söka behöver du bara skicka `"*"` för `searchText` parametern för [Sök](/dotnet/api/azure.search.documents.searchclient.search) metoden. Om du vill söka utan filtrering lämnar du `Filter` egenskapen unset eller skickar den inte i en `SearchOptions` instans alls.

## <a name="run-the-program"></a>Köra programmet

Tryck på F5 för att återskapa appen och köra programmet i sin helhet. 

Utdata innehåller meddelanden från [Console. WriteLine](/dotnet/api/system.console.writeline), med tillägget av frågans information och resultat.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kostar pengar. Du kan ta bort resurser individuellt eller ta bort resursgruppen om du vill ta bort hela uppsättningen resurser.

Du kan hitta och hantera resurser i portalen med hjälp av länken **alla resurser** eller **resurs grupper** i det vänstra navigerings fönstret.

Kom ihåg att du är begränsad till tre index, indexerare och data källor om du använder en kostnads fri tjänst. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här snabb starten av C# arbetade du med en uppsättning aktiviteter för att skapa ett index, läsa in det med dokument och köra frågor. I olika steg tog vi genvägar för att förenkla koden för läsbarhet och förståelse. Om du är van vid de grundläggande begreppen rekommenderar vi nästa artikel för en utforskning av alternativa metoder och koncept som kommer att fördjupa dina kunskaper. 

> [!div class="nextstepaction"]
> [Utveckla i .NET](search-howto-dotnet-sdk.md)

Vill du optimera och Spara på dina moln utgifter?

> [!div class="nextstepaction"]
> [Börja analysera kostnaderna med Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
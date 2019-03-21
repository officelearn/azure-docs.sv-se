---
title: Fråga efter data till ett Azure Search-index i C# (.NET SDK) – Azure Search
description: C#-kodexempel för att skapa en sökfråga i Azure Search. Lägg till sökparametrar för att filtrera och sortera sökresultaten.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: 6bb170a5f3353288ab9c393e01b7a0902361913b
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287017"
---
# <a name="quickstart-3---query-an-azure-search-index-in-c"></a>Snabbstart: 3 – fråga ett Azure Search-index iC#

Den här artikeln visar hur du frågar [ett Azure Search-index](search-what-is-an-index.md) med C# och [.NET SDK](https://aka.ms/search-sdk). Söka dokument i indexet utförs genom att utföra dessa uppgifter:

> [!div class="checklist"]
> * Skapa en [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objekt för att ansluta till ett search-index med skrivskyddad behörighet.
> * Skapa en [ `SearchParameters` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet) objekt som innehåller definitionen för söka och filtrera.
> * Anropa den `Documents.Search` metoden på `SearchIndexClient` att skicka frågor till ett index.

## <a name="prerequisites"></a>Förutsättningar

[Läsa in ett Azure Search-index](search-import-data-dotnet.md) med exempeldata hotell.

Hämta en frågenyckel som används för skrivskyddad åtkomst till dokument. Fram till nu har du använt en administrations-API-nyckel så att du kan skapa objekt och innehåll på en söktjänst. Men query-stöd i appar, rekommenderar vi använder en frågenyckel. Anvisningar finns i [skapa en frågenyckel](search-security-api-keys.md#create-query-keys).

## <a name="create-a-client"></a>Skapa en klient
Skapa en instans av den `SearchIndexClient` klassen så att du kan ge den en frågenyckel för skrivskyddad åtkomst (till skillnad från de skrivbehörighet rättigheter på den `SearchServiceClient` används i den förra lektionen).

Den här klassen har flera konstruktorer. Du tar din söktjänstens namn, ett indexnamn och ett [ `SearchCredentials` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchcredentials?view=azure-dotnet) objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

Koden nedan skapar en ny `SearchIndexClient` för indexet ”hotels” med värdena för söktjänstens namn och api-nyckel som lagras i programmets konfigurationsfil (`appsettings.json` för den [exempelprogrammet](https://aka.ms/search-dotnet-howto)):

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

`SearchIndexClient` har en [ `Documents` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient.documents?view=azure-dotnet) egenskapen. Den här egenskapen tillhandahåller alla metoder som du behöver för att skicka frågor mot Azure Search-index.

## <a name="construct-searchparameters"></a>Konstruera SearchParameters
Det är enkelt att söka med .NET SDK. Du anropar bara `Documents.Search`-metoden för din `SearchIndexClient`. Den här metoden stöder några parametrar, inklusive söktexten, tillsammans med ett `SearchParameters`-objekt som kan användas för att ytterligare förfina frågan.

### <a name="types-of-queries"></a>Typer av frågor
De två viktigaste [frågetyperna](search-query-overview.md#types-of-queries) som du använder är `search` och `filter`. En `search`-fråga söker efter en eller flera termer i alla *sökbara* fält i ditt index. En `filter`-fråga utvärderar ett booleskt uttryck i alla *filtrerbara* fält i ett index. Du kan använda sökningar och filter tillsammans eller separat.

Både sökningar och filtreringar utförs med hjälp av metoden `Documents.Search`. En sökfråga kan skickas i parametern `searchText`, medan ett filteruttryck kan skickas i `Filter`-egenskapen för klassen `SearchParameters`. Om du vill filtrera utan sökning skickar du bara `"*"` för `searchText`-parametern. Om du vill söka utan filtrering lämnar du bara `Filter`-egenskapen odefinierad eller väljer att inte skicka den i en `SearchParameters`-instans över huvud taget.

### <a name="example-queries"></a>Exempelfrågor
Följande exempelkod visar några olika sätt att fråga indexet ”hotels” som definierats i [skapa ett Azure Search-index i C# ](search-create-index-dotnet.md#DefineIndex). Observera att dokumenten som returneras med sökresultaten är instanser av den `Hotel` klassen som definierades i [importera data till ett Azure Search-index i C# ](search-import-data-dotnet.md#construct-indexbatch). Exempelkoden utnyttjar en `WriteDocuments`-metod för att mata ut sökresultaten till konsolen. Den här metoden beskrivs i nästa avsnitt.

```csharp
SearchParameters parameters;
DocumentSearchResult<Hotel> results;

Console.WriteLine("Search the entire index for the term 'budget' and return only the hotelName field:\n");

parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);

Console.Write("Apply a filter to the index to find hotels cheaper than $150 per night, ");
Console.WriteLine("and return the hotelId and description:\n");

parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
Console.Write("in descending order, take the top two results, and show only hotelName and ");
Console.WriteLine("lastRenovationDate:\n");

parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'motel':\n");

parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

## <a name="handle-search-results"></a>Hantera sökresultat
Metoden `Documents.Search` returnerar ett `DocumentSearchResult`-objekt som innehåller resultatet av frågan. I exemplet i föregående avsnitt användes metoden `WriteDocuments` för att mata ut sökresultatet till konsolen:

```csharp
private static void WriteDocuments(DocumentSearchResult<Hotel> searchResults)
{
    foreach (SearchResult<Hotel> result in searchResults.Results)
    {
        Console.WriteLine(result.Document);
    }

    Console.WriteLine();
}
```

Här är vad resultatet som ut för frågorna i avsnittet ovan, förutsatt att ”Hotels” fyllts med exempeldata:

```
Search the entire index for the term 'budget' and return only the hotelName field:

Name: Roach Motel

Apply a filter to the index to find hotels cheaper than $150 per night, and return the hotelId and description:

ID: 2   Description: Cheapest hotel in town
ID: 3   Description: Close to town hall and the river

Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Search the entire index for the term 'motel':

ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577
```

Exempelkoden ovan använder konsolen för att mata ut sökresultat. Du behöver också visa sökresultat i ditt eget program. Ett exempel på hur du kan visa sökresultat i en ASP.NET MVC-baserad webbapp finns i den [DotNetSample projekt](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetSample) på GitHub.

## <a name="next-steps"></a>Nästa steg

Om du inte redan gjort det, kan du granska exempelkoden i [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) på GitHub, tillsammans med [hur du använder Azure Search från .NET-program ](search-howto-dotnet-sdk.md) mer detaljerade beskrivningar av exempelkoden. 
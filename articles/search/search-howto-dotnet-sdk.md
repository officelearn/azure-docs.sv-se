---
title: Använda Azure Cognitive Search i .NET
titleSuffix: Azure Cognitive Search
description: Lär dig hur du använder Azure Cognitive Search i ett .NET-program med C# och .NET SDK. Kodbaserade uppgifter inkluderar ansluta till tjänsten, indexinnehåll och fråga ett index.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.devlang: dotnet
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b31a4e40c1e9095499faf265673ab4213ad6bde0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283074"
---
# <a name="how-to-use-azure-cognitive-search-from-a-net-application"></a>Så här använder du Azure Cognitive Search från ett .NET-program

Den här artikeln är en genomgång för att komma igång med [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). Du kan använda .NET SDK för att implementera en omfattande sökupplevelse i ditt program med Azure Cognitive Search.

## <a name="whats-in-the-azure-cognitive-search-sdk"></a>Vad finns i Azure Cognitive Search SDK
SDK består av några klientbibliotek som gör att du kan hantera dina index, datakällor, indexerare och synonymkartor, samt ladda upp och hantera dokument och köra frågor, allt utan att behöva hantera information om HTTP och JSON. Dessa klientbibliotek distribueras alla som NuGet-paket.

Det huvudsakliga NuGet-paketet är `Microsoft.Azure.Search`, som är ett metapaket som innehåller alla andra paket som beroenden. Använd det här paketet om du precis har kommit igång eller om du vet att ditt program behöver alla funktioner i Azure Cognitive Search.

De andra NuGet-paketen i SDK är:
 
  - `Microsoft.Azure.Search.Data`: Använd det här paketet om du utvecklar ett .NET-program med Azure Cognitive Search och du behöver bara fråga eller uppdatera dokument i dina index. Om du också behöver skapa eller uppdatera index, synonymkartor eller `Microsoft.Azure.Search` andra resurser på tjänstnivå använder du paketet i stället.
  - `Microsoft.Azure.Search.Service`: Använd det här paketet om du utvecklar automatisering i .NET för att hantera Azure Cognitive Search-index, synonymkartor, indexerare, datakällor eller andra resurser på tjänstnivå. Om du bara behöver fråga eller uppdatera dokument `Microsoft.Azure.Search.Data` i dina index använder du paketet i stället. Om du behöver alla funktioner i Azure `Microsoft.Azure.Search` Cognitive Search använder du paketet i stället.
  - `Microsoft.Azure.Search.Common`: Vanliga typer som behövs av Azure Cognitive Search .NET-biblioteken. Du behöver inte använda det här paketet direkt i ditt program. Det är bara tänkt att användas som ett beroende.

De olika klientbiblioteken `Index`definierar klasser som `Field`, och `Document`, `SearchServiceClient` `SearchIndexClient` samt åtgärder som `Indexes.Create` och `Documents.Search` på klasserna och. Dessa klasser är ordnade i följande namnområden:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Om du vill ge feedback för en framtida uppdatering av SDK, se vår [feedbacksida](https://feedback.azure.com/forums/263029-azure-search/) eller skapa ett problem på [GitHub](https://github.com/azure/azure-sdk-for-net/issues) och nämna "Azure Cognitive Search" i ärendetiteln.

.NET SDK stöder `2019-05-06` version av [AZURE Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Den här versionen innehåller stöd för [komplexa typer,](search-howto-complex-data-types.md) [AI-anrikning,](cognitive-search-concept-intro.md) [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete)och [JsonLines tolkningsläge](search-howto-index-json-blobs.md) när azure blobbar indexeras. 

Den här SDK stöder inte [hanteringsåtgärder](https://docs.microsoft.com/rest/api/searchmanagement/) som att skapa och skala söktjänster och hantera API-nycklar. Om du behöver hantera sökresurserna från ett .NET-program kan du använda [Azure Cognitive Search .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Uppgradera till den senaste versionen av SDK
Om du redan använder en äldre version av Azure Cognitive Search .NET SDK och vill uppgradera till den senaste allmänt tillgängliga versionen, förklarar [den här artikeln](search-dotnet-sdk-migration-version-9.md) hur.

## <a name="requirements-for-the-sdk"></a>Krav för SDK
1. Visual Studio 2017 eller senare.
2. Din egen Azure Cognitive Search-tjänst. För att kunna använda SDK behöver du namnet på din tjänst och en eller flera API-nycklar. [Skapa en tjänst i portalen](search-create-service-portal.md) hjälper dig genom dessa steg.
3. Ladda ned Azure Cognitive Search .NET SDK [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Search) med hjälp av "Hantera NuGet-paket" i Visual Studio. Sök bara efter `Microsoft.Azure.Search` paketnamnet på NuGet.org (eller något av de andra paketnamnen ovan om du bara behöver en delmängd av funktionaliteten).

Azure Cognitive Search .NET SDK stöder program som är inriktade på .NET Framework 4.5.2 och senare samt .NET Core 2.0 och högre.

## <a name="core-scenarios"></a>Grundläggande scenarier
Det finns flera saker du behöver göra i sökprogrammet. I den här självstudien täcker vi dessa grundläggande scenarier:

* Skapa ett index
* Fylla i indexet med dokument
* Söka efter dokument med hjälp av fulltextsökning och filter

Följande exempelkod illustrerar vart och ett av dessa scenarier. Använd gärna kodavsnitten i ditt eget program.

### <a name="overview"></a>Översikt
Exempelprogrammet som vi ska utforska skapar ett nytt index med namnet "hotell", fyller det med några dokument och kör sedan några sökfrågor. Här är huvudprogrammet, som visar det totala flödet:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    string indexName = configuration["SearchIndexName"];

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteIndexIfExists(indexName, serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateIndex(indexName, serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);

    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);

    ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

    RunQueries(indexClientForQueries);

    Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
    Console.ReadKey();
}
```

> [!NOTE]
> Du hittar hela källkoden för exempelprogrammet i den här genomgången på [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).
> 
>

Vi går igenom det här steg för steg. Först måste vi skapa `SearchServiceClient`en ny . Med det här objektet kan du hantera index. För att kunna skapa ett måste du ange ditt Azure Cognitive Search-tjänstnamn samt en administratörs-API-nyckel. Du kan ange den `appsettings.json` här informationen i filen i [exempelprogrammet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

> [!NOTE]
> Om du anger en felaktig nyckel (till exempel en frågenyckel `SearchServiceClient` där `CloudException` en administratörsnyckel krävdes), kommer det att kasta en med `Indexes.Create`felmeddelandet "Förbjudet" första gången du anropar en åtgärdsmetod på den, till exempel . Om detta händer dig, dubbelkolla vår API-nyckel.
> 
> 

De närmaste raderna anropar metoder för att skapa ett index med namnet "hotell", och tar bort det först om det redan finns. Vi kommer att gå igenom dessa metoder lite senare.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Därefter måste indexet fyllas i. För att fylla i indexet behöver `SearchIndexClient`vi en . Det finns två sätt att få en: genom `Indexes.GetClient` att `SearchServiceClient`bygga den, eller genom att uppmana . Vi använder det senare för enkelhetens skull.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> I ett typiskt sökprogram kan indexhantering och population hanteras av en separat komponent från sökfrågor. `Indexes.GetClient`är bekvämt för att fylla ett index eftersom det `SearchCredentials`sparar dig besväret med att ge ytterligare . Den gör det genom att skicka administratörsnyckeln som du använde för att skapa `SearchServiceClient` till den nya `SearchIndexClient`. Men i den del av programmet som kör frågor är `SearchIndexClient` det bättre att skapa direkt så att du kan skicka in en frågenyckel, vilket bara gör att du kan läsa data i stället för en administratörsnyckel. Den här riktlinjen följer principen om lägsta behörighet och hjälper till att göra programmet säkrare. Du kan läsa mer om admin nycklar och frågenycklar [här](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Nu när vi `SearchIndexClient`har en, kan vi befolka indexet. Indexpopulationen görs med en annan metod som vi kommer att gå igenom senare.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Slutligen kör vi några sökfrågor och visar resultaten. Den här gången `SearchIndexClient`använder vi en annan:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Vi kommer att ta `RunQueries` en närmare titt på metoden senare. Här är koden för `SearchIndexClient`att skapa den nya:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Den här gången använder vi en frågenyckel eftersom vi inte behöver skrivåtkomst till indexet. Du kan ange den `appsettings.json` här informationen i filen i [exempelprogrammet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Om du kör det här programmet med ett giltigt tjänstnamn och API-nycklar ska utdata se ut så här: (Vissa konsolutdata har ersatts med "..." för illustrationsändamål.)

    Deleting index...

    Creating index...

    Uploading documents...

    Waiting for documents to be indexed...

    Search the entire index for the term 'motel' and return only the HotelName field:

    Name: Secret Point Motel

    Name: Twin Dome Motel


    Apply a filter to the index to find hotels with a room cheaper than $100 per night, and return the hotelId and description:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Times Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.

    HotelId: 2
    Description: The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.


    Search the entire index, order by a specific field (lastRenovationDate) in descending order, take the top two results, and show only hotelName and lastRenovationDate:

    Name: Triple Landscape Hotel
    Last renovated on: 9/20/2015 12:00:00 AM +00:00

    Name: Twin Dome Motel
    Last renovated on: 2/18/1979 12:00:00 AM +00:00


    Search the hotel names for the term 'hotel':

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

    Complete.  Press any key to end application... 

Programmets fullständiga källkod finns i slutet av den här artikeln.

Därefter kommer vi att ta en närmare titt `Main`på var och en av de metoder som kallas av .

### <a name="creating-an-index"></a>Skapa ett index
När du `SearchServiceClient` `Main` har skapat ett tas "hotellindex" bort om det redan finns. Strykningen görs med följande metod:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Den här metoden `SearchServiceClient` använder den angivna för att kontrollera om indexet finns, och i så fall ta bort det.

> [!NOTE]
> Exempelkoden i den här artikeln använder synkronmetoderna i Azure Cognitive Search .NET SDK för enkelhetens skull. Vi rekommenderar att du använder asynkrona metoder i dina egna program så att de blir skalbara och responsiva. Till exempel i metoden ovan `ExistsAsync` kan `DeleteAsync` du `Exists` `Delete`använda och i stället för och .
> 
> 

Därefter `Main` skapar ett nytt "hotell"-index genom att anropa den här metoden:

```csharp
private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    
    serviceClient.Indexes.Create(definition);
}
```

Med den här `Index` metoden skapas `Field` ett nytt objekt med en lista med objekt som definierar schemat för det nya indexet. Varje fält har ett namn, en datatyp och flera attribut som definierar dess sökbeteende. Klassen `FieldBuilder` använder reflektion för att `Field` skapa en lista över objekt för indexet `Hotel` genom att undersöka de offentliga egenskaperna och attributen för den angivna modellklassen. Vi tar en närmare titt `Hotel` på klassen senare.

> [!NOTE]
> Du kan alltid skapa `Field` listan med `FieldBuilder` objekt direkt i stället för att använda om det behövs. Du kanske till exempel inte vill använda en modellklass eller så kan du behöva använda en befintlig modellklass som du inte vill ändra genom att lägga till attribut.
>
> 

Förutom fält kan du också lägga till bedömningsprofiler, förslagsställare eller CORS-alternativ i indexet (dessa parametrar utelämnas från exemplet för korthet). Du hittar mer information om indexobjektet och dess beståndsdelar i [SDK-referensen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index)samt i [AZURE Cognitive Search REST API-referensen](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Fylla i indexet
Nästa steg `Main` i fyller det nyskapade indexet. Indexpopulationen görs i följande metod: (Viss kod ersätts med "..." för illustrationsändamål.  Se hela exempellösningen för den fullständiga datapopulationskoden.)

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        {
            HotelId = "1",
            HotelName = "Secret Point Motel",
            ...
            Address = new Address()
            {
                StreetAddress = "677 5th Ave",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Budget Room, 1 Queen Bed (Cityside)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (City View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "2",
            HotelName = "Twin Dome Motel",
            ...
            {
                StreetAddress = "140 University Town Center Dr",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Suite, 2 Double Beds (Mountain View)",
                    ...
                },
                new Room()
                {
                    Description = "Standard Room, 1 Queen Bed (City View)",
                    ...
                },
                new Room()
                {
                    Description = "Budget Room, 1 King Bed (Waterfront View)",
                    ...
                }
            }
        },
        new Hotel()
        {
            HotelId = "3",
            HotelName = "Triple Landscape Hotel",
            ...
            Address = new Address()
            {
                StreetAddress = "3393 Peachtree Rd",
                ...
            },
            Rooms = new Room[]
            {
                new Room()
                {
                    Description = "Standard Room, 2 Queen Beds (Amenities)",
                    ...
                },
                new Room ()
                {
                    Description = "Standard Room, 2 Double Beds (Waterfront View)",
                    ...
                },
                new Room()
                {
                    Description = "Deluxe Room, 2 Double Beds (Cityside)",
                    ...
                }
            }
        }
    };

    var batch = IndexBatch.Upload(hotels);

    try
    {
        indexClient.Documents.Index(batch);
    }
    catch (IndexBatchException e)
    {
        // Sometimes when your Search service is under load, indexing will fail for some of the documents in
        // the batch. Depending on your application, you can take compensating actions like delaying and
        // retrying. For this simple demo, we just log the failed document keys and continue.
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

    Console.WriteLine("Waiting for documents to be indexed...\n");
    Thread.Sleep(2000);
}
```

Denna metod har fyra delar. Den första skapar en matris `Hotel` med 3 `Room` objekt vardera med 3 objekt som kommer att fungera som våra indata för att ladda upp till indexet. Dessa data är hårdkodade för enkelhetens skull. I ditt eget program kommer dina data troligen att komma från en extern datakälla, till exempel en SQL-databas.

Den andra delen `IndexBatch` skapar en innehållande dokument. Du anger den åtgärd som du vill koppla till batchen när `IndexBatch.Upload`du skapar den, i det här fallet genom att anropa . Batchen överförs sedan till Azure Cognitive Search-indexet med `Documents.Index` metoden.

> [!NOTE]
> I det här exemplet laddar vi bara upp dokument. Om du vill koppla ändringar till befintliga dokument eller ta `IndexBatch.Merge`bort `IndexBatch.MergeOrUpload`dokument `IndexBatch.Delete` kan du skapa batchar genom att anropa , eller i stället. Du kan också blanda olika åtgärder `IndexBatch.New`i en enda `IndexAction` batch genom att anropa , som tar en samling objekt, som var och en talar om för Azure Cognitive Search att utföra en viss åtgärd i ett dokument. Du kan `IndexAction` skapa var och en med en `IndexAction.Merge` `IndexAction.Upload`egen åtgärd genom att anropa motsvarande metod, till exempel , och så vidare.
> 
> 

Den tredje delen av den här metoden är ett catch-block som hanterar ett viktigt felfall för indexering. Om din Azure Cognitive Search-tjänst inte indexerar `IndexBatchException` vissa av `Documents.Index`dokumenten i batchen, genereras en av . Det här undantaget kan inträffa om du indexerar dokument medan tjänsten är under stor belastning. **Vi rekommenderar starkt att du uttryckligen hanterar den här situationen i din kod.** Du kan fördröja och sedan försöka indexera dokumentet som misslyckades igen eller så kan du logga och fortsätta som i exemplet, eller göra något annat beroende på programmets krav på datakonsekvens.

> [!NOTE]
> Du kan [`FindFailedActionsToRetry`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) använda metoden för att skapa en ny batch som `Index`bara innehåller de åtgärder som misslyckades i ett tidigare anrop till . Det finns en diskussion om hur du korrekt använder den [på StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Slutligen försenas metoden i `UploadDocuments` två sekunder. Indexering sker asynkront i din Azure Cognitive Search-tjänst, så exempelprogrammet måste vänta en kort tid för att säkerställa att dokumenten är tillgängliga för sökning. Fördröjningar som den här är normalt endast nödvändiga i demonstrationer, tester och exempelprogram.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Hur .NET SDK hanterar dokument
Du kanske undrar hur Azure Cognitive Search .NET SDK kan överföra instanser `Hotel` av en användardefinierad klass som till indexet. För att besvara den `Hotel` frågan, låt oss titta på klassen:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsSearchable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.EnLucene)]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("Description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    // SmokingAllowed reflects whether any room in the hotel allows smoking.
    // The JsonIgnore attribute indicates that a field should not be created 
    // in the index for this property and it will only be used by code in the client.
    [JsonIgnore]
    public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? Rating { get; set; }

    public Address Address { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }

    public Room[] Rooms { get; set; }
}
```

Det första du bör lägga märke till är `Hotel` att namnet på varje offentlig egenskap i klassen mappas till ett fält med samma namn i indexdefinitionen. Om du vill att varje fält ska börja med en gemens bokstav ("kamelfall" kan du be SDK att mappa egenskapsnamnen till kamelfall automatiskt med `[SerializePropertyNamesAsCamelCase]` attributet på klassen. Det här scenariot är vanligt i .NET-program som utför databindning där målschemat ligger utanför programutvecklarens kontroll utan att behöva bryta mot namngivningsriktlinjerna "Pascal case" i .NET.

> [!NOTE]
> Azure Cognitive Search .NET SDK använder [NewtonSoft-JSON.NET-biblioteket](https://www.newtonsoft.com/json/help/html/Introduction.htm) för att serialisera och avserialisera dina anpassade modellobjekt till och från JSON. Du kan anpassa den här serialiseringen om det behövs. Mer information finns i [Anpassad serialisering med JSON.NET](#JsonDotNet).
> 
> 

Den andra sak att märka är varje egenskap `IsFilterable` `IsSearchable`är `Key`dekorerad med attribut som , , och `Analyzer`. Dessa attribut mappas direkt till [motsvarande fältattribut i ett Azure Cognitive Search-index](/rest/api/searchservice/create-index). Klassen `FieldBuilder` använder dessa egenskaper för att konstruera fältdefinitioner för indexet.

Det tredje viktiga `Hotel` med klassen är datatyperna för de offentliga egenskaperna. .NET-typerna för dessa egenskaper mappar till deras motsvarande fälttyper i indexdefinitionen. Exempelvis mappar `Category`-strängegenskapen till `category`-fältet, som är av typen `Edm.String`. Det finns liknande typmappningar `DateTimeOffset?`mellan `Edm.DateTimeOffset` `bool?`, `Edm.Boolean`, och så vidare. De specifika reglerna för typmappningen `Documents.Get` dokumenteras med metoden i [Azure Cognitive Search .NET SDK-referensen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). Klassen `FieldBuilder` tar hand om den här mappningen åt dig, men det kan fortfarande vara bra att förstå om du behöver felsöka eventuella serialiseringsproblem.

Har du råkade `SmokingAllowed` lägga märke till fastigheten?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Attributet `JsonIgnore` på den `FieldBuilder` här egenskapen talar om för att inte serialisera det till indexet som ett fält.  Det här är ett bra sätt att skapa beräknade egenskaper på klientsidan som du kan använda som medhjälpare i ditt program.  I detta fall `SmokingAllowed` återspeglar fastigheten `Room` om `Rooms` någon i samlingen tillåter rökning.  Om alla är falska, indikerar det att hela hotellet inte tillåter rökning.

Vissa egenskaper, `Address` `Rooms` till exempel och är förekomster av .NET-klasser.  Dessa egenskaper representerar mer komplexa datastrukturer och kräver därför fält med en [komplex datatyp](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) i indexet.

Egenskapen `Address` representerar en uppsättning `Address` med flera värden i klassen, som definieras nedan:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Address
    {
        [IsSearchable]
        public string StreetAddress { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string City { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string StateProvince { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string PostalCode { get; set; }

        [IsSearchable, IsFilterable, IsSortable, IsFacetable]
        public string Country { get; set; }
    }
}
```

Den här klassen innehåller de standardvärden som används för att beskriva adresser i USA eller Kanada. Du kan använda såna typer för att gruppera logiska fält i indexet.

Egenskapen `Rooms` representerar `Room` en matris med objekt:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Newtonsoft.Json;

namespace AzureSearch.SDKHowTo
{
    public partial class Room
    {
        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
        public string Description { get; set; }

        [IsSearchable]
        [Analyzer(AnalyzerName.AsString.FrMicrosoft)]
        [JsonProperty("Description_fr")]
        public string DescriptionFr { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string Type { get; set; }

        [IsFilterable, IsFacetable]
        public double? BaseRate { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string BedOptions { get; set; }

        [IsFilterable, IsFacetable]
        public int SleepsCount { get; set; }

        [IsFilterable, IsFacetable]
        public bool? SmokingAllowed { get; set; }

        [IsSearchable, IsFilterable, IsFacetable]
        public string[] Tags { get; set; }
    }
}
```

Din datamodell i .NET och motsvarande indexschema bör utformas för att stödja den sökupplevelse som du vill ge till slutanvändaren. Varje objekt på den översta nivån i .NET, dvs-dokumentet i indexet, motsvarar ett sökresultat som du skulle presentera i användargränssnittet. I ett hotellsökprogram kanske dina slutanvändare till exempel vill söka efter hotellnamn, hotellets funktioner eller egenskaperna hos ett visst rum. Vi kommer att täcka några frågeexempel lite senare.

Den här möjligheten att använda dina egna klasser för att interagera med dokument i indexet fungerar i båda riktningarna. Du kan också hämta sökresultat och låta SDK automatiskt avserialisera dem till en typ av val, som vi kommer att se i nästa avsnitt.

> [!NOTE]
> Azure Cognitive Search .NET SDK stöder också dynamiskt `Document` skrivna dokument med klassen, som är en nyckel-/värdemappning av fältnamn till fältvärden. Detta är användbart i scenarier då du inte känner till indexeringsschemat redan i designfasen, eller då det skulle vara opraktiskt att binda till specifika modellklasser. Alla metoder i SDK som hanterar dokument har överlagringar som fungerar med klassen `Document`, samt starkt typifierade överlagringar som använder en parameter av generisk typ. Endast de senare används i exempelkoden i den här självstudien. [ `Document` Klassen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) ärver `Dictionary<string, object>`från .
> 
>

**Varför du bör använda datatyper som kan ha värdet null**

När du utformar egna modellklasser för att mappa till ett Azure `bool` Cognitive `int` Search-index rekommenderar vi `bool?` att `bool`du deklarerar egenskaper för värdetyper som och är nullable (till exempel i stället för ). Om du använder en icke-nullbar egenskap måste du **se till** att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller Azure Cognitive Search-tjänsten hjälper dig att genomdriva detta.

Detta är inte bara ett hypotetiskt problem. Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer är nullable i Azure Cognitive Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Av den anledningen rekommenderar vi att du använder nullbara typer i dina modellklasser som bästa praxis.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Anpassad serialisering med JSON.NET
SDK använder JSON.NET för serialisering och deserialisering av dokument. Du kan anpassa serialisering och deserialisering om `JsonConverter` `IContractResolver`det behövs genom att definiera din egen eller . Mer information finns i [dokumentationen JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm). Detta kan vara användbart när du vill anpassa en befintlig modellklass från ditt program för användning med Azure Cognitive Search och andra mer avancerade scenarier. Med anpassad serialisering kan du till exempel:

* Inkludera eller utesluta att vissa egenskaper för modellklassen lagras som dokumentfält.
* Mappa mellan egenskapsnamn i koden och fältnamnen i indexet.
* Skapa anpassade attribut som kan användas för att mappa egenskaper till dokumentfält.

Du kan hitta exempel på att implementera anpassad serialisering i enhetstesterna för Azure Cognitive Search .NET SDK på GitHub. En bra utgångspunkt är [den här mappen](https://github.com/Azure/azure-sdk-for-net/tree/4f6f4e4c90200c1b0621c4cead302a91e89f2aba/sdk/search/Microsoft.Azure.Search/tests/Tests/Models). Den innehåller klasser som används av de anpassade serialiseringstesterna.

### <a name="searching-for-documents-in-the-index"></a>Söka efter dokument i indexet
Det sista steget i exempelprogrammet är att söka efter några dokument i indexet:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
    SearchParameters parameters;
    DocumentSearchResult<Hotel> results;

    Console.WriteLine("Search the entire index for the term 'motel' and return only the HotelName field:\n");

    parameters =
        new SearchParameters()
        {
            Select = new[] { "HotelName" }
        };

    results = indexClient.Documents.Search<Hotel>("motel", parameters);

    WriteDocuments(results);

    Console.Write("Apply a filter to the index to find hotels with a room cheaper than $100 per night, ");
    Console.WriteLine("and return the hotelId and description:\n");

    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(r: r/BaseRate lt 100)",
            Select = new[] { "HotelId", "Description" }
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.Write("Search the entire index, order by a specific field (lastRenovationDate) ");
    Console.Write("in descending order, take the top two results, and show only hotelName and ");
    Console.WriteLine("lastRenovationDate:\n");

    parameters =
        new SearchParameters()
        {
            OrderBy = new[] { "LastRenovationDate desc" },
            Select = new[] { "HotelName", "LastRenovationDate" },
            Top = 2
        };

    results = indexClient.Documents.Search<Hotel>("*", parameters);

    WriteDocuments(results);

    Console.WriteLine("Search the entire index for the term 'hotel':\n");

    parameters = new SearchParameters();
    results = indexClient.Documents.Search<Hotel>("hotel", parameters);

    WriteDocuments(results);
}
```

Varje gång en fråga körs skapar den här `SearchParameters` metoden först ett nytt objekt. Det här objektet används för att ange ytterligare alternativ för frågan, till exempel sortering, filtrering, växling och fasning. I den här metoden anger `Filter` `Select`vi `OrderBy`egenskapen , , och `Top` för olika frågor. Alla `SearchParameters` egenskaper dokumenteras [här](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Nästa steg är att faktiskt köra sökfrågan. Körningen av sökningen `Documents.Search` görs med metoden. För varje fråga skickar vi söktexten som `"*"` ska användas som en sträng (eller om det inte finns någon söktext), plus sökparametrarna som skapats tidigare. Vi anger `Hotel` också som `Documents.Search`typparameter för , som talar om för SDK att `Hotel`deserialisera dokument i sökresultaten till objekt av typen .

> [!NOTE]
> Du hittar mer information om syntaxen för sökfrågeuttryck [här](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Slutligen, efter varje fråga den här metoden itererar igenom alla matchningar i sökresultaten, skriva ut varje dokument till konsolen:

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

Låt oss ta en närmare titt på var och en av frågorna i tur och ordning. Här är koden för att köra den första frågan:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

I det här fallet söker vi igenom hela indexet efter ordet "motell" i valbart fält och vi `Select` vill bara hämta hotellnamnen, enligt parametern. Här är resultaten:

    Name: Secret Point Motel

    Name: Twin Dome Motel

Nästa fråga är lite mer intressant.  Vi vill hitta alla hotell som har ett rum med ett nattligt pris på mindre än $ 100 och returnera endast hotellets ID och beskrivning:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "Rooms/any(r: r/BaseRate lt 100)",
        Select = new[] { "HotelId", "Description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Den här frågan `$filter` använder `Rooms/any(r: r/BaseRate lt 100)`ett OData-uttryck för att filtrera dokumenten i indexet. Detta använder [alla operatör](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) för att tillämpa "BaseRate lt 100" på varje objekt i rumssamlingen. Du kan läsa mer om OData-syntaxen som Azure Cognitive Search stöder [här](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Här är resultatet av frågan:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Därefter vill vi hitta de två bästa hotellen som senast renoverats, och visa hotellets namn och sista renoveringsdatum. Här är koden: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "LastRenovationDate desc" },
        Select = new[] { "HotelName", "LastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

I det här fallet använder vi odatasyntaxen igen för att ange parametern `OrderBy` som `lastRenovationDate desc`. Vi satte `Top` också till 2 för att säkerställa att vi bara får de två bästa dokumenten. Som tidigare anger `Select` vi vilka fält som ska returneras.

Här är resultaten:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Slutligen vill vi hitta alla hotellnamn som matchar ordet "hotell":

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Och här är resultaten, som omfattar alla fält `Select` eftersom vi inte angav egenskapen:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Det här steget slutför självstudien, men sluta inte här. **Nästa steg innehåller ytterligare resurser för att lära dig mer om Azure Cognitive Search.

## <a name="next-steps"></a>Nästa steg
* Bläddra i referensinformationen till [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) och [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Granska [namngivningskonventioner](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) för att lära dig reglerna för att namnge olika objekt.
* Granska [datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) i Azure Cognitive Search.

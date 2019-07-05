---
title: Hur du använder Azure Search från .NET-program – Azure Search
description: Lär dig hur du använder Azure Search i en .NET-program med C# och .NET SDK. Kodbaserad kan till exempel ansluta till tjänsten, index-innehåll och fråga ett index.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: brjohnst
ms.openlocfilehash: 9f0af40d442747181636b50612f7d2162ead6a86
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450015"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Hur du använder Azure Search från .NET-program

Den här artikeln är en genomgång för att komma igång med den [Azure Search .NET SDK](https://aka.ms/search-sdk). Du kan använda .NET SDK för att implementera en fullständig sökfunktion i ditt program med Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Nyheter i Azure Sök SDK
SDK består av några klientbibliotek som gör det möjligt att hantera dina index, datakällor, indexerare och synonymen kartor, samt ladda upp och hantera dokument och köra frågor, helt utan att behöva arbeta med information om HTTP och JSON. Dessa klientbibliotek distribueras som NuGet-paket.

Den huvudsakliga NuGet-paketet `Microsoft.Azure.Search`, vilket är ett meta-paket som innehåller alla de andra paketen som beroenden. Använd det här paketet om du precis börjat eller om du känner till ditt program behöver alla funktioner i Azure Search.

Andra NuGet-paketen i SDK är:
 
  - `Microsoft.Azure.Search.Data`: Använd det här paketet om du utvecklar ett .NET-program som använder Azure Search och du behöver bara frågor eller uppdaterar dokument i ditt index. Om du behöver också att skapa eller uppdatera index, synonymmappningar eller andra resurser på tjänstenivå, använda den `Microsoft.Azure.Search` paketet i stället.
  - `Microsoft.Azure.Search.Service`: Använd det här paketet om du utvecklar automatisering i .NET för att hantera Azure Search-index, synonymmappningar, indexerare, datakällor eller andra resurser för servicenivå. Om du bara behöver fråga eller uppdatera dokument i ditt index kan du använda den `Microsoft.Azure.Search.Data` paketet i stället. Om du behöver alla funktioner i Azure Search kan du använda den `Microsoft.Azure.Search` paketet i stället.
  - `Microsoft.Azure.Search.Common`: Vanliga typer som krävs av Azure Search .NET-bibliotek. Du behöver inte använda det här paketet direkt i ditt program. Det är endast avsett att användas som ett beroende.

Olika klientbiblioteken definierar klasser som `Index`, `Field`, och `Document`, samt åtgärder som `Indexes.Create` och `Documents.Search` på den `SearchServiceClient` och `SearchIndexClient` klasser. De här klasserna är ordnade i följande namnrymder:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Om du vill ge feedback för en kommande uppdatering av SDK finns i vår [feedbacksidan](https://feedback.azure.com/forums/263029-azure-search/) eller skapa ett problem på [GitHub](https://github.com/azure/azure-sdk-for-net/issues) och nämner ”Azure Search” i rubriken för problemet.

.NET SDK har stöd för version `2019-05-06` av den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Den här versionen innehåller stöd för [komplexa typer](search-howto-complex-data-types.md), [kognitiv sökning](cognitive-search-concept-intro.md), [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete), och [JsonLines parsningsläge](search-howto-index-json-blobs.md) när Indexera Azure-Blobar. 

Detta SDK stöder inte [hanteringsåtgärder](https://docs.microsoft.com/rest/api/searchmanagement/) , till exempel skapa och skala söktjänster och hantera API-nycklar. Om du vill hantera din Search-resurser från en .NET-program kan du använda den [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Uppgradera till den senaste versionen av SDK: N
Om du redan använder en äldre version av Azure Search .NET SDK och du vill uppgradera till senast allmänt tillgängliga versionen [i den här artikeln](search-dotnet-sdk-migration-version-9.md) förklarar hur.

## <a name="requirements-for-the-sdk"></a>Krav för SDK
1. Visual Studio 2017 eller senare.
2. Din egen Azure Search-tjänst. För att kunna använda SDK: N behöver du namnet på din tjänst och en eller flera API-nycklar. [Skapa en tjänst i portalen](search-create-service-portal.md) hjälpa dig med de här stegen.
3. Ladda ned Azure Search .NET SDK [NuGet-paketet](https://www.nuget.org/packages/Microsoft.Azure.Search) med hjälp av ”hantera NuGet-paket” i Visual Studio. Sök bara efter paketnamnet `Microsoft.Azure.Search` på NuGet.org (eller något av de andra paketera ovanstående namn om du behöver bara en delmängd av funktionerna).

Azure Search .NET SDK har stöd för program som riktar in sig på .NET Framework 4.5.2 och högre som samt .NET Core 2.0 och senare.

## <a name="core-scenarios"></a>Grundläggande scenarier
Det finns flera saker du behöver göra i ditt sökprogram. I den här självstudien får upp vi dessa grundläggande scenarier:

* Skapa ett index
* Fylla i index med dokument
* Söker efter dokument med hjälp av fulltextsökning och filter

Följande exempelkod visar var och en av dessa scenarier. Använd gärna kodfragmenten i ditt eget program.

### <a name="overview"></a>Översikt
Vi kommer att utforska exempelprogrammet skapar en ny index med namnet ”hotels” fylls med några dokument och sedan köra några sökfrågor. Här är huvudsakliga programmet, som visar övergripande flödet:

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

Vi går igenom detta steg för steg. Vi måste först skapa en ny `SearchServiceClient`. Det här objektet kan du hantera index. Du måste ange namnet på din Azure Search-tjänst, samt en administrations-API-nyckel för att konstruera ett. Du kan ange den här informationen i den `appsettings.json` -filen för den [exempelprogrammet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Om du anger en felaktig nyckel (till exempel en frågenyckel där en administratörsnyckel krävdes), den `SearchServiceClient` genereras en `CloudException` med fel meddelandet ”förbjuden” första gången du anropa en åtgärdsmetod, till exempel `Indexes.Create`. Om det händer att du kan kontrollera vår API-nyckel.
> 
> 

De nästa raderna anropa metoder för att skapa ett index med namnet ”hotels” ta bort det först om det finns redan. Vi guidar dig igenom de här metoderna lite senare.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteIndexIfExists(indexName, serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateIndex(indexName, serviceClient);
```

Därefter måste indexet fyllas i. Om du vill Fyll i indexet, måste vi en `SearchIndexClient`. Det finns två sätt att skaffa en: genom att skapa det eller genom att anropa `Indexes.GetClient` på den `SearchServiceClient`. Vi använder det senare i informationssyfte.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
```

> [!NOTE]
> I ett typiskt sökprogram hanteras indexhanteringen och ifyllningen av en separat komponent från sökfrågorna. `Indexes.GetClient` är praktiskt för att fylla ett index eftersom du inte behöver ange ytterligare `SearchCredentials`. Den gör det genom att skicka administratörsnyckeln som du använde för att skapa `SearchServiceClient` till den nya `SearchIndexClient`. Men i delen av ditt program som kör frågor är det bättre att skapa den `SearchIndexClient` direkt så att du kan skicka en frågenyckel som endast kan du läsa data, i stället för en administratörsnyckel. Detta är konsekvent med principen om lägsta behörighet och hjälper till att göra programmet säkrare. Du hittar mer information om administratörsnycklar och frågenycklar [här](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Nu när vi har en `SearchIndexClient`, vi kan fylla i indexet. Index population görs med en annan metod som går igenom senare.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Slutligen kan vi köra några sökfrågor och visa resultatet. Den här gången vi använder en annan `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(indexName, configuration);

RunQueries(indexClientForQueries);
```

Vi tar en närmare titt på den `RunQueries` metoden senare. Här är koden för att skapa den nya `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(string indexName, IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, indexName, new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Den här gången vi använder en frågenyckel eftersom vi inte behöver skrivåtkomst till indexet. Du kan ange den här informationen i den `appsettings.json` -filen för den [exempelprogrammet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Om du kör det här programmet med ett giltigt namn och API-nycklar bör utdata se ut som i följande exempel: (Vissa konsolens utdata har ersatts med ”...” för tydlighetens skull.)

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

Den fullständiga källkoden för programmet finns i slutet av den här artikeln.

Nu ska vi tar en närmare titt på var och en av de metoder som anropas av `Main`.

### <a name="creating-an-index"></a>Skapa ett index
När du har skapat en `SearchServiceClient`, `Main` tar bort indexet ”hotels” om det redan finns. Borttagningen är klar med följande metod:

```csharp
private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists(indexName))
    {
        serviceClient.Indexes.Delete(indexName);
    }
}
```

Den här metoden använder den angivna `SearchServiceClient` för att kontrollera om indexet finns och i så fall tar du bort den.

> [!NOTE]
> För enkelhetens skull används synkrona metoder för Azure Search .NET SDK i exempelkoden i den här artikeln. Vi rekommenderar att du använder asynkrona metoder i dina egna program så att de blir skalbara och responsiva. Till exempel i metoden ovan kunde du använda `ExistsAsync` och `DeleteAsync` i stället för `Exists` och `Delete`.
> 
> 

Nästa `Main` skapar en ny indexet ”hotels” genom att anropa den här metoden:

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

Den här metoden skapar en ny `Index` objekt med en lista över `Field` objekt som definierar schemat för det nya indexet. Varje fält har ett namn, datatyp och flera attribut som definierar dess sökbeteendet. Den `FieldBuilder` klassen använder reflektion för att skapa en lista över `Field` objekt för indexet genom att undersöka allmänna egenskaper och attribut för den angivna `Hotel` modellklass. Tar vi en närmare titt på de `Hotel` klassen vid ett senare tillfälle.

> [!NOTE]
> Du kan alltid skapa listan över `Field` objekt direkt istället för att använda `FieldBuilder` om det behövs. Exempelvis kan du vill inte använda någon modellklass eller du kan behöva använda en befintlig modellklass som du inte vill ändra genom att lägga till attribut.
>
> 

Förutom fält, kan du också lägga till bedömningsprofiler, förslagsställare eller CORS-alternativ till indexet (dessa parametrar har utelämnats från exemplet kortfattat). Du hittar mer information om objektet Index och dess komponenter som ingår i den [SDK-referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index), samt i den [Azure Search REST API-referens](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Fylla i index
Nästa steg `Main` fyller det nyligen skapade indexet. Det här indexet population görs i följande metod: (Kod ersätts med ”...” för tydlighetens skull.  Se den fullständiga exempellösningen för fullständig ifyllning av kod.)

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

Den här metoden har fyra delar. Först skapar en matris med 3 `Hotel` objekt som var och en med 3 `Room` objekt som fungerar som våra inkommande data ska överföras till indexet. Dessa data är hårdkodad för enkelhetens skull. I ditt eget program kommer troligen dina data från en extern datakälla, till exempel en SQL-databas.

Den andra delen skapar en `IndexBatch` som innehåller dokumenten. Du anger den åtgärd som du vill använda i gruppen när du skapar den, i det här fallet genom att anropa `IndexBatch.Upload`. Batch överförs sedan till Azure Search-index med den `Documents.Index` metoden.

> [!NOTE]
> I det här exemplet överför vi bara dokument. Om du vill slå samman ändringar i befintliga dokument eller ta bort dokument kan du skapa batchar genom att anropa `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, eller `IndexBatch.Delete` i stället. Du kan även blanda olika åtgärder i en enda batch genom att anropa `IndexBatch.New`, som tar en samling `IndexAction` objekt, som meddelar Azure Search för att utföra en viss åtgärd i ett dokument. Du kan skapa var och en `IndexAction` med egna åtgärder genom att anropa metoden motsvarande som `IndexAction.Merge`, `IndexAction.Upload`och så vidare.
> 
> 

Den tredje delen av den här metoden är ett catch-block som hanterar en viktig felsituation för indexering. Om Azure Search-tjänsten inte kan indexera vissa av dokumenten i batchen skickas ett `IndexBatchException` av `Documents.Index`. Detta undantag kan inträffa om du indexerar dokument när tjänsten är hårt belastad. **Vi rekommenderar starkt att du uttryckligen hanterar den här situationen i din kod.** Du kan fördröja och sedan försöka indexera dokumentet som misslyckades igen eller så kan du logga och fortsätta som i exemplet, eller göra något annat beroende på programmets krav på datakonsekvens.

> [!NOTE]
> Du kan använda den [ `FindFailedActionsToRetry` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception.findfailedactionstoretry) metod för att skapa en ny grupp som innehåller de instruktioner som inte godkänts i ett tidigare anrop till `Index`. Det finns en beskrivning av hur du använder den korrekt [på StackOverflow](https://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Slutligen den `UploadDocuments` metoden skapar en fördröjning på två sekunder. Indexeringen sker asynkront i Azure Search-tjänsten, så exempelprogrammet måste vänta en kort stund för att kontrollera att dokumenten är tillgängliga för sökning. Fördröjningar som den här är normalt endast nödvändiga i demonstrationer, tester och exempelprogram.

<a name="how-dotnet-handles-documents"></a>

#### <a name="how-the-net-sdk-handles-documents"></a>Hur .NET SDK hanterar dokument
Du kanske undrar hur Azure Search .NET SDK kan ladda upp instanser av en användardefinierad klass som `Hotel` till indexet. För att besvara frågan ska vi titta på den `Hotel` klass:

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

Det första du ser är att namnet på varje offentlig egenskap i den `Hotel` klass mappas till ett fält med samma namn i indexdefinitionen. Om du vill att varje fält för att börja med gemen (”kamelnotation”), du kan uppmana SDK att mappa egenskapsnamnen till kamelnotation automatiskt med den `[SerializePropertyNamesAsCamelCase]` attribut i-klassen. Det här scenariot är vanligt i .NET-program som utför databindning där målschemat ligger utanför programutvecklarens kontrollen utan att behöva bryta mot den ”pascalnotation” namngivning av riktlinjerna i .NET.

> [!NOTE]
> Azure Search .NET SDK använder [NewtonSoft JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm)-biblioteket för att serialisera och deserialisera anpassade modellobjekt till och från JSON. Du kan anpassa den här serialiseringen om det behövs. Mer information finns i [anpassad serialisering med JSON.NET](#JsonDotNet).
> 
> 

Det andra att Observera är varje egenskap är dekorerad med attribut som `IsFilterable`, `IsSearchable`, `Key`, och `Analyzer`. Dessa attribut mappar direkt till den [motsvarande fältattribut i ett Azure Search-index](https://docs.microsoft.com/rest/api/searchservice/create-index#request). Den `FieldBuilder` klassen använder de här egenskaperna för att konstruera fältdefinitioner för indexet.

Det tredje viktigt om den `Hotel` klassen är datatyperna för de offentliga egenskaperna. .NET-typerna för dessa egenskaper mappar till deras motsvarande fälttyper i indexdefinitionen. Exempelvis mappar `Category`-strängegenskapen till `category`-fältet, som är av typen `Edm.String`. Det finns liknande typmappningar mellan `bool?`, `Edm.Boolean`, `DateTimeOffset?`, och `Edm.DateTimeOffset` och så vidare. De specifika reglerna för typmappningen finns dokumenterade med `Documents.Get`-metoden i [Azure Search .NET SDK-referensen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.get). Den `FieldBuilder` klass tar hand om den här mappningen för dig, men det kan fortfarande vara bra att förstå om du behöver felsöka serialiseringsproblem med.

Har du råkar Lägg märke till de `SmokingAllowed` egenskapen?

```csharp
[JsonIgnore]
public bool? SmokingAllowed => (Rooms != null) ? Array.Exists(Rooms, element => element.SmokingAllowed == true) : (bool?)null;
```

Den `JsonIgnore` attributet på den här egenskapen anger att den `FieldBuilder` till inte att serialisera det till indexet som ett fält.  Det här är ett bra sätt att skapa klientsidan beräknade egenskaper som du kan använda som hjälp i ditt program.  I det här fallet den `SmokingAllowed` egenskapen visar om det finns några `Room` i den `Rooms` samlingen kan hälsan.  Om alla är FALSKT, indikerar det att inte tillåter hela hotell hälsan.

Vissa egenskaper som `Address` och `Rooms` är instanser av .NET-klasser.  Dessa egenskaper representerar mer komplexa datastrukturer och därför kräver fält med en [komplex datatyp](https://docs.microsoft.com/azure/search/search-howto-complex-data-types) i indexet.

Den `Address` egenskap representerar en uppsättning med flera värden i den `Address` klassen, som definieras nedan:

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

Den här klassen innehåller de standardvärden som används för att beskriva adresser i USA eller Kanada. Du kan använda typer så här för att gruppera logiska fält i indexet.

Den `Rooms` egenskap representerar en matris med `Room` objekt:

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

Datamodellen i .NET och dess motsvarande indexschema bör utformas för att stödja sökupplevelsen som du vill ge dina slutanvändare. Objekten översta nivån i .NET, d.v.s. dokument i indexet, motsvarar ett sökresultat som du vill presentera i användargränssnittet. Till exempel i ett hotell sökprogram kanske dina slutanvändare vill söker efter hotellnamn, funktionerna i hotellet eller egenskaperna för en viss plats. Vi går igenom några exempel på frågan lite senare.

Den här möjligheten att använda egna klasser för att interagera med dokument i indexet fungerar i båda riktningarna; Du kan också hämta sökresultat och låta SDK deserialisera dem automatiskt till en typ av föredrar, som vi ser i nästa avsnitt.

> [!NOTE]
> Azure Search .NET SDK stöder också dynamiskt typifierade dokument med hjälp av klassen `Document`, som är en nyckel/värde-mappning av fältnamn till fältvärden. Detta är användbart i scenarier då du inte känner till indexeringsschemat redan i designfasen, eller då det skulle vara opraktiskt att binda till specifika modellklasser. Alla metoder i SDK som hanterar dokument har överlagringar som fungerar med klassen `Document`, samt starkt typifierade överlagringar som använder en parameter av generisk typ. Endast de senare används i exempelkoden i den här självstudien. Den [ `Document` klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document) ärver från `Dictionary<string, object>`.
> 
>

**Varför du bör använda datatyper som kan ha värdet null**

När du utformar dina egna modellklasser för mappning till ett Azure Search-index rekommenderar vi att du deklarerar egenskaper för värdetyper som `bool` och `int` så att de kan vara null (t.ex. `bool?` i stället för `bool`). Om du använder en icke-nullbar egenskap måste du **se till** att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller Azure Search-tjänsten hjälper dig med detta.

Detta är inte bara ett hypotetiskt problem: Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan vara null i Azure Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Av den anledningen rekommenderar vi att du använder nullbara typer i dina modellklasser som bästa praxis.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Anpassad serialisering med JSON.NET
SDK: N använder JSON.NET för serialisering och avserialisering av dokument. Du kan anpassa serialisering och deserialiseringsfel om det behövs genom att definiera dina egna `JsonConverter` eller `IContractResolver`. Mer information finns i den [JSON.NET dokumentation](https://www.newtonsoft.com/json/help/html/Introduction.htm). Detta kan vara användbart när du vill anpassa en befintlig modellklass från ditt program för användning med Azure Search och andra mer avancerade scenarier. Med anpassad serialisering kan du till exempel:

* Inkludera eller exkludera vissa egenskaper hos din modellklass lagras som dokumentfält.
* Mappa egenskapsnamn i din kod och fältnamn i ditt index.
* Skapa anpassade attribut som kan användas för att mappa egenskaper till dokumentfält.

Du kan hitta exempel för att implementera anpassad serialisering i enhetstesterna för Azure Search .NET SDK på GitHub. En bra utgångspunkt är [foldern](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Den innehåller klasser som används av anpassad serialisering testerna.

### <a name="searching-for-documents-in-the-index"></a>Söka efter dokument i indexet
Det sista steget i exempelprogrammet är att söka efter vissa dokument i indexet:

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

Varje gång det kör en fråga, den här metoden skapar först en ny `SearchParameters` objekt. Det här objektet används för att ange ytterligare alternativ för frågan, till exempel sortera, filtrera, växling och fasettering. I den här metoden vi håller på att den `Filter`, `Select`, `OrderBy`, och `Top` -egenskapen för olika frågor. Alla de `SearchParameters` egenskaper dokumenteras [här](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Nästa steg är att faktiskt kör sökfrågan. Kör sökningen görs med hjälp av den `Documents.Search` metoden. För varje fråga vi skicka söktexten ska användas som en sträng (eller `"*"` om det finns inga Söktext), plus sökparametrarna skapade tidigare. Vi också ange `Hotel` som typparameter för `Documents.Search`, som talar om för SDK för att deserialisera dokument i sökresultaten till objekt av typen `Hotel`.

> [!NOTE]
> Du hittar mer information om sökning fråga uttryckssyntax [här](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Slutligen, efter varje fråga den här metoden går exempelkoden igenom alla matchningar i sökresultaten varje utskrift till konsolen:

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

Låt oss ta en närmare titt på alla frågor i tur och ordning. Här är koden för att köra den första frågan:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "HotelName" }
    };

results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

I det här fallet vi söker hela indexet för ordet ”motel” i valfritt sökbart fält och vi vill bara hämta namnen hotell, enligt den `Select` parametern. Här följer resultaten:

    Name: Secret Point Motel

    Name: Twin Dome Motel

Nästa fråga är lite mer intressant.  Vi vill hitta alla hotell som har ett rum som varje natt kostar mindre än 100 USD och returnera bara hotell-ID och beskrivning:

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

Den här frågan använder en OData `$filter` uttryck, `Rooms/any(r: r/BaseRate lt 100)`, för att filtrera dokument i indexet. Här används den [varje aktör](https://docs.microsoft.com/azure/search/search-query-odata-collection-operators) att tillämpa den ' BaseRate lt 100' för alla objekt i samlingen rum. Du hittar mer information om OData-syntax som har stöd för Azure Search [här](https://docs.microsoft.com/azure/search/query-odata-filter-orderby-syntax).

Här visas resultatet av frågan:

    HotelId: 1
    Description: The hotel is ideally located on the main commercial artery of the city in the heart of New York...

    HotelId: 2
    Description: The hotel is situated in a nineteenth century plaza, which has been expanded and renovated to...

Nu ska vill vi att hitta de övre två hotell som har varit senast renoverade och visa hotellnamn och datum för senaste renovering. Här är koden: 

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

I det här fallet vi igen använder OData-filtersyntaxen för att ange den `OrderBy` parametern som `lastRenovationDate desc`. Vi också ange `Top` till 2 så vi får endast de två översta-dokument. Som tidigare kan vi ange `Select` att ange vilka fält som ska returneras.

Här följer resultaten:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Slutligen vill vi hitta alla hotels-namn som matchar ordet ”hotell”:

```csharp
parameters = new SearchParameters()
{
    SearchFields = new[] { "HotelName" }
};
results = indexClient.Documents.Search<Hotel>("hotel", parameters);

WriteDocuments(results);
```

Och här är de resultat som innehåller alla fält eftersom vi inte har angett den `Select` egenskapen:

    HotelId: 3
    Name: Triple Landscape Hotel
    ...

Det här steget Slutför självstudien, men avbryta inte här. ** Nästa steg finns ytterligare resurser för att läsa mer om Azure Search.

## <a name="next-steps"></a>Nästa steg
* Bläddra i referensinformationen till [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) och [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Granska [namngivningskonventioner](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) mer regler för namngivning av olika objekt.
* Granska [datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) i Azure Search.

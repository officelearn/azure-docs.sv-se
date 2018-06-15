---
title: Hur du använder Azure Search från ett .NET-program | Microsoft Docs
description: Hur du använder Azure Search från ett .NET-program
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: e8a492a0786281bdc1d7c2123a7188c32a124e13
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
ms.locfileid: "32194132"
---
# <a name="how-to-use-azure-search-from-a-net-application"></a>Hur du använder Azure Search från ett .NET-program
Den här artikeln är en genomgång för att komma igång med den [Azure Search .NET SDK](https://aka.ms/search-sdk). Du kan använda .NET SDK för att implementera en omfattande sökinställningar i ditt program med Azure Search.

## <a name="whats-in-the-azure-search-sdk"></a>Vad är i Azure söka SDK
SDK består av några klientbibliotek som hjälper dig att hantera ditt index, datakällor, indexerare och synonymen mappar samt överföra och hantera dokument och köra frågor, allt utan att behöva hantera information om HTTP- och JSON. Dessa klientbibliotek distribueras som NuGet-paket.

Det huvudsakliga NuGet-paketet är `Microsoft.Azure.Search`, vilket är ett meta-paket som innehåller alla de andra paketen som beroenden. Använd det här paketet om du precis har börjat eller om du känner till ditt program behöver alla funktioner i Azure Search.

Det finns andra NuGet-paketen i SDK:
 
  - `Microsoft.Azure.Search.Data`: Använd det här paketet om du utvecklar ett .NET-program med Azure Search och du behöver bara fråga eller uppdatera dokument i ditt index. Om du också behöva skapa eller uppdatera index synonymen maps och andra resurser för servicenivåer använder den `Microsoft.Azure.Search` paketet i stället.
  - `Microsoft.Azure.Search.Service`: Använd det här paketet om du utvecklar automatisering i .NET för att hantera Azure Search index synonymen maps, indexerare, datakällor eller andra resurser på tjänstnivå. Om du behöver bara fråga eller uppdatering dokument i ditt index, använder du den `Microsoft.Azure.Search.Data` paketet i stället. Om du behöver alla funktioner i Azure Search kan du använda den `Microsoft.Azure.Search` paketet i stället.
  - `Microsoft.Azure.Search.Common`: Vanliga typer som krävs av Azure Search .NET-bibliotek. Du behöver inte använda det här paketet direkt i ditt program. Det är endast avsett att användas som ett beroende.

Olika klientbibliotek definiera klasser som `Index`, `Field`, och `Document`, samt åtgärder som `Indexes.Create` och `Documents.Search` på den `SearchServiceClient` och `SearchIndexClient` klasser. De här klasserna är uppdelade i följande namnområden:

* [Microsoft.Azure.Search](https://docs.microsoft.com/dotnet/api/microsoft.azure.search)
* [Microsoft.Azure.Search.Models](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models)

Den aktuella versionen av Azure Search .NET SDK är nu allmänt tillgänglig. Om du vill ge feedback för att vi ska tas med i nästa version kan du besöka vårt [feedback sidan](https://feedback.azure.com/forums/263029-azure-search/).

.NET SDK stöder version `2017-11-11` av den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). Den här versionen innehåller nu stöd för synonymer samt gradvisa förbättringar till indexerare. Förhandsgranska funktioner som är *inte* en del av den här versionen, till exempel stöd för indexering av JSON-matriser och CSV-filer finns i [preview](search-api-2016-09-01-preview.md) och är tillgängliga via [4.0-förhandsversionen av .NET SDK](https://aka.ms/search-sdk-preview).

Detta SDK stöder inte [hanteringsåtgärder](https://docs.microsoft.com/rest/api/searchmanagement/) , till exempel skapa och skala Search-tjänster och hantera API-nycklar. Om du behöver hantera Sök efter resurser från ett .NET-program kan du använda den [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk).

## <a name="upgrading-to-the-latest-version-of-the-sdk"></a>Uppgradera till den senaste versionen av SDK
Om du redan använder en äldre version av Azure Search .NET SDK och du vill uppgradera till den nya allmänt tillgängliga versionen [i den här artikeln](search-dotnet-sdk-migration-version-5.md) förklarar hur.

## <a name="requirements-for-the-sdk"></a>Krav för SDK
1. Visual Studio 2017.
2. Din egen Azure Search-tjänsten. För att kunna använda SDK, måste namnet på din tjänst och en eller flera API-nycklar. [Skapa en tjänst i portalen](search-create-service-portal.md) hjälper dig genom stegen.
3. Hämta Azure Search .NET SDK [NuGet-paketet](http://www.nuget.org/packages/Microsoft.Azure.Search) med hjälp av ”hantera NuGet-paket” i Visual Studio. Sök bara efter paketnamnet `Microsoft.Azure.Search` på NuGet.org (eller någon av de andra paketet ovanstående namn om du behöver bara en delmängd av funktionerna).

Azure Search .NET SDK stöder tillämpningar för .NET Framework 4.5.2 och högre, samt .NET Core.

## <a name="core-scenarios"></a>Huvudscenarier
Det finns flera saker du behöver göra i sökprogram. I den här självstudiekursen kommer tar vi upp dessa huvudscenarier:

* Skapa ett index
* Fylla index med dokument
* Söker efter dokument med hjälp av fulltextsökning och filter

Exempelkoden nedan illustrerar var och en av dessa. Använd gärna kodavsnitten i ditt eget program.

### <a name="overview"></a>Översikt
Vi kommer att utforska exempelprogrammet skapar en ny index med namnet ”hotell” fylls med några dokument och sedan kör vissa sökfrågor. Här är programmets huvudsakliga visar det allmänna flödet:

```csharp
// This sample shows how to delete, create, upload documents and query an index
static void Main(string[] args)
{
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    Console.WriteLine("{0}", "Deleting index...\n");
    DeleteHotelsIndexIfExists(serviceClient);

    Console.WriteLine("{0}", "Creating index...\n");
    CreateHotelsIndex(serviceClient);

    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

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

Vi går igenom detta steg för steg. Vi måste först skapa en ny `SearchServiceClient`. Det här objektet kan du hantera index. Du måste ange ditt Azure Search-tjänstnamn samt ett administrations-API-nyckel för att kunna skapa en. Du kan ange den här informationen i den `appsettings.json` -filen för den [exempelprogram](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

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
> Om du anger en felaktig nyckel (till exempel en fråga nyckel där en administratör för krävdes), den `SearchServiceClient` genereras en `CloudException` med fel meddelandet ”förbjuden” första gången du anropa en åtgärdsmetod, som `Indexes.Create`. Om detta händer ska du kontrollera våra API-nyckel.
> 
> 

Nästa raderna anropa metoder för att skapa ett index med namnet ”hotell” ta bort den först om det redan finns. Vi kommer att gå igenom dessa metoder lite senare.

```csharp
Console.WriteLine("{0}", "Deleting index...\n");
DeleteHotelsIndexIfExists(serviceClient);

Console.WriteLine("{0}", "Creating index...\n");
CreateHotelsIndex(serviceClient);
```

Därefter måste indexet fyllas i. Detta gör vi behöver en `SearchIndexClient`. Det finns två sätt att skaffa en: genom att skapa den eller genom att anropa `Indexes.GetClient` på den `SearchServiceClient`. Vi använder denna i informationssyfte.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> I ett typiskt sökprogram hanteras indexhanteringen och ifyllningen av en separat komponent från sökfrågorna. `Indexes.GetClient` är praktiskt för att fylla ett index eftersom du inte behöver ange en till `SearchCredentials`. Den gör det genom att skicka administratörsnyckeln som du använde för att skapa `SearchServiceClient` till den nya `SearchIndexClient`. Men i den del av ditt program som kör frågor är det bättre att skapa `SearchIndexClient` direkt så att du kan skicka en frågenyckel i stället för en administratörsnyckel. Detta är konsekventa med principen om minsta behörighet och hjälper till att programmet säkrare. Du hittar mer information om administratör och fråga nycklar [här](https://docs.microsoft.com/rest/api/searchservice/#authentication-and-authorization).
> 
> 

Nu när vi har en `SearchIndexClient`, vi kan fylla i indexet. Detta görs med en annan metod som går igenom senare.

```csharp
Console.WriteLine("{0}", "Uploading documents...\n");
UploadDocuments(indexClient);
```

Slutligen kan vi köra några sökningar och visa resultatet. Den här gången vi använder en annan `SearchIndexClient`:

```csharp
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

RunQueries(indexClientForQueries);
```

Vi ska titta närmare på den `RunQueries` metoden senare. Här är koden för att skapa den nya `SearchIndexClient`:

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string queryApiKey = configuration["SearchServiceQueryApiKey"];

    SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "hotels", new SearchCredentials(queryApiKey));
    return indexClient;
}
```

Nu vi använder en nyckel för frågan eftersom vi inte behöver skrivbehörighet för indexet. Du kan ange den här informationen i den `appsettings.json` -filen för den [exempelprogram](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo).

Om du kör det här programmet med ett giltigt tjänstnamn och API-nycklar utdata ska se ut så här:

    Deleting index...
    
    Creating index...
    
    Uploading documents...
    
    Waiting for documents to be indexed...
    
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
    
    Complete.  Press any key to end application...

Fullständig källkoden för programmet har angetts i slutet av den här artikeln.

Vi ska ta en närmare titt på var och en av metoderna anropas av `Main`.

### <a name="creating-an-index"></a>Skapa ett index
När du har skapat en `SearchServiceClient`, nästa uppgift `Main` har är delete ”hotell” index om den redan finns. Som görs enligt följande metod:

```csharp
private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
{
    if (serviceClient.Indexes.Exists("hotels"))
    {
        serviceClient.Indexes.Delete("hotels");
    }
}
```

Den här metoden använder den angivna `SearchServiceClient` för att kontrollera om indexet finns och i så fall tar du bort den.

> [!NOTE]
> För enkelhetens skull används synkrona metoder för Azure Search .NET SDK i exempelkoden i den här artikeln. Vi rekommenderar att du använder asynkrona metoder i dina egna program så att de blir skalbara och responsiva. Till exempel i metoden ovan kunde du använda `ExistsAsync` och `DeleteAsync` i stället för `Exists` och `Delete`.
> 
> 

Nästa `Main` skapar ett nytt ”hotell” index genom att anropa den här metoden:

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>()
    };

    serviceClient.Indexes.Create(definition);
}
```

Den här metoden skapar en ny `Index` objekt med en lista över `Field` objekt som definierar schemat för det nya indexet. Varje fält har ett namn, datatyp och flera attribut som definierar dess sökbeteendet. Den `FieldBuilder` klassen använder reflektion för att skapa en lista över `Field` objekt för indexet genom att undersöka allmänna egenskaper och attribut för den angivna `Hotel` modellklass. Tar vi en närmare titt på den `Hotel` klassen vid ett senare tillfälle.

> [!NOTE]
> Du kan alltid skapa listan över `Field` objekt direkt i stället för `FieldBuilder` om det behövs. Exempelvis kan du inte vill använda en modellklass eller du kan behöva använda en befintlig modellklass som du inte vill ändra genom att lägga till attribut.
>
> 

Förutom fält, du kan också lägga till bedömningsprofil profiler, suggesters eller CORS alternativ indexet (dessa har uteslutits från exemplet planeringsaspekter). Du hittar mer information om objektet Index och dess komponenter som ingår i den [SDK referens](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index#microsoft_azure_search_models_index), såväl som i den [Azure Search REST API-referensen](https://docs.microsoft.com/rest/api/searchservice/).

### <a name="populating-the-index"></a>Fylla indexet
Nästa steg `Main` är att fylla i det nyligen skapade indexet. Detta görs i följande metod:

```csharp
private static void UploadDocuments(ISearchIndexClient indexClient)
{
    var hotels = new Hotel[]
    {
        new Hotel()
        { 
            HotelId = "1", 
            BaseRate = 199.0, 
            Description = "Best hotel in town",
            DescriptionFr = "Meilleur hôtel en ville",
            HotelName = "Fancy Stay",
            Category = "Luxury", 
            Tags = new[] { "pool", "view", "wifi", "concierge" },
            ParkingIncluded = false, 
            SmokingAllowed = false,
            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
            Rating = 5, 
            Location = GeographyPoint.Create(47.678581, -122.131577)
        },
        new Hotel()
        { 
            HotelId = "2", 
            BaseRate = 79.99,
            Description = "Cheapest hotel in town",
            DescriptionFr = "Hôtel le moins cher en ville",
            HotelName = "Roach Motel",
            Category = "Budget",
            Tags = new[] { "motel", "budget" },
            ParkingIncluded = true,
            SmokingAllowed = true,
            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
            Rating = 1,
            Location = GeographyPoint.Create(49.678581, -122.131577)
        },
        new Hotel() 
        { 
            HotelId = "3", 
            BaseRate = 129.99,
            Description = "Close to town hall and the river"
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

Den här metoden har fyra delar. Först skapar en matris med `Hotel` objekt som ska användas som våra inkommande data att överföra till indexet. Dessa data är hårdkodat för enkelhetens skull. I ditt eget program kommer troligen dina data från en extern datakälla, till exempel en SQL-databas.

Den andra delen skapar en `IndexBatch` som innehåller dokument. Du anger den åtgärd som du vill använda i gruppen när du skapar det, i det här fallet genom att anropa `IndexBatch.Upload`. Batchen överförs sedan till Azure Search-index med den `Documents.Index` metoden.

> [!NOTE]
> I det här exemplet vi bara ladda upp dokument. Om du vill slå samman ändringar i befintliga dokument eller ta bort dokument kan du skapa batchar genom att anropa `IndexBatch.Merge`, `IndexBatch.MergeOrUpload`, eller `IndexBatch.Delete` i stället. Du kan också blanda olika åtgärder i en enda grupp genom att anropa `IndexBatch.New`, som tar en samling `IndexAction` objekt, som talar om Azure-sökning för att utföra en viss åtgärd i ett dokument. Du kan skapa varje `IndexAction` med åtgärden genom att anropa metoden motsvarande som `IndexAction.Merge`, `IndexAction.Upload`och så vidare.
> 
> 

Den tredje delen av den här metoden är ett catch-block som hanterar ett viktigt fel ärende för indexering. Om Azure Search-tjänsten inte kan indexera vissa av dokumenten i batchen skickas ett `IndexBatchException` av `Documents.Index`. Detta kan inträffa om du indexerar dokument när tjänsten är hårt belastad. **Vi rekommenderar starkt att du uttryckligen hanterar den här situationen i din kod.** Du kan fördröja och sedan försöka indexera dokumentet som misslyckades igen eller så kan du logga och fortsätta som i exemplet, eller göra något annat beroende på programmets krav på datakonsekvens.

> [!NOTE]
> Du kan använda den `FindFailedActionsToRetry` metod för att skapa en ny grupp som innehåller de instruktioner som misslyckades under ett tidigare anrop till `Index`. Metoden dokumenteras [här](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexbatchexception#Microsoft_Azure_Search_IndexBatchException_FindFailedActionsToRetry_Microsoft_Azure_Search_Models_IndexBatch_System_String_) och det finns en beskrivning av hur du använder den korrekt [på StackOverflow](http://stackoverflow.com/questions/40012885/azure-search-net-sdk-how-to-use-findfailedactionstoretry).
>
>

Slutligen den `UploadDocuments` metoden fördröjningar i två sekunder. Indexeringen sker asynkront i Azure Search-tjänsten, så exempelprogrammet måste vänta en kort stund för att kontrollera att dokumenten är tillgängliga för sökning. Fördröjningar som den här är normalt endast nödvändiga i demonstrationer, tester och exempelprogram.

#### <a name="how-the-net-sdk-handles-documents"></a>Hur .NET SDK hanterar dokument
Du kanske undrar hur Azure Search .NET SDK kan ladda upp instanser av en användardefinierad klass som `Hotel` till indexet. För att besvara frågan kan vi titta på den `Hotel` klass:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Det första som du bör lägga märke till är att varje offentlig egenskap för `Hotel` motsvarar ett fält i indexdefinitionen, men med en viktig skillnad: namnet på fälten börjar med gemen (”kamelnotation”), men namnet på offentliga egenskaper för `Hotel` börjar med versal (”Pascalnotation”). Det här är ett vanligt scenario i .NET-program som utför databindning där målschemat ligger utanför programutvecklarens kontroll. I stället för att behöva bryta mot riktlinjerna för .NET-namngivning genom att göra egenskapsnamnen gemena kan du uppmana SDK att mappa egenskapsnamnen till kamelnotation automatiskt med attributet `[SerializePropertyNamesAsCamelCase]`.

> [!NOTE]
> Azure Search .NET SDK använder [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)-biblioteket för att serialisera och deserialisera anpassade modellobjekt till och från JSON. Du kan anpassa den här serialiseringen om det behövs. Mer information finns i [anpassad serialisering med JSON.NET](#JsonDotNet).
> 
> 

Den andra praktiskt är attribut som `IsFilterable`, `IsSearchable`, `Key`, och `Analyzer` som skapa snygga varje offentlig egenskap. Dessa attribut mappar direkt till den [motsvarande attribut för Azure Search index](https://docs.microsoft.com/rest/api/searchservice/create-index#request). Den `FieldBuilder` klassen använder dessa för att konstruera fältdefinitioner för indexet.

Det tredje viktigt om den `Hotel` klass är datatyper offentliga egenskaper. .NET-typer av de här egenskaperna mappar till sina motsvarande fälttyp i indexdefinitionen. Exempelvis mappar `Category`-strängegenskapen till `category`-fältet, som är av typen `Edm.String`. Det finns liknande typmappningar mellan `bool?` och `Edm.Boolean`, `DateTimeOffset?` och `Edm.DateTimeOffset` osv. De specifika reglerna för typmappningen finns dokumenterade med `Documents.Get`-metoden i [Azure Search .NET SDK-referensen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_). Den `FieldBuilder` klassen tar hand om mappningen för dig, men det kan fortfarande vara bra att förstå om du behöver felsöka eventuella problem som serialisering.

Den här möjligheten att använda egna klasser som dokument fungerar i båda riktningarna; Du kan också hämta sökresultat och har SDK automatiskt deserialisera dem till en typ av du väljer, som vi kommer att se i nästa avsnitt.

> [!NOTE]
> Azure Search .NET SDK stöder också dynamiskt typifierade dokument med hjälp av klassen `Document`, som är en nyckel/värde-mappning av fältnamn till fältvärden. Detta är användbart i scenarier då du inte känner till indexeringsschemat redan i designfasen, eller då det skulle vara opraktiskt att binda till specifika modellklasser. Alla metoder i SDK som hanterar dokument har överlagringar som fungerar med klassen `Document`, samt starkt typifierade överlagringar som använder en parameter av generisk typ. Endast de senare används i koden i den här självstudiekursen. Den `Document` klassen ärver från `Dictionary<string, object>`. Du kan hitta annan information [här](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.document#microsoft_azure_search_models_document).
> 
> 

**Varför du bör använda datatyper som kan ha värdet null**

När du utformar dina egna modellklasser för mappning till ett Azure Search-index rekommenderar vi att du deklarerar egenskaper för värdetyper som `bool` och `int` så att de kan vara null (t.ex. `bool?` i stället för `bool`). Om du använder en icke-nullbar egenskap måste du **se till** att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller Azure Search-tjänsten hjälper dig med detta.

Detta är inte bara ett hypotetiskt problem. Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `Edm.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan vara null i Azure Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Av den anledningen rekommenderar vi att du använder nullbara typer i dina modellklasser som bästa praxis.

<a name="JsonDotNet"></a>

#### <a name="custom-serialization-with-jsonnet"></a>Anpassad serialisering med JSON.NET
SDK använder JSON.NET för serialisering och avserialisering av dokument. Du kan anpassa serialisering och avserialisering vid behov genom att definiera egna `JsonConverter` eller `IContractResolver` (finns i [JSON.NET dokumentationen](http://www.newtonsoft.com/json/help/html/Introduction.htm) för mer information). Detta kan vara användbart när du vill anpassa en befintlig modellklass från ditt program för användning med Azure Search och andra mer avancerade scenarier. Med anpassad serialisering kan du till exempel:

* Ta med eller undanta vissa egenskaper hos din modellklass lagras som dokumentfält.
* Mappa egenskapsnamn i koden och fältnamnen i ditt index.
* Skapa anpassade attribut som kan användas för att mappa egenskaper till dokumentfält.

Du kan hitta exempel på att implementera anpassad serialisering i enheten tester för Azure Search .NET SDK på GitHub. En bra utgångspunkt är [mappen](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Den innehåller klasser som används av anpassad serialisering testerna.

### <a name="searching-for-documents-in-the-index"></a>Söker efter dokument i index
Det sista steget i exempelprogrammet är att söka efter vissa dokument i indexet. Följande metod gör detta:

```csharp
private static void RunQueries(ISearchIndexClient indexClient)
{
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
}
```

Varje gång det kör en fråga, den här metoden först skapar en ny `SearchParameters` objekt. Används för att ange ytterligare alternativ för frågan som sortering, filtrering, sidindelning och faceting. I den här metoden vi konfigurerar den `Filter`, `Select`, `OrderBy`, och `Top` -egenskapen för olika frågor. Alla de `SearchParameters` egenskaper dokumenteras [här](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters).

Nästa steg är att faktiskt utföra frågan. Detta görs med hjälp av den `Documents.Search` metoden. För varje fråga vi skicka söktexten ska användas som en sträng (eller `"*"` om det finns ingen Söktext), plus sökparametrarna skapade tidigare. Vi även ange `Hotel` som typparameter för `Documents.Search`, som talar om SDK avserialisering av dokument i sökresultaten till objekt av typen `Hotel`.

> [!NOTE]
> Du hittar mer information om sökningen uttryck frågesyntaxen [här](https://docs.microsoft.com/rest/api/searchservice/Simple-query-syntax-in-Azure-Search).
> 
> 

Slutligen efter varje fråga den här metoden går exempelkoden igenom alla matchningar i sökresultaten varje utskrift till konsolen:

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

Låt oss ta en närmare titt på alla frågor i sin tur. Här är koden för att köra den första frågan:

```csharp
parameters =
    new SearchParameters()
    {
        Select = new[] { "hotelName" }
    };

results = indexClient.Documents.Search<Hotel>("budget", parameters);

WriteDocuments(results);
```

I det här fallet vi söker efter hotell som matchar ordet ”budget” och vi vill få tillbaka endast hotell namn, som anges av den `Select` parameter. Här finns resultat:

    Name: Roach Motel

Därefter vill vi hitta hotell som mindre än 150 USD automatiskt varje natt kostar och returnera endast hotell-ID och beskrivning:

```csharp
parameters =
    new SearchParameters()
    {
        Filter = "baseRate lt 150",
        Select = new[] { "hotelId", "description" }
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

Den här frågan använder en OData `$filter` uttryck, `baseRate lt 150`, för att filtrera dokumenten i indexet. Du hittar mer information om OData-syntax som har stöd för Azure Search [här](https://docs.microsoft.com/rest/api/searchservice/OData-Expression-Syntax-for-Azure-Search).

Här är resultatet av frågan:

    ID: 2   Description: Cheapest hotel in town
    ID: 3   Description: Close to town hall and the river

Nu ska vill vi hitta de övre två hotell som har varit senast renoverade och visar hotell namn och datum för senaste renovering. Här är koden: 

```csharp
parameters =
    new SearchParameters()
    {
        OrderBy = new[] { "lastRenovationDate desc" },
        Select = new[] { "hotelName", "lastRenovationDate" },
        Top = 2
    };

results = indexClient.Documents.Search<Hotel>("*", parameters);

WriteDocuments(results);
```

I det här fallet vi igen syntaxen OData för att ange den `OrderBy` parameter som `lastRenovationDate desc`. Vi också ange `Top` till 2 så vi får endast de två översta dokument. Som tidigare kan du ange `Select` att ange vilka fält som ska returneras.

Här finns resultat:

    Name: Fancy Stay        Last renovated on: 6/27/2010 12:00:00 AM +00:00
    Name: Roach Motel       Last renovated on: 4/28/1982 12:00:00 AM +00:00

Slutligen vill vi hitta alla hotell som matchar ordet ”översikt”:

```csharp
parameters = new SearchParameters();
results = indexClient.Documents.Search<Hotel>("motel", parameters);

WriteDocuments(results);
```

Här är de resultat som omfattar alla fält eftersom vi inte har angett den `Select` egenskapen:

    ID: 2   Base rate: 79.99        Description: Cheapest hotel in town     Description (French): Hôtel le moins cher en ville      Name: Roach Motel       Category: Budget        Tags: [motel, budget]   Parking included: yes   Smoking allowed: yes    Last renovated on: 4/28/1982 12:00:00 AM +00:00 Rating: 1/5     Location: Latitude 49.678581, longitude -122.131577

Det här steget Slutför kursen, men stoppar inte här. **Nästa steg** innehåller ytterligare resurser för att lära dig mer om Azure Search.

## <a name="next-steps"></a>Nästa steg
* Bläddra i referensinformationen till [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) och [REST API](https://docs.microsoft.com/rest/api/searchservice/).
* Fördjupa dina kunskaper genom [videor och andra exempel och självstudier](search-video-demo-tutorial-list.md).
* Granska [namngivningskonventioner](https://docs.microsoft.com/rest/api/searchservice/Naming-rules) Läs regler för namngivning av olika objekt.
* Granska [datatyper som stöds](https://docs.microsoft.com/rest/api/searchservice/Supported-data-types) i Azure Search.

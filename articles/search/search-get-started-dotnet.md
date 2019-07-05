---
title: 'C#Snabbstart: Skapa, läsa in och fråga sedan index med .NET SDK - Azure Search'
description: Beskriver hur du skapar ett index, läsa in data och kör frågor med C# och Azure Search .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/20/2019
ms.openlocfilehash: a5cbd2036f92c27709d92d0cf415cc9837645fb8
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67485608"
---
# <a name="quickstart-create-an-azure-search-index-in-c-using-the-net-sdk"></a>Snabbstart: Skapa ett Azure Search-index i C# med .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Skapa en .NET Core C# -konsolapp som skapar, läser in och frågar en Azure Search-index med hjälp av Visual Studio och [Azure Search .NET SDK](https://aka.ms/search-sdk). Den här artikeln beskriver hur du skapar programmet steg för steg. Du kan också [ladda ned och kör hela appen](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

> [!NOTE]
> Demo-koden i den här artikeln används synkrona metoder för Azure Search .NET SDK för enkelhetens skull. Men i produktionsscenarier bör använda asynkrona metoder i dina egna program så att de skalbara och tillgängliga. Du kan till exempel använda `CreateAsync` och `DeleteAsync` i stället för `Create` och `Delete`.

## <a name="prerequisites"></a>Förutsättningar

Följande tjänster, verktyg och data som används i den här snabbstarten. 

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), alla versioner. Exempelkod och instruktioner har testats på den kostnadsfria Community-versionen.

+ Ett exempelindex och dokument tillhandahålls i den här artikeln samt i den [Visual Studio-lösning](https://github.com/Azure-Samples/azure-search-dotnet-samples/quickstart) för den här snabbstarten.

+ [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

   Hämta nyckeln fråga. Det är en bra idé att utfärda frågebegäranden med skrivskyddad åtkomst.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Börja genom att öppna Visual Studio och skapa ett nytt Console App-projekt som kan köras på .NET Core.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

Den [Azure Search .NET SDK](https://aka.ms/search-sdk) består av några klientbibliotek som distribueras som NuGet-paket.

Använda version 9 av för det här projektet i `Microsoft.Azure.Search` NuGet-paketet och senast `Microsoft.Extensions.Configuration.Json` NuGet-paketet.

1. I **verktyg** > **NuGet-Pakethanteraren**väljer **hantera NuGet-paket för lösningen...** . 

1. Klicka på **Browse** (Bläddra).

1. Sök efter `Microsoft.Azure.Search` och välj version 9.0.1 eller senare.

1. Klicka på **installera** till höger för att lägga till sammansättningen i projektet och lösningen.

1. Upprepa detta för `Microsoft.Extensions.Configuration.Json`, om du väljer version 2.2.0 eller senare.


### <a name="add-azure-search-service-information"></a>Lägga till information om Azure Search-tjänsten

1. I Solution Explorer högerklickar du på projektet och välj **Lägg till** > **nytt objekt...**  . 

1. Sök efter ”JSON” att returnera en JSON-relaterade lista över typer av objekt i Lägg till nytt objekt.

1. Välj **JSON-fil**, ge filen namnet ”appsettings.json” och klicka på **Lägg till**. 

1. Lägga till filen på din utdatakatalog. Högerklicka på appsettings.json och välj **egenskaper**. I **kopiera till utdatakatalog**väljer **kopiera om nyare**.

1. Kopiera följande JSON till din nya JSON-fil. Ersätt söktjänstnamnet (din-SEARCH-SERVICE-NAME) och admin API-nyckel (din-ADMIN-API-nyckel) med giltiga värden. Om tjänstens slutpunkt är `https://mydemo.search.windows.net`, tjänstens namn är ”mydemo”.

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Lägg till klass ”. Metoden ”-filer till ditt projekt

När du skriver ut resultat till konsolfönstret, returneras enskilda fält från hotell objekt som strängar. Du kan implementera [toString ()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) utföra denna uppgift kopierar nödvändig kod i till två nya filer.

1. Lägg till två tomma klassdefinitioner i projektet: Address.Methods.cs Hotel.Methods.cs

1. I Address.Methods.cs, skriva standard med följande kod, [linjer 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. I Hotel.Methods.cs, kopiera [linjer 1 66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66).


## <a name="1---create-index"></a>1 – Skapa index

Hotellindexet som består av enkla och komplexa fält, där en enkel fältreferens är ”HotelName” eller ”beskrivning” och komplexa fält är en adress med delfält eller en samling rum. När ett index omfattar komplexa typer, isolera komplexa fältdefinitioner i separata klasser.

1. Lägg till två tomma klassdefinitioner i projektet: Address.CS Hotel.cs

1. I Address.cs, skriver du över standard innehåll med följande kod:

    ```csharp
    using System;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Newtonsoft.Json;

    namespace AzureSearchQuickstart
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

1. I Hotel.cs definierar klassen övergripande strukturen för indexet, inklusive referenser till klassen.

    ```csharp
    namespace AzureSearchQuickstart
    {
        using System;
        using Microsoft.Azure.Search;
        using Microsoft.Azure.Search.Models;
        using Newtonsoft.Json;

        public partial class Hotel
        {
            [System.ComponentModel.DataAnnotations.Key]
            [IsFilterable]
            public string HotelId { get; set; }

            [IsSearchable, IsSortable]
            public string HotelName { get; set; }

            [IsSearchable]
            [Analyzer(AnalyzerName.AsString.EnMicrosoft)]
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

            [IsFilterable, IsSortable, IsFacetable]
            public DateTimeOffset? LastRenovationDate { get; set; }

            [IsFilterable, IsSortable, IsFacetable]
            public double? Rating { get; set; }

            public Address Address { get; set; }
        }
    }
    ```

    Attribut i fältet avgör hur den används i ett program. Till exempel den `IsSearchable` attributet tilldelas till varje fält som ska ingå i en fulltextsökning. Standardvärdet är att inaktivera fält beteenden som inte uttryckligen har aktiverats i .NET-SDK.

    Exakt ett fält i indexet av typen `string` måste vara den *nyckel* fält som unikt identifierar varje dokument. I det här schemat nyckeln är `HotelId`.

    I det här indexet använder fälten Beskrivning egenskapen valfritt analyzer, som du angav när du vill åsidosätta den standard analysverktyget från Lucene. Den `description_fr` fältet använder franska Lucene analysatorn ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) eftersom den innehåller en fransk text. Den `description` använder valfritt Microsoft-språkanalysverktyg ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. I Program.cs, skapa en instans av den [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) klassen för att ansluta till tjänsten med hjälp av värden som lagras i programmets konfigurationsfil (appsettings.json). 

   `SearchServiceClient` har en [ `Indexes` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) egenskapen tillhandahåller alla metoder som du behöver för att skapa, visa, uppdatera eller ta bort Azure Search-index. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program
            // Demonstrates index delete, create, load, and query
            // Commented-out code is uncommented in later steps
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

                // Uncomment next 3 lines in "2 - Load documents"
                // ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
                // Console.WriteLine("{0}", "Uploading documents...\n");
                // UploadDocuments(indexClient);

                // Uncomment next 2 lines in "3 - Search an index"
                // Console.WriteLine("{0}", "Searching index...\n");
                // RunQueries(indexClient);

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            // Create the search service client
            private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
            {
                string searchServiceName = configuration["SearchServiceName"];
                string adminApiKey = configuration["SearchServiceAdminApiKey"];

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
                return serviceClient;
            }

            // Delete an existing index to reuse its name
            private static void DeleteIndexIfExists(string indexName, SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists(indexName))
                {
                    serviceClient.Indexes.Delete(indexName);
                }
            }

            // Create an index whose fields correspond to the properties of the Hotel class.
            // The Address property of Hotel will be modeled as a complex field.
            // The properties of the Address class in turn correspond to sub-fields of the Address complex field.
            // The fields of the index are defined by calling the FieldBuilder.BuildForType() method.
            private static void CreateIndex(string indexName, SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = indexName,
                    Fields = FieldBuilder.BuildForType<Hotel>()
                };

                serviceClient.Indexes.Create(definition);
            }
        }
    }    
    ```

    Om det är möjligt, dela en enda instans av `SearchServiceClient` i din app för att undvika att behöva öppna för många anslutningar. Klassmetoder är trådsäkra för att aktivera typen av delning.

   Klassen har flera konstruktorer. Den som du vill använda har namnet på din söktjänst och ett `SearchCredentials`-objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

    I indexdefinitionen, det enklaste sättet att skapa den `Field` objekt är genom att anropa den `FieldBuilder.BuildForType` metoden och skicka en modellklass för typparametern. En modellklass har egenskaper som mappar till fält i ditt index. Den här mappningen kan du binda dokument från din sökindex till instanser av din modellklass.

    > [!NOTE]
    > Även om du inte planerar att använda någon modellklass kan du definiera ditt index genom att skapa `Field`-objekt direkt. Du kan ge namnet på fältet till konstruktorn, tillsammans med datatypen (eller analysverktyget för strängfält). Du kan också ange andra egenskaper som `IsSearchable`, `IsFilterable`, för att nämna några.
    >

1. Tryck på F5 för att skapa appen och skapa indexet. 

    Om den projektversioner har öppnas ett konsolfönster och skriva statusmeddelanden till skärmen för att ta bort och skapa indexet. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 – läsa in dokument

Dokument är datastrukturer som är både indata till indexering och matar ut från frågor i Azure Search. Som erhålls från en extern datakälla, kanske dokumentet indata rader i en databas, BLOB-objekt i Blob storage eller JSON-dokument på disken. I det här exemplet vi tar en genväg och bädda in JSON-dokument för fyra hotell i själva koden berörs. 

När du laddar upp dokument, måste du använda en [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objekt. En `IndexBatch` innehåller en samling [ `IndexAction` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) objekt, vart och ett innehåller ett dokument och en egenskap som talar om Azure Search vilken åtgärd som ska utföra ([ladda upp, sammanfoga, ta bort och mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. Skapa en matris med dokument och Indexåtgärder i Program.cs, och sedan skicka för matrisen på `IndexBatch`. Dokumenten nedan följer hotell-quickstart-index som definieras av klasserna hotell och adress.

    ```csharp
    // Upload documents as a batch
    private static void UploadDocuments(ISearchIndexClient indexClient)
    {
        var actions = new IndexAction<Hotel>[]
        {
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "1",
                    HotelName = "Secret Point Motel",
                    Description = "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
                    DescriptionFr = "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "air conditioning", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate = new DateTimeOffset(1970, 1, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.6,
                    Address = new Address()
                    {
                        StreetAddress = "677 5th Ave",
                        City = "New York",
                        StateProvince = "NY",
                        PostalCode = "10022",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "2",
                    HotelName = "Twin Dome Motel",
                    Description = "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Boutique",
                    Tags = new[] { "pool", "free wifi", "concierge" },
                    ParkingIncluded = false,
                    LastRenovationDate =  new DateTimeOffset(1979, 2, 18, 0, 0, 0, TimeSpan.Zero),
                    Rating = 3.60,
                    Address = new Address()
                    {
                        StreetAddress = "140 University Town Center Dr",
                        City = "Sarasota",
                        StateProvince = "FL",
                        PostalCode = "34243",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "3",
                    HotelName = "Triple Landscape Hotel",
                    Description = "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
                    DescriptionFr = "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
                    Category = "Resort and Spa",
                    Tags = new[] { "air conditioning", "bar", "continental breakfast" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(2015, 9, 20, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.80,
                    Address = new Address()
                    {
                        StreetAddress = "3393 Peachtree Rd",
                        City = "Atlanta",
                        StateProvince = "GA",
                        PostalCode = "30326",
                        Country = "USA"
                    }
                }
            ),
            IndexAction.Upload(
                new Hotel()
                {
                    HotelId = "4",
                    HotelName = "Sublime Cliff Hotel",
                    Description = "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
                    DescriptionFr = "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
                    Category = "Boutique",
                    Tags = new[] { "concierge", "view", "24-hour front desk service" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1960, 2, 06, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4.6,
                    Address = new Address()
                    {
                        StreetAddress = "7400 San Pedro Ave",
                        City = "San Antonio",
                        StateProvince = "TX",
                        PostalCode = "78216",
                        Country = "USA"
                    }
                }
            ),
        };

        var batch = IndexBatch.New(actions);

        try
        {
            indexClient.Documents.Index(batch);
        }
        catch (IndexBatchException e)
        {
            // When a service is under load, indexing might fail for some documents in the batch. 
            // Depending on your application, you can compensate by delaying and retrying. 
            // For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait 2 seconds before starting queries 
        Console.WriteLine("Waiting for indexing...\n");
        Thread.Sleep(2000);
    }
    ```

    När du initierar den`IndexBatch` objekt kan du skicka det till indexet genom att anropa [ `Documents.Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) på din [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objekt. `Documents` är en egenskap för `SearchIndexClient` som ger metoder för att lägga till, ändra, ta bort eller fråga dokument i indexet.

    Den `try` / `catch` omger anropet till den `Index` metoden fångst indexering fel, vilket kan inträffa om tjänsten är hårt belastad. I produktionskoden kunde du fördröjning och försök sedan att indexera dokumentet som misslyckades, logga in och fortsätta som exemplet gör eller hantera den på något annat sätt som uppfyller ditt programs krav på datakonsekvens.

    2 sekunder fördröjningen kompenserar för indexering, vilket är asynkron så att alla dokument som kan indexeras innan frågorna som körs. Kodning i en fördröjning är normalt endast nödvändiga i demonstrationer, tester och exempelprogram.

1. I Program.cs i huvudsakliga, ta bort kommentarerna raderna för ”2 – Läs in dokument”. 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Tryck på F5 för att återskapa appen. 

    Om projektet skapas har måste öppnas ett konsolfönster och skriva statusmeddelanden, den här gången med ett meddelande om Överför dokument. Azure-portalen i söktjänsten **översikt** sidan hotels-quickstart-index bör nu ha 4 dokument.

Läs mer om Dokumentbearbetning [”hur .NET SDK hanterar dokument”](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).

## <a name="3---search-an-index"></a>3 – Söka i ett index

Du kan hämta frågeresultat när det första dokumentet indexeras, men verkliga tester i ditt index bör vänta tills alla dokument indexeras. 

Det här avsnittet lägger till två delar av funktionerna: frågans logik och resultat. För frågor, använda den [ `Search` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) metod. Den här metoden tar söktext samt annat [parametrar](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

Den [ `DocumentsSearchResult` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) klassen representerar resultaten.


1. I Program.cs, skapar du en WriteDocuments metod att utskrifter sökresultat till konsolen.

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

1. Skapa en RunQueries metod för att köra frågor och returnera resultat. Resultatet är hotell objekt. Du kan använda parametern väljer att visa enskilda fält. Om ett fält inte ingår i parametern väljer, kommer dess motsvarande egenskap för hotell vara null.

    ```csharp
    private static void RunQueries(ISearchIndexClient indexClient)
    {
        SearchParameters parameters;
        DocumentSearchResult<Hotel> results;

        // Query 1 
        Console.WriteLine("Query 1: Search for term 'Atlanta' with no result trimming");
        parameters = new SearchParameters();
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 2
        Console.WriteLine("Query 2: Search on the term 'Atlanta', with trimming");
        Console.WriteLine("Returning only these fields: HotelName, Tags, Address:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Tags", "Address" },
            };
        results = indexClient.Documents.Search<Hotel>("Atlanta", parameters);
        WriteDocuments(results);

        // Query 3
        Console.WriteLine("Query 3: Search for the terms 'restaurant' and 'wifi'");
        Console.WriteLine("Return only these fields: HotelName, Description, and Tags:\n");
        parameters =
            new SearchParameters()
            {
                Select = new[] { "HotelName", "Description", "Tags" }
            };
        results = indexClient.Documents.Search<Hotel>("restaurant, wifi", parameters);
        WriteDocuments(results);

        // Query 4 -filtered query
        Console.WriteLine("Query 4: Filter on ratings greater than 4");
        Console.WriteLine("Returning only these fields: HotelName, Rating:\n");
        parameters =
            new SearchParameters()
            {
                Filter = "Rating gt 4",
                Select = new[] { "HotelName", "Rating" }
            };
        results = indexClient.Documents.Search<Hotel>("*", parameters);
        WriteDocuments(results);

        // Query 5 - top 2 results
        Console.WriteLine("Query 5: Search on term 'boutique'");
        Console.WriteLine("Sort by rating in descending order, taking the top two results");
        Console.WriteLine("Returning only these fields: HotelId, HotelName, Category, Rating:\n");
        parameters =
            new SearchParameters()
            {
                OrderBy = new[] { "Rating desc" },
                Select = new[] { "HotelId", "HotelName", "Category", "Rating" },
                Top = 2
            };
        results = indexClient.Documents.Search<Hotel>("boutique", parameters);
        WriteDocuments(results);
    }
    ```

    Det finns två [sätt med matchande villkor i en fråga](search-query-overview.md#types-of-queries): fulltextsökning och filter. En fullständig textsökning-fråga söker efter en eller flera termer i `IsSearchable` fält i indexet. Ett filter är ett booleskt uttryck som utvärderas `IsFilterable` fält i ett index. Du kan använda fulltextsökning och filter tillsammans eller separat.

    Både sökningar och filtreringar utförs med hjälp av metoden `Documents.Search`. En sökfråga kan skickas i parametern `searchText`, medan ett filteruttryck kan skickas i `Filter`-egenskapen för klassen `SearchParameters`. Om du vill filtrera utan sökning skickar du bara `"*"` för `searchText`-parametern. Om du vill söka utan filtrering lämnar du bara `Filter`-egenskapen odefinierad eller väljer att inte skicka den i en `SearchParameters`-instans över huvud taget.

1. I Program.cs i huvudsakliga, ta bort kommentarerna raderna för ”3 – Sök efter”. 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Lösningen är nu klar. Tryck på F5 för att återskapa appen och kör programmet i sin helhet. 

    Utdata innehåller samma meddelanden som tidigare, med hjälp av läsa information och resultat.

## <a name="clean-up"></a>Rensa

När du är klar med ett index och vill ta bort det anropar den `Indexes.Delete` metoden på din `SearchServiceClient`.

```csharp
serviceClient.Indexes.Delete("hotels");
```

Om du är klar med söktjänsten kan du ta bort resurser från Azure-portalen.

## <a name="next-steps"></a>Nästa steg

I det här C# snabbstarten har du arbetat via en serie aktiviteter för att skapa ett index, läsa in den med dokument och köra frågor. Under de olika faserna tog vi genvägar till förenklar koden för Läs- och förståelse. Om du är nöjd med de grundläggande begreppen, rekommenderar vi nästa artikel för en förklaring av alternativa metoder och koncept som kommer att fördjupa dina kunskaper. 

Exempelkod och index finns expanderade versioner av den här. I nästa exempel lägger till en samling rum, använder olika klasser och åtgärder och tar en närmare titt på hur bearbetningen fungerar.

> [!div class="nextstepaction"]
> [Hur du utvecklar i .NET](search-howto-dotnet-sdk.md)
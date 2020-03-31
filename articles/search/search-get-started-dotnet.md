---
title: 'Snabbstart: Skapa ett sökindex i C# med .NET'
titleSuffix: Azure Cognitive Search
description: I den här snabbstarten C# lär du dig hur du skapar ett index, läser in data och kör frågor med Azure Cognitive Search .NET SDK.
manager: nitinme
author: tchristiani
ms.author: terrychr
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/10/2020
ms.openlocfilehash: 3d0006a3c77050c1bb21a0da8d6be51e659f933d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77589223"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-c-using-the-net-sdk"></a>Snabbstart: Skapa ett Azure Cognitive Search-index i C# med .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-get-started-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [Powershell](search-create-index-rest-api.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)
>*

Skapa ett .NET Core C#-konsolprogram som skapar, läser in och frågar efter ett Azure Cognitive Search-index med Visual Studio och [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). I den här artikeln beskrivs hur du skapar programmet steg för steg. Alternativt kan du [ladda ner och köra hela programmet](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/Quickstart).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

> [!NOTE]
> Demokoden i den här artikeln använder synkronmetoderna i Azure Cognitive Search .NET SDK för enkelhetens skull. För produktionsscenarier rekommenderar vi dock att du använder asynkrona metoder i dina egna program för att hålla dem skalbara och responsiva. Du kan till `CreateAsync` exempel `DeleteAsync` använda `Create` `Delete`och i stället för och .

## <a name="prerequisites"></a>Krav

Följande tjänster och verktyg krävs för den här snabbstarten.

+ [Visual Studio](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst. Exempelkod och instruktioner testades på den kostnadsfria gemenskapsutgåvan.

+ [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Hämta en nyckel och webbadress

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomstnyckel på varje begäran. En söktjänst skapas med båda, så om du har lagt till Azure Cognitive Search i din prenumeration följer du dessa steg för att få nödvändig information:

1. [Logga in på Azure-portalen](https://portal.azure.com/)och hämta webbadressen i söktjänstens **översiktssida.** Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **Inställningsnycklar** > **Keys**får du en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara admin nycklar, som tillhandahålls för kontinuitet i verksamheten om du behöver rulla en över. Du kan använda antingen primär- eller sekundärnyckeln på begäranden om att lägga till, ändra och ta bort objekt.

   Hämta frågenyckeln också. Det är en bra idé att utfärda frågebegäranden med skrivskyddad åtkomst.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-get-started-postman/get-url-key.png "Hämta en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden kräver en api-nyckel på varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="set-up-your-environment"></a>Konfigurera din miljö

Börja med att öppna Visual Studio och skapa ett nytt Console App-projekt som kan köras på .NET Core.

### <a name="install-nuget-packages"></a>Installera NuGet-paket

[Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk) består av några klientbibliotek som distribueras som NuGet-paket.

För det här projektet använder `Microsoft.Azure.Search` du version 9 `Microsoft.Extensions.Configuration.Json` av NuGet-paketet och det senaste NuGet-paketet.

1. I **Tools** > **NuGet Package Manager**väljer du Hantera **NuGet-paket för lösning...**. 

1. Klicka på **Browse** (Bläddra).

1. Sök `Microsoft.Azure.Search` efter och välj version 9.0.1 eller senare.

1. Klicka på **Installera** till höger om du vill lägga till sammansättningen i projektet och lösningen.

1. Upprepa `Microsoft.Extensions.Configuration.Json`för , välja version 2.2.0 eller senare.


### <a name="add-azure-cognitive-search-service-information"></a>Lägga till Azure Cognitive Search-tjänstinformation

1. Högerklicka på projektet i Solution Explorer och välj **Lägg till** > **nytt objekt...** . 

1. Sök efter "JSON" i Lägg till nytt objekt för att returnera en JSON-relaterad lista över objekttyper.

1. Välj **JSON-fil**, namnge filen "appsettings.json" och klicka på **Lägg till**. 

1. Lägg till filen i utdatakatalogen. Högerklicka på appsettings.json och välj **Egenskaper**. Välj Kopiera om du vill göra **det nyare**i **Katalogen Kopiera till utdata**.

1. Kopiera följande JSON till din nya JSON-fil. Ersätt söktjänstnamnet (YOUR-SEARCH-SERVICE-NAME) och admin API-nyckeln (YOUR-ADMIN-API-KEY) med giltiga värden. Om tjänstslutpunkten `https://mydemo.search.windows.net`är, skulle tjänstnamnet vara "mydemo".

```json
{
  "SearchServiceName": "<YOUR-SEARCH-SERVICE-NAME>",
  "SearchServiceAdminApiKey": "<YOUR-ADMIN-API-KEY>",
  "SearchIndexName": "hotels-quickstart"
}
```

### <a name="add-class-method-files-to-your-project"></a>Lägg till klass ". Metodfiler till projektet

När du skriver ut resultat i konsolfönstret måste enskilda fält från Hotellobjektet returneras som strängar. Du kan implementera [ToString()](https://docs.microsoft.com/dotnet/api/system.object.tostring?view=netframework-4.8) för att utföra den här uppgiften genom att kopiera den kod som krävs till två nya filer.

1. Lägg till två tomma klassdefinitioner i projektet: Address.Methods.cs Hotel.Methods.cs

1. I Address.Methods.cs skriver du över standardinnehållet med följande kod, [raderna 1-32](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Address.Methods.cs/#L1-L32).

1. Kopiera [rader 1-66](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/Quickstart/AzureSearchQuickstart/Hotel.Methods.cs/#L1-L66)i Hotel.Methods.cs .


## <a name="1---create-index"></a>1 - Skapa index

Hotellindexet består av enkla och komplexa fält, där ett enkelt fält är "HotelName" eller "Beskrivning", och komplexa fält är en adress med underfält, eller en samling rum. När ett index innehåller komplexa typer isolerar du komplexa fältdefinitioner i separata klasser.

1. Lägg till två tomma klassdefinitioner i projektet: Address.cs Hotel.cs

1. I Address.cs skriver du över standardinnehållet med följande kod:

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

1. I Hotel.cs definierar klassen indexets övergripande struktur, inklusive referenser till adressklassen.

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

    Attribut i fältet avgör hur det används i ett program. `IsSearchable` Attributet måste till exempel tilldelas varje fält som ska inkluderas i en fulltextsökning. 
    
    > [!NOTE]
    > I .NET SDK måste fält uttryckligen [`IsSearchable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issearchable?view=azure-dotnet) [`IsFilterable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfilterable?view=azure-dotnet)tilldelas [`IsSortable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.issortable?view=azure-dotnet)som [`IsFacetable`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.isfacetable?view=azure-dotnet), , och . Det här beteendet står i kontrast till REST API som implicit aktiverar attribution baserat på datatyp (till exempel är enkla strängfält automatiskt sökbara).

    Exakt ett fält i `string` ditt textindex måste vara *nyckelfältet* och unikt identifiera varje dokument. I det här schemat `HotelId`är nyckeln .

    I det här indexet använder [`analyzer`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.analyzer?view=azure-dotnet) beskrivningsfälten den valfria egenskapen, som anges när du vill åsidosätta standardstandarden Lucene-analysator. Fältet `description_fr` använder den franska Lucene-analysatorn ([FrLucene](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.frlucene?view=azure-dotnet)) eftersom den lagrar fransk text. Den `description` använder den valfria Microsoft språkanalysator ([EnMicrosoft](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername.enmicrosoft?view=azure-dotnet)).

1. I Program.cs skapar du en förekomst av [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) klassen för att ansluta till tjänsten med hjälp av värden som lagras i programmets config-fil (appsettings.json). 

   `SearchServiceClient`har [`Indexes`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexes?view=azure-dotnet) en egenskap som anger alla metoder som du behöver för att skapa, lista, uppdatera eller ta bort Azure Cognitive Search-index. 

    ```csharp
    using System;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Extensions.Configuration;

    namespace AzureSearchQuickstart
    {
        class Program {
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

    Om möjligt delar du `SearchServiceClient` en enda instans av i ditt program för att undvika att öppna för många anslutningar. Klassmetoder är trådsäkra för att möjliggöra sådan delning.

   Klassen har flera konstruktörer. Den som du vill använda har namnet på din söktjänst och ett `SearchCredentials`-objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

    I indexdefinitionen är det enklaste sättet att `FieldBuilder.BuildForType` skapa objekten `Field` genom att anropa metoden, som skickar en modellklass för typparametern. En modellklass har egenskaper som mappar till fält i ditt index. Med den här mappningen kan du binda dokument från sökindexet till instanser av modellklassen.

    > [!NOTE]
    > Även om du inte planerar att använda någon modellklass kan du definiera ditt index genom att skapa `Field`-objekt direkt. Du kan ge namnet på fältet till konstruktorn, tillsammans med datatypen (eller analysverktyget för strängfält). Du kan också ange `IsSearchable` `IsFilterable`andra egenskaper som , , för att nämna några.
    >

1. Tryck på F5 för att skapa appen och skapa indexet. 

    Om projektet har byggts upp öppnas ett konsolfönster och statusmeddelanden skrivs till skärmen för att ta bort och skapa indexet. 

<a name="load-documents"></a>

## <a name="2---load-documents"></a>2 - Ladda dokument

I Azure Cognitive Search är dokument datastrukturer som både är indata till indexering och utdata från frågor. Som erhållits från en extern datakälla kan dokumentindata vara rader i en databas, blobbar i Blob-lagring eller JSON-dokument på disken. I det här exemplet tar vi en genväg och bäddar in JSON-dokument för fyra hotell i själva koden. 

När du överför dokument måste [`IndexBatch`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) du använda ett objekt. En `IndexBatch` innehåller en [`IndexAction`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexaction?view=azure-dotnet) samling objekt, som var och en innehåller ett dokument och en egenskap som talar om för Azure Cognitive Search vilken åtgärd som ska utföras[(ladda upp, sammanfoga, ta bort och mergeOrUpload](search-what-is-data-import.md#indexing-actions)).

1. I Program.cs skapar du en matris med dokument och indexåtgärder `IndexBatch`och skickar sedan matrisen till . Dokumenten nedan överensstämmer med hotel-quickstart-indexet, enligt definitionen i hotell- och adressklasserna.

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

    När du har`IndexBatch` initierat objektet kan du skicka [`Documents.Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.index?view=azure-dotnet) det [`SearchIndexClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) till indexet genom att anropa objektet. `Documents`är en `SearchIndexClient` egenskap som innehåller metoder för att lägga till, ändra, ta bort eller fråga dokument i indexet.

    `try` / `catch` Omgivande samtalet till `Index` metoden fångar indexeringsfel, vilket kan inträffa om din tjänst är under tung belastning. I produktionskoden kan du fördröja och sedan försöka indexera dokumenten som misslyckades, eller logga och fortsätta som exemplet gör, eller hantera det på något annat sätt som uppfyller programmets datakonsekvenskrav.

    Fördröjningen på två sekunder kompenserar för indexeringen, vilket är asynkront, så att alla dokument kan indexeras innan frågorna körs. Kodning i en fördröjning är vanligtvis bara nödvändigt i demonstrationer, tester och exempelprogram.

1. I Program.cs, i huvudsak, ta av raderna för "2 - Ladda dokument". 

    ```csharp
    // Uncomment next 3 lines in "2 - Load documents"
    ISearchIndexClient indexClient = serviceClient.Indexes.GetClient(indexName);
    Console.WriteLine("{0}", "Uploading documents...\n");
    UploadDocuments(indexClient);
    ```
1. Tryck på F5 för att återskapa appen. 

    Om projektet har byggts upp öppnas ett konsolfönster och skriver statusmeddelanden den här gången med ett meddelande om att ladda upp dokument. På sidan Azure-portalen bör hotell-quickstart-indexet nu ha 4 dokument i sidan **Översikt** för söktjänsten.

Mer information om dokumentbearbetning finns i ["Så här hanterar .NET SDK dokument".](search-howto-dotnet-sdk.md#how-dotnet-handles-documents)

## <a name="3---search-an-index"></a>3 – Söka i ett index

Du kan få frågeresultat så fort det första dokumentet har indexerats, men den faktiska testningen av indexet bör vänta tills alla dokument indexeras. 

Det här avsnittet lägger till två funktioner: frågelogik och resultat. Använd metoden för [`Search`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.search?view=azure-dotnet
) frågor. Den här metoden tar söktext samt andra [parametrar](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters?view=azure-dotnet). 

Klassen [`DocumentsSearchResult`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.documentsearchresult-1?view=azure-dotnet) representerar resultaten.


1. I Program.cs skapar du en WriteDocuments-metod som skriver ut sökresultat till konsolen.

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

1. Skapa en RunQueries-metod för att köra frågor och returnera resultat. Resultaten är Hotellobjekt. Du kan använda urvalsparametern för att visa enskilda fält. Om ett fält inte ingår i urvalsparametern kommer motsvarande hotellegendom att vara null.

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

    Det finns två [sätt att matcha termer i en fråga:](search-query-overview.md#types-of-queries)fulltextsökning och filter. En fulltextsökfråga söker efter ett `IsSearchable` eller flera termer i fälten i indexet. Ett filter är ett booleskt `IsFilterable` uttryck som utvärderas över fält i ett index. Du kan använda fulltextsökning och filter tillsammans eller separat.

    Både sökningar och filtreringar utförs med hjälp av metoden `Documents.Search`. En sökfråga kan skickas i parametern `searchText`, medan ett filteruttryck kan skickas i `Filter`-egenskapen för klassen `SearchParameters`. Om du vill filtrera utan sökning skickar du bara `"*"` för `searchText`-parametern. Om du vill söka utan filtrering lämnar du bara `Filter`-egenskapen odefinierad eller väljer att inte skicka den i en `SearchParameters`-instans över huvud taget.

1. I Program.cs, i huvudsak, avkommentera raderna för "3 - Sök". 

    ```csharp
    // Uncomment next 2 lines in "3 - Search an index"
    Console.WriteLine("{0}", "Searching documents...\n");
    RunQueries(indexClient);
    ```
1. Lösningen är nu klar. Tryck på F5 för att bygga om appen och köra programmet i sin helhet. 

    Utdata innehåller samma meddelanden som tidigare, med tillägg av frågeinformation och resultat.

## <a name="clean-up-resources"></a>Rensa resurser

När du arbetar i din egen prenumeration kan det dock vara klokt att i slutet av ett projekt kontrollera om du fortfarande behöver de resurser som du skapade. Resurser som fortsätter att köras kan medföra kostnader. Du kan ta bort enstaka resurser eller ta bort hela resursuppsättningen genom att ta bort resursgruppen.

Du kan hitta och hantera resurser i portalen med hjälp av länken **Alla resurser** eller **Resursgrupper** i fönstret för vänsternavigering.

Om du använder en kostnadsfri tjänst bör du komma ihåg att du är begränsad till tre index, indexerare och datakällor. Du kan ta bort enskilda objekt i portalen för att hålla dig under gränsen. 

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten C# har du arbetat igenom en serie aktiviteter för att skapa ett index, läsa in det med dokument och köra frågor. I olika skeden tog vi genvägar för att förenkla koden för läsbarhet och förståelse. Om du är bekväm med de grundläggande begreppen, rekommenderar vi nästa artikel för en utforskning av alternativa metoder och begrepp som kommer att fördjupa dina kunskaper. 

Exempelkoden och indexet expanderas versioner av den här. Nästa exempel lägger till en rumssamling, använder olika klasser och åtgärder och tar en närmare titt på hur bearbetning fungerar.

> [!div class="nextstepaction"]
> [Hur man utvecklar i .NET](search-howto-dotnet-sdk.md)

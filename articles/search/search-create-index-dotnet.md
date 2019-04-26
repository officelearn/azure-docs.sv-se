---
title: 'Snabbstart: Skapa ett index i en C# konsolapp – Azure Search'
description: Lär dig hur du skapar ett sökbart fulltextindex i C# med hjälp av Azure Search .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/08/2019
ms.openlocfilehash: 83842893e0ffc6bb954832cd65b6312b59bbcaa3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516562"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Snabbstart: 1 – skapa ett Azure Search-index iC#
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Postman](search-fiddler.md)
>*

Den här artikeln vägleder dig genom processen för att skapa [ett Azure Search-index](search-what-is-an-index.md) med C# och [.NET SDK](https://aka.ms/search-sdk). Det här är den första lektionen i en 3 delar Övning för att skapa, läsa in och fråga ett index. Skapandet av index åstadkoms genom att utföra dessa uppgifter:

> [!div class="checklist"]
> * Skapa en [ `SearchServiceClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) objekt för att ansluta till en söktjänst.
> * Skapa en [ `Index` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) objekt ska skickas som en parameter till `Indexes.Create`.
> * Anropa den `Indexes.Create` metoden på `SearchServiceClient` att skicka den `Index` till en tjänst.

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande tjänster, verktyg och data som används i den här snabbstarten. 

[Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst för den här snabbstarten.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), alla versioner. Exempelkod och instruktioner har testats på den kostnadsfria Community-versionen.

[DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) ger exempellösningen är en .NET Core-konsolprogram som skrivits C#, som finns i GitHub-lagringsplatsen Azure-exempel. Hämta och extrahera lösningen. Som standard är lösningar skrivskyddad. Högerklicka på lösningen och ta bort attributet skrivskyddad så att du kan ändra filer. Data som ingår i lösningen.

## <a name="get-a-key-and-url"></a>Hämta en nyckel och URL: en

Anrop till tjänsten kräver en URL-slutpunkt och en åtkomstnyckel för varje begäran. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

1. [Logga in på Azure-portalen](https://portal.azure.com/), och i din söktjänst **översikt** sidan, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://mydemo.search.windows.net`.

2. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln för förfrågningar för att lägga till, ändra och ta bort objekt.

![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="1---configure-and-build"></a>1 – konfigurera och skapa

1. Öppna den **DotNetHowTo.sln** filen i Visual Studio.

1. I appsettings.json, ersätter du standard innehåll med exemplet nedan och ange sedan namnet på tjänsten och admin api-nyckel för din tjänst. 


   ```json
   {
       "SearchServiceName": "Put your search service name here (not the full URL)",
       "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
    }
   ```

  För tjänstnamnet behöver du bara namnet i sig. Exempel: om din URL är https://mydemo.search.windows.net, lägga till `mydemo` till JSON-fil.

1. Tryck på F5 för att skapa lösningen och kör konsolappen. De återstående stegen i den här övningen och de som följer är en förklaring av hur den här koden fungerar. 

Du kan också referera till [hur du använder Azure Search från .NET-program](search-howto-dotnet-sdk.md) mer detaljerad täckning av SDK-beteenden. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 – Skapa en klient

Om du vill börja använda Azure Search .NET SDK genom att skapa en instans av den `SearchServiceClient` klass. Den här klassen har flera konstruktorer. Den som du vill använda har namnet på din söktjänst och ett `SearchCredentials`-objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

Följande kod finns i filen Program.cs. Den skapar en ny `SearchServiceClient` med värdena för söktjänstens namn och api-nyckel som lagras i programmets konfigurationsfil (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` har en `Indexes`-egenskap. Den här egenskapen tillhandahåller alla metoder som du behöver för att skapa, visa, uppdatera eller ta bort Azure Search-index.

> [!NOTE]
> `SearchServiceClient`-klassen hanterar anslutningar till din söktjänst. För att undvika att behöva öppna för många anslutningar bör du försöka dela en enskild instans av `SearchServiceClient` i ditt program om möjligt. Dess metoder är trådsäkra för att möjliggöra den typen av delning.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 – skapa Index
Ett enda anrop till den `Indexes.Create` metoden skapar ett index. Den här metoden tar som en parameter ett `Index` objekt som definierar ett Azure Search-index. Skapa en `Index` objekt och initiera det på följande sätt:

1. Ange `Name`-egenskapen för `Index`-objektet till namnet på ditt index.

2. Ange `Fields`-egenskapen för `Index`-objektet till en matris med `Field`-objekt. Det enklaste sättet att skapa `Field`-objekten på är att anropa `FieldBuilder.BuildForType`-metoden och skicka en modellklass för typparametern. En modellklass har egenskaper som mappar till fält i ditt index. På så sätt kan du binda dokument från din sökindex till instanser av din modellklass.

> [!NOTE]
> Även om du inte planerar att använda någon modellklass kan du definiera ditt index genom att skapa `Field`-objekt direkt. Du kan ge namnet på fältet till konstruktorn, tillsammans med datatypen (eller analysverktyget för strängfält). Du kan också ange andra egenskaper som `IsSearchable`, `IsFilterable`, för att nämna några.
>
>

Det är viktigt att du behåller dina användarupplevelsen och affärsbehov i åtanke när du utformar ditt index. Varje fält måste tilldelas den [attribut](https://docs.microsoft.com/rest/api/searchservice/Create-Index) som styr vilka sökfunktioner (filtrering, aspekter, sortering och så vidare) som tillämpas på vilka fält. För egenskaper som du inte uttryckligen anger inaktiverar klassen  `Field` som standard motsvarande sökfunktion såvida du inte uttryckligen aktiverar den.

I det här exemplet Indexnamnet är ”hotels” och fält definieras med en modellklass. Varje egenskap i modellklassen har attribut som avgör motsvarande indexfälts sökrelaterade beteende. Modellklassen definieras så här:

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

Vi har noga valt attributen för varje egenskap baserat på hur vi tror att de kommer att användas i ett program. Eftersom det t.ex. är troligt att personer som söker efter hotell är intresserade av nyckelordsmatchningar i `description`-fältet har vi aktiverat fulltextsökning för det fältet genom att lägga till attributet `IsSearchable` till egenskapen `Description`.

Observera att exakt ett fält i indexet av typen `string` måste definieras som *nyckelfält*. Det gör du genom att lägga till attributet `Key` till (se `HotelId` i ovanstående exempel).

Indexdefinitionen ovan använder ett språkanalysverktyg för `description_fr`-fältet eftersom det ska lagra fransk text. Mer information finns i [lägga till språkanalysverktyg till ett Azure Search-index](index-add-language-analyzers.md).

> [!NOTE]
> Som standard motsvarar namn för varje egenskap i din modellklass fältnamnet i indexet. Om du vill mappa alla egenskapsnamn till kamelnotationsfältnamn, så markerar du klassen med attributet `SerializePropertyNamesAsCamelCase`. Om du vill att mappa till ett annat namn, du kan använda `JsonProperty`-attributet som egenskapen `DescriptionFr` ovan. Attributet `JsonProperty` har företräde framför attributet `SerializePropertyNamesAsCamelCase`.
> 
> 

Nu när vi har definierat en modellklass kan vi lätt skapa en indexdefinition:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 – anropa Indexes.Create
Nu när du har ett initierat `Index` objekt, skapa indexet genom att anropa `Indexes.Create` på din `SearchServiceClient` objekt:

```csharp
serviceClient.Indexes.Create(definition);
```

Om begäran lyckas returnerar metoden som vanligt. Om det uppstår ett problem med begäran, till exempel på grund av en ogiltig parameter, returnerar metoden `CloudException`.

När du är klar med ett index och vill ta bort det anropar den `Indexes.Delete` metoden på din `SearchServiceClient`. Exempel:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> För enkelhetens skull används synkrona metoder för Azure Search .NET SDK i exempelkoden i den här artikeln. Vi rekommenderar att du använder asynkrona metoder i dina egna program så att de blir skalbara och responsiva. I exemplen ovan kan du till exempel använda `CreateAsync` och `DeleteAsync` i stället för `Create` och `Delete`.
> 
> 

## <a name="next-steps"></a>Nästa steg
I den här snabbstarten har skapat du en tom Azure Search-index baserat på ett schema som definierar datatyper och beteenden. Indexet är ett ”utan ben” index som består av ett namn och en samling av attributet fält. En mer realistisk indexet skulle innehålla andra element, till exempel [poängprofiler](index-add-scoring-profiles.md), [förslagsställare](index-add-suggesters.md) typeahead support, [synonymer](search-synonyms.md), och eventuellt [ anpassade analysverktyg](index-add-custom-analyzers.md). Vi rekommenderar att du besöker dessa funktioner när du förstår det grundläggande arbetsflödet.

Nästa Snabbstart i den här serien beskriver hur du läser in indexet med sökbart innehåll.

> [!div class="nextstepaction"]
> [Läsa in data till ett Azure Search index med hjälp avC#](search-import-data-dotnet.md)
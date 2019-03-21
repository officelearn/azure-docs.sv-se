---
title: Skapa ett index i kod med hjälp av .NET API – Azure Search
description: Lär dig hur du skapar ett sökbart fulltextindex med hjälp av Azure Search .NET SDK och exempelkoden C#.
author: brjohnstmsft
manager: jlembicz
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/14/2019
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: e0aa7dce0b3cc4609a995097d8e70f8ec4336809
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58226376"
---
# <a name="quickstart-create-load-and-query-an-azure-search-index-using-the-net-sdk"></a>Snabbstart: Skapa, läsa in och fråga en Azure Search-index med .NET SDK
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [PowerShell (REST)](search-create-index-rest-api.md)
> * [Postman (REST)](search-fiddler.md)
> * [Portal](search-create-index-portal.md)
> 

Den här artikeln vägleder dig genom processen att skapa, läsa in och fråga ett Azure Search [index](search-what-is-an-index.md) med hjälp av den [Azure Search .NET SDK](https://aka.ms/search-sdk).

> [!NOTE]
> All exempelkod i den här artikeln är skriven i C#. Du hittar den fullständiga källkoden [på GitHub](https://aka.ms/search-dotnet-howto). Du kan läsa om [Azure Search .NET SDK](search-howto-dotnet-sdk.md) och få en mer detaljerad genomgång av exempelkoden.

## <a name="prerequisites"></a>Förutsättningar

+ [Skapa en Azure Search-tjänst](search-create-service-portal.md). Du kan använda en kostnadsfri tjänst för den här snabbstarten.

+ [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), alla versioner. Exempelkod och instruktioner har testats på den kostnadsfria Community-versionen.

+ URL-slutpunkt och admin api-nyckeln för Search-tjänsten. En söktjänst har vanligen båda dessa komponenter, så om du har valt att lägga till Azure Search i din prenumeration följer du bara stegen nedan för att hitta fram till rätt information:

  1. I Azure-portalen [hitta din tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) i tjänstelistan.

  2. I **översikt**, hämta URL: en. Här följer ett exempel på hur en slutpunkt kan se ut: `https://my-service-name.search.windows.net`.

  3. I **inställningar** > **nycklar**, hämta en administratörsnyckel för fullständiga rättigheter på tjänsten. Det finns två utbytbara administratörsnycklar, som angetts för kontinuitet för företag om du behöver förnya ett. Du kan använda antingen den primära eller sekundära nyckeln på din begäran.

     ![Hämta en HTTP-slutpunkt och åtkomstnyckel](media/search-fiddler/get-url-key.png "får en HTTP-slutpunkt och åtkomstnyckel")

     Alla begäranden som kräver en api-nyckel för varje begäran som skickas till din tjänst. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

## <a name="1---create-a-new-project"></a>1 – skapa ett nytt projekt

Skapa en ny visualisering i Visual Studio C# projekt. En bra mall för den här snabbstarten är Visual C# > Kom igång > Web App. Den här mallen får du en fil i appsettings.json.  

I appsettings.json, ersätter du standard innehåll med exemplet nedan och ange sedan namnet på tjänsten och admin api-nyckel för din tjänst. För tjänstnamnet behöver du bara namnet i sig. Exempel: om din URL är https://mydemo.search.windows.net, lägga till `mydemo` till JSON-fil.


```json
{
    "SearchServiceName": "Put your search service name here",
    "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
}
```

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-an-instance-of-the-searchserviceclient-class"></a>2 – Skapa en instans av SearchServiceClient-klassen
Innan du kan börja använda .NET SDK Azure Search måste du skapa en instans av klassen `SearchServiceClient`. Den här klassen har flera konstruktorer. Den som du vill använda har namnet på din söktjänst och ett `SearchCredentials`-objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

Kopiera följande kod till filen Program.cs. Koden nedan skapar en ny `SearchServiceClient` med värdena för söktjänstens namn och api-nyckel som lagras i programmets konfigurationsfil (appsettings.json).

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

## <a name="3---define-an-index-schema"></a>3 – definiera ett indexschema
Ditt index skapas med ett enda anrop till `Indexes.Create`-metoden. Den här metoden tar som en parameter ett `Index`-objekt som definierar ditt Azure Search-index. Du måste skapa ett `Index`-objekt och initiera det så här:

1. Ange `Name`-egenskapen för `Index`-objektet till namnet på ditt index.

2. Ange `Fields`-egenskapen för `Index`-objektet till en matris med `Field`-objekt. Det enklaste sättet att skapa `Field`-objekten på är att anropa `FieldBuilder.BuildForType`-metoden och skicka en modellklass för typparametern. En modellklass har egenskaper som mappar till fält i ditt index. På så sätt kan du binda dokument från din sökindex till instanser av din modellklass.

> [!NOTE]
> Även om du inte planerar att använda någon modellklass kan du definiera ditt index genom att skapa `Field`-objekt direkt. Du kan ge namnet på fältet till konstruktorn, tillsammans med datatypen (eller analysverktyget för strängfält). Du kan också ange andra egenskaper som `IsSearchable`, `IsFilterable` osv.
>
>

Det är viktigt att du har användarupplevelsen och dina affärsbehov i åtanke när du utformar ditt index eftersom varje fält måste tilldelas [lämpliga egenskaper](https://docs.microsoft.com/rest/api/searchservice/Create-Index). Dessa egenskaper styr vilka sökfunktioner (filtrering, aspekter, sortering av textsökningar osv) som tillämpas på vilka fält. För egenskaper som du inte uttryckligen anger inaktiverar klassen  `Field` som standard motsvarande sökfunktion såvida du inte uttryckligen aktiverar den.

I vårt exempel har vi gett indexet namnet ”hotels” och definierat fälten med en modellklass. Varje egenskap i modellklassen har attribut som avgör motsvarande indexfälts sökrelaterade beteende. Modellklassen definieras så här:

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

Indexdefinitionen ovan använder ett språkanalysverktyg för `description_fr`-fältet eftersom det ska lagra fransk text. Mer information om språkanalysverktyg finns i [avsnittet om språkstöd](https://docs.microsoft.com/rest/api/searchservice/Language-support) och i motsvarande [blogginlägg](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [!NOTE]
> Som standard används namnet för varje egenskap i din modellklass som namnet på motsvarande fält i indexet. Om du vill mappa alla egenskapsnamn till kamelnotationsfältnamn, så markerar du klassen med attributet `SerializePropertyNamesAsCamelCase`. Om du vill att mappa till ett annat namn, du kan använda `JsonProperty`-attributet som egenskapen `DescriptionFr` ovan. Attributet `JsonProperty` har företräde framför attributet `SerializePropertyNamesAsCamelCase`.
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

## <a name="4---create-the-index-on-the-service"></a>4 – skapa indexet på tjänsten
Nu när du har ett initierat `Index`-objekt kan du skapa indexet genom att bara anropa `Indexes.Create` för ditt `SearchServiceClient`-objekt:

```csharp
serviceClient.Indexes.Create(definition);
```

Om begäran lyckas returnerar metoden som vanligt. Om det uppstår ett problem med begäran, till exempel på grund av en ogiltig parameter, returnerar metoden `CloudException`.

När du är klar med ett index och vill ta bort det anropar du bara `Indexes.Delete`-metoden för `SearchServiceClient`. Så här skulle vi till exempel göra om vi ville ta bort indexet ”hotels”:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> För enkelhetens skull används synkrona metoder för Azure Search .NET SDK i exempelkoden i den här artikeln. Vi rekommenderar att du använder asynkrona metoder i dina egna program så att de blir skalbara och responsiva. I exemplen ovan kan du till exempel använda `CreateAsync` och `DeleteAsync` i stället för `Create` och `Delete`.
> 
> 


## <a name="next-steps"></a>Nästa steg
När du har skapat ett Azure Search-index är det dags att [ladda upp innehållet till indexet](search-what-is-data-import.md) så att du kan börja söka efter data.


---
title: "Skapa ett Azure Search-index med hjälp av .NET SDK | Microsoft Docs"
description: "Skapa ett index i kod med hjälp av .NET SDK för Azure Search."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 12/08/2016
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 455c4847893175c1091ae21fa22215fd1dd10c53
ms.openlocfilehash: a607ab6bf73f59f55109f9ee60ab69aa15d74db3


---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>Skapa ett Azure Search-index med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [Översikt](search-what-is-an-index.md)
> * [Portalen](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Den här artikeln beskriver steg för steg hur du skapar ett Azure Search-[index](https://docs.microsoft.com/rest/api/searchservice/Create-Index) med hjälp av [Azure Search .NET SDK](https://aka.ms/search-sdk).

Innan du följer den här guiden och skapar ett index bör du redan ha [skapat en Azure Search-tjänst](search-create-service-portal.md).

Observera att all exempelkod i den här artikeln är skriven i C#. Du hittar den fullständiga källkoden [på GitHub](http://aka.ms/search-dotnet-howto).

## <a name="i-identify-your-azure-search-services-admin-api-key"></a>I. Identifiera Azure Search-tjänstens API-administratörsnyckel
Nu när du har etablerat en Azure Search-tjänst är du nästan klar att skicka förfrågningar mot tjänstens slutpunkt med hjälp av .NET SDK. Först måste du skaffa en av API-administratörsnycklarna som genererades för söktjänsten som du etablerade. .NET SDK skickar den här API-nyckeln vid varje begäran till tjänsten. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

1. För att hitta din tjänsts API-nycklar måste du logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till Azure Search-tjänstens blad
3. Klicka på nyckelikonen

Tjänsten har *administratörsnycklar* och *frågenycklar*.

* Dina primära och sekundära *administratörsnycklar* ger fullständig behörighet för alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor. Det finns två nycklar så att du kan fortsätta att använda den sekundära nyckeln om du bestämmer dig för att återskapa den primära nyckeln och tvärtom.
* Dina *frågenycklar* beviljar läsbehörighet till index och dokument och distribueras vanligen till klientprogram som skickar sökförfrågningar.

Du kan använda antingen en primär eller sekundär administrationsnyckel när du skapar ett index.

<a name="CreateSearchServiceClient"></a>

## <a name="ii-create-an-instance-of-the-searchserviceclient-class"></a>II. Skapa en instans av SearchServiceClient-klassen
Innan du kan börja använda .NET SDK Azure Search måste du skapa en instans av klassen `SearchServiceClient`. Den här klassen har flera konstruktorer. Den som du vill använda har namnet på din söktjänst och ett `SearchCredentials`-objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

Koden nedan skapar en ny `SearchServiceClient` med värden för söktjänstens namn och API-nyckel som lagras i programmets konfigurationsfil (`app.config` eller `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` har en `Indexes`-egenskap. Den här egenskapen tillhandahåller alla metoder som du behöver för att skapa, visa, uppdatera eller ta bort Azure Search-index.

> [!NOTE]
> `SearchServiceClient`-klassen hanterar anslutningar till din söktjänst. För att undvika att behöva öppna för många anslutningar bör du försöka dela en enskild instans av `SearchServiceClient` i ditt program om möjligt. Dess metoder är trådsäkra för att möjliggöra den typen av delning.
> 
> 

<a name="DefineIndex"></a>

## <a name="iii-define-your-azure-search-index"></a>III. Definiera ditt Azure Search-index
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
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
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

    // ToString() method omitted for brevity...
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

## <a name="iv-create-the-index"></a>IV. Skapa indexet
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

## <a name="next"></a>Nästa
När du har skapat ett Azure Search-index är det dags att [ladda upp innehållet till indexet](search-what-is-data-import.md) så att du kan börja söka efter data.




<!--HONumber=Dec16_HO2-->



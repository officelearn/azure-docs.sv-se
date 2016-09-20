<properties
    pageTitle="Skapa ett Azure Search-index med .NET SDK | Microsoft Azure | Värdbaserad söktjänst i molnet"
    description="Skapa ett index i kod med hjälp av .NET SDK för Azure Search."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="08/29/2016"
    ms.author="brjohnst"/>

# Skapa ett Azure Search-index med hjälp av .NET SDK
> [AZURE.SELECTOR]
- [Översikt](search-what-is-an-index.md)
- [Portalen](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Den här artikeln beskriver steg för steg hur du skapar ett Azure Search-[index](https://msdn.microsoft.com/library/azure/dn798941.aspx) med hjälp av [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Innan du följer den här guiden och skapar ett index bör du redan ha [skapat en Azure Search-tjänst](search-create-service-portal.md).

Observera att all exempelkod i den här artikeln är skriven i C#. Du hittar den fullständiga källkoden [på GitHub](http://aka.ms/search-dotnet-howto).

## I. Identifiera Azure Search-tjänstens API-administratörsnyckel
Nu när du har etablerat en Azure Search-tjänst är du nästan klar att skicka förfrågningar mot tjänstens slutpunkt med hjälp av .NET SDK. Först måste du skaffa en av API-administratörsnycklarna som genererades för söktjänsten som du etablerade. .NET SDK skickar den här API-nyckeln vid varje begäran till tjänsten. En giltig nyckel upprättar förtroende, i varje begäran, mellan programmet som skickar begäran och tjänsten som hanterar den.

1. För att hitta din tjänsts API-nycklar måste du logga in på [Azure Portal](https://portal.azure.com/).
2. Gå till Azure Search-tjänstens blad
3. Klicka på nyckelikonen

Tjänsten har *administratörsnycklar* och *frågenycklar*.

  - Dina primära och sekundära *administratörsnycklar* ger fullständig behörighet för alla åtgärder, inklusive möjligheten att hantera tjänsten, skapa och ta bort index, indexerare och datakällor. Det finns två nycklar så att du kan fortsätta att använda den sekundära nyckeln om du bestämmer dig för att återskapa den primära nyckeln och tvärtom.
  - Dina *frågenycklar* beviljar läsbehörighet till index och dokument och distribueras vanligen till klientprogram som skickar sökförfrågningar.

Du kan använda antingen en primär eller sekundär administrationsnyckel när du skapar ett index.

<a name="CreateSearchServiceClient"></a>
## II. Skapa en instans av SearchServiceClient-klassen
Innan du kan börja använda .NET SDK Azure Search måste du skapa en instans av klassen `SearchServiceClient`. Den här klassen har flera konstruktorer. Den som du vill använda har namnet på din söktjänst och ett `SearchCredentials`-objekt som parametrar. `SearchCredentials` omsluter din API-nyckel.

Koden nedan skapar en ny `SearchServiceClient` med värden för söktjänstens namn och API-nyckel som lagras i programmets konfigurationsfil (`app.config` eller `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` har en `Indexes`-egenskap. Den här egenskapen tillhandahåller alla metoder som du behöver för att skapa, visa, uppdatera eller ta bort Azure Search-index.

> [AZURE.NOTE] `SearchServiceClient`-klassen hanterar anslutningar till din söktjänst. För att undvika att behöva öppna för många anslutningar bör du försöka dela en enskild instans av `SearchServiceClient` i ditt program om möjligt. Dess metoder är trådsäkra för att möjliggöra den typen av delning.

<a name="DefineIndex"></a>
## III. Definiera ditt Azure Search-index med hjälp av `Index`-klassen
Ditt index skapas med ett enda anrop till `Indexes.Create`-metoden. Den här metoden tar som en parameter ett `Index`-objekt som definierar ditt Azure Search-index. Du måste skapa ett `Index`-objekt och initiera det så här:

1. Ange `Name`-egenskapen för `Index`-objektet till namnet på ditt index.
2. Ange `Fields`-egenskapen för `Index`-objektet till en matris med `Field`-objekt. Varje `Field`-objekt definierar beteendet för ett fält i ditt index. Du kan ge namnet på fältet till konstruktorn, tillsammans med datatypen (eller analysverktyget för strängfält). Du kan också ange andra egenskaper som `IsSearchable`, `IsFilterable` osv.

Det är viktigt att du har användarupplevelsen och dina affärsbehov i åtanke när du utformar ditt index eftersom varje `Field` måste tilldelas [lämpliga egenskaper](https://msdn.microsoft.com/library/azure/dn798941.aspx). Dessa egenskaper styr vilka sökfunktioner (filtrering, aspekter, sortering av textsökningar osv) som tillämpas på vilka fält. För egenskaper som du inte uttryckligen anger inaktiverar klassen  `Field` som standard motsvarande sökfunktion såvida du inte uttryckligen aktiverar den.

I vårt exempel har vi gett indexet namnet ”hotels” och definierat fälten så här:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Vi har noga valt egenskapsvärdena för varje `Field` baserat på hur vi tror att de ska användas i ett program. Eftersom det till exempel är troligt att personer som söker efter hotell är intresserade av nyckelordsmatchningar i fältet `description` har vi aktiverat fulltextsökning för det fältet genom att ange `IsSearchable` till `true`.

Observera att exakt ett fält i indexet av typen `DataType.String` måste definieras som _nyckelfältet_. Det gör du genom att ange `IsKey` till `true` (se `hotelId` i ovanstående exempel).

Indexdefinitionen ovan använder ett anpassat språkanalysverktyg för fältet `description_fr` eftersom det ska lagra fransk text. Mer information om språkanalysverktyg finns i [avsnittet om språkstöd på MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) och i tillhörande [blogginlägg](https://azure.microsoft.com/blog/language-support-in-azure-search/).

> [AZURE.NOTE]  Observera att `Field` automatiskt tilldelas typen `DataType.String` med `IsSearchable` inställt till `true` när `AnalyzerName.FrLucene` skickas i konstruktorn.

## IV. Skapa indexet
Nu när du har ett initierat `Index`-objekt kan du skapa indexet genom att bara anropa `Indexes.Create` för ditt `SearchServiceClient`-objekt:

```csharp
serviceClient.Indexes.Create(definition);
```

Om begäran lyckas returnerar metoden som vanligt. Om det uppstår ett problem med begäran, till exempel på grund av en ogiltig parameter, returnerar metoden `CloudException`.

När du är klar med ett index och vill ta bort det anropar du bara `Indexes.Delete`-metoden för `SearchServiceClient`. Så här skulle vi till exempel göra om vi ville ta bort indexet ”hotels”:

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] För enkelhetens skull används synkrona metoder för Azure Search .NET SDK i exempelkoden i den här artikeln. Vi rekommenderar att du använder asynkrona metoder i dina egna program så att de blir skalbara och responsiva. I exemplen ovan kan du till exempel använda `CreateAsync` och `DeleteAsync` i stället för `Create` och `Delete`.

## Nästa
När du har skapat ett Azure Search-index är det dags att [ladda upp innehållet till indexet](search-what-is-data-import.md) så att du kan börja söka efter data.



<!--HONumber=sep16_HO1-->



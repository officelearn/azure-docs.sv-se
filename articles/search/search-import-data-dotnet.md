---
title: "Överföra data (.NET – Azure Search)| Microsoft Docs"
description: "Lär dig hur du laddar upp data till ett index i Azure Search med .NET SDK."
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: 
ms.assetid: 0e0e7e7b-7178-4c26-95c6-2fd1e8015aca
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
ms.openlocfilehash: bdd952869143c6ca6374bb9264db5bcba1f32b50
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2017
---
# <a name="upload-data-to-azure-search-using-the-net-sdk"></a>Ladda upp data till Azure Search med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [Översikt](search-what-is-data-import.md)
> * [NET](search-import-data-dotnet.md)
> * [REST](search-import-data-rest-api.md)
> 
> 

Den här artikeln visar hur du använder [Azure Search .NET SDK](https://aka.ms/search-sdk) för att importera data till ett Azure Search-index.

Innan du påbörjar den här genomgången bör du redan ha [skapat ett Azure Search-index](search-what-is-an-index.md). I den här artikeln förutsätter vi också att du redan har skapat ett `SearchServiceClient`-objekt. Mer information finns i [Skapa ett Azure Search-index med .NET SDK](search-create-index-dotnet.md#CreateSearchServiceClient).

> [!NOTE]
> All exempelkod i den här artikeln är skriven i C#. Du hittar den fullständiga källkoden [på GitHub](http://aka.ms/search-dotnet-howto). Du kan läsa om [Azure Search .NET SDK](search-howto-dotnet-sdk.md) och få en mer detaljerad genomgång av exempelkoden.

För att kunna skicka dokument till ditt index med .NET SDK måste du:

1. Skapa ett `SearchIndexClient`-objekt för att ansluta till ditt sökindex.
2. Skapa en `IndexBatch` som innehåller dokumenten som ska läggas till, ändras eller tas bort.
3. Anropa `Documents.Index`-metoden för din `SearchIndexClient` för att skicka `IndexBatch` till sökindexet.

## <a name="create-an-instance-of-the-searchindexclient-class"></a>Skapa en instans av klassen SearchIndexClient
För att importera data till ditt index med Azure Search .NET SDK måste du skapa en instans av klassen `SearchIndexClient`. Du kan skapa den här instansen själv, men det är enklare om du redan har en `SearchServiceClient`-instans och kan anropa dess `Indexes.GetClient`-metod. Här är ett exempel på hur du hämtar en `SearchIndexClient` för indexet med namnet ”hotels” från en `SearchServiceClient` med namnet `serviceClient`:

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

> [!NOTE]
> I ett typiskt sökprogram hanteras indexhanteringen och ifyllningen av en separat komponent från sökfrågorna. `Indexes.GetClient` är praktiskt för att fylla ett index eftersom du inte behöver ange en till `SearchCredentials`. Den gör det genom att skicka administratörsnyckeln som du använde för att skapa `SearchServiceClient` till den nya `SearchIndexClient`. Men i den del av ditt program som kör frågor är det bättre att skapa `SearchIndexClient` direkt så att du kan skicka en frågenyckel i stället för en administratörsnyckel. Den här riktlinjen följer [principen om lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) och hjälper till att göra programmet säkrare. Mer information om administratörsnycklar och frågenycklar finns i [REST API-referensen för Azure Search](https://docs.microsoft.com/rest/api/searchservice/).
> 
> 

`SearchIndexClient` har en `Documents`-egenskap. Den här egenskapen tillhandahåller alla metoder som du behöver för att lägga till, ändra, ta bort eller fråga dokument i ditt index.

## <a name="decide-which-indexing-action-to-use"></a>Bestäm vilken indexeringsåtgärd som du vill använda
För att importera data med .NET SDK måste du paketera dessa data i ett `IndexBatch`-objekt. En `IndexBatch` kapslar in en samling `IndexAction`-objekt, som vart och ett innehåller ett dokument och en egenskap som meddelar Azure Search vilken åtgärd som ska utföras för dokumentet (ladda upp, sammanfoga, ta bort osv.). Beroende på vilken av åtgärderna nedan som du väljer måste endast vissa fält tas med för varje dokument:

| Åtgärd | Beskrivning | Nödvändiga fält för varje dokument | Anteckningar |
| --- | --- | --- | --- |
| `Upload` |En `Upload`-åtgärd liknar en ”upsert” där dokumentet infogas om det är nytt och uppdateras/ersätts om det finns. |nyckel plus eventuella andra fält som du vill definiera |När du uppdaterar och ersätter ett befintligt dokument tilldelas alla fält som inte angetts i begäran `null`. Detta sker även om fältet tidigare hade ett värde som inte var null. |
| `Merge` |Uppdaterar ett befintligt dokument med de angivna fälten. Sammanfogningen misslyckas om dokumentet inte finns i indexet. |nyckel plus eventuella andra fält som du vill definiera |Alla fält som du anger i en sammanfogning ersätter det befintliga fältet i dokumentet. Detta gäller även fält av typen `DataType.Collection(DataType.String)`. Om dokumentet till exempel innehåller ett `tags`-fält med värdet `["budget"]` och du utför en sammanfogning med värdet `["economy", "pool"]` för `tags` så blir det slutliga värdet för fältet `tags` `["economy", "pool"]`. Det blir inte `["budget", "economy", "pool"]`. |
| `MergeOrUpload` |Den här åtgärden fungerar som `Merge` om ett dokument med den angivna nyckeln redan finns i indexet. Om dokumentet inte finns fungerar den som `Upload` med ett nytt dokument. |nyckel plus eventuella andra fält som du vill definiera |- |
| `Delete` |Tar bort det angivna dokumentet från indexet. |endast nyckel |Andra fält som du anger än nyckelfältet ignoreras. Om du vill ta bort ett enstaka fält från ett dokument använder du `Merge` i stället och anger bara fältet till null. |

Du kan ange vilken åtgärd du vill använda med de olika statiska metoderna för `IndexBatch`- och `IndexAction`-klasserna, som du ser i nästa avsnitt.

## <a name="construct-your-indexbatch"></a>Skapa IndexBatch
Nu när du vet vilka åtgärder som ska utföras på dina dokument är det dags att skapa `IndexBatch`. Exemplet nedan visar hur du skapar en batch med några olika åtgärder. Observera att vi i vårt exempel använder den anpassade klassen `Hotel` som mappar till ett dokument i indexet ”hotels”.

```csharp
var actions =
    new IndexAction<Hotel>[]
    {
        IndexAction.Upload(
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
            }),
        IndexAction.Upload(
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
            }),
        IndexAction.MergeOrUpload(
            new Hotel()
            {
                HotelId = "3",
                BaseRate = 129.99,
                Description = "Close to town hall and the river"
            }),
        IndexAction.Delete(new Hotel() { HotelId = "6" })
    };

var batch = IndexBatch.New(actions);
```

I detta fall använder vi `Upload`, `MergeOrUpload` och `Delete` som våra sökåtgärder, som anges av de metoder som anropas med klassen `IndexAction`.

Anta att exempelindexet ”hotels” redan fyllts med ett antal dokument. Observera att vi inte behövde ange alla tillgängliga dokumentfält när vi använde `MergeOrUpload` och att vi bara angav dokumentnyckeln (`HotelId`) när vi använde `Delete`.

Observera också att du bara kan ta med upp till 1 000 dokument i samma indexeringsbegäran.

> [!NOTE]
> I det här exemplet använder vi åtgärder för olika dokument. Om du vill utföra samma åtgärder i alla dokument i batchen, i stället för att anropa `IndexBatch.New`, kan du använda de andra statiska metoderna för `IndexBatch`. Du kan till exempel skapa batchar genom att anropa `IndexBatch.Merge`, `IndexBatch.MergeOrUpload` eller `IndexBatch.Delete`. Dessa metoder använder en samling dokument (objekt av typen `Hotel` i det här exemplet) i stället för `IndexAction`-objekt.
> 
> 

## <a name="import-data-to-the-index"></a>Importera data till indexet
Nu när du har ett initierat `IndexBatch`-objekt kan du skicka det till indexet genom att anropa `Documents.Index` för ditt `SearchIndexClient`-objekt. Följande exempel visar hur du anropar `Index`, samt några extra steg som du måste utföra:

```csharp
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
```

Observera den `try`/`catch` som omger anropet till `Index`-metoden. Catch-blocket hanterar en viktig felsituation som kan uppstå i samband med indexering. Om Azure Search-tjänsten inte kan indexera vissa av dokumenten i batchen skickas ett `IndexBatchException` av `Documents.Index`. Detta kan inträffa om du indexerar dokument när tjänsten är hårt belastad. **Vi rekommenderar starkt att du uttryckligen hanterar den här situationen i din kod.** Du kan fördröja och sedan försöka indexera dokumentet som misslyckades igen eller så kan du logga och fortsätta som i exemplet, eller göra något annat beroende på programmets krav på datakonsekvens.

Koden i exemplet ovan skapar en fördröjning på två sekunder. Indexeringen sker asynkront i Azure Search-tjänsten, så exempelprogrammet måste vänta en kort stund för att kontrollera att dokumenten är tillgängliga för sökning. Fördröjningar som den här är normalt endast nödvändiga i demonstrationer, tester och exempelprogram.

<a name="HotelClass"></a>

### <a name="how-the-net-sdk-handles-documents"></a>Hur .NET SDK hanterar dokument
Du kanske undrar hur Azure Search .NET SDK kan ladda upp instanser av en användardefinierad klass som `Hotel` till indexet. För att besvara frågan ska vi titta på klassen `Hotel`, som mappar till indexeringsschemat som definieras i [Skapa ett Azure Search-index med .NET SDK](search-create-index-dotnet.md#DefineIndex):

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

Det första som du bör lägga märke till är att varje offentlig egenskap för `Hotel` motsvarar ett fält i indexdefinitionen, men med en viktig skillnad: namnet på fälten börjar med gemen (”kamelnotation”), men namnet på offentliga egenskaper för `Hotel` börjar med versal (”Pascalnotation”). Det här är ett vanligt scenario i .NET-program som utför databindning där målschemat ligger utanför programutvecklarens kontroll. I stället för att behöva bryta mot riktlinjerna för .NET-namngivning genom att göra egenskapsnamnen gemena kan du uppmana SDK att mappa egenskapsnamnen till kamelnotation automatiskt med attributet `[SerializePropertyNamesAsCamelCase]`.

> [!NOTE]
> Azure Search .NET SDK använder [NewtonSoft JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm)-biblioteket för att serialisera och deserialisera anpassade modellobjekt till och från JSON. Du kan anpassa den här serialiseringen om det behövs. Mer information finns i [Anpassad serialisering med JSON.NET](search-howto-dotnet-sdk.md#JsonDotNet). Ett exempel på detta är användningen av `[JsonProperty]`-attributet för `DescriptionFr`-egenskapen i exempelkoden ovan.
> 
> 

Den andra viktiga saken om klassen `Hotel` är datatyperna för de offentliga egenskaperna. .NET-typerna för dessa egenskaper mappar till deras motsvarande fälttyper i indexdefinitionen. Exempelvis mappar `Category`-strängegenskapen till `category`-fältet, som är av typen `DataType.String`. Det finns liknande typmappningar mellan `bool?` och `DataType.Boolean`, `DateTimeOffset?` och `DataType.DateTimeOffset` osv. De specifika reglerna för typmappningen finns dokumenterade med `Documents.Get`-metoden i [Azure Search .NET SDK-referensen](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations#Microsoft_Azure_Search_IDocumentsOperations_GetWithHttpMessagesAsync__1_System_String_System_Collections_Generic_IEnumerable_System_String__Microsoft_Azure_Search_Models_SearchRequestOptions_System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_).

Den här möjligheten att använda egna klasser som dokument fungerar i båda riktningar. Du kan också hämta sökresultat och låta SDK deserialisera dem automatiskt till valfri typ, som du ser i [nästa artikel](search-query-dotnet.md).

> [!NOTE]
> Azure Search .NET SDK stöder också dynamiskt typifierade dokument med hjälp av klassen `Document`, som är en nyckel/värde-mappning av fältnamn till fältvärden. Detta är användbart i scenarier då du inte känner till indexeringsschemat redan i designfasen, eller då det skulle vara opraktiskt att binda till specifika modellklasser. Alla metoder i SDK som hanterar dokument har överlagringar som fungerar med klassen `Document`, samt starkt typifierade överlagringar som använder en parameter av generisk typ. Endast de senare används i exempelkoden i den här artikeln.
> 
> 

**Varför du bör använda datatyper som kan ha värdet null**

När du utformar dina egna modellklasser för mappning till ett Azure Search-index rekommenderar vi att du deklarerar egenskaper för värdetyper som `bool` och `int` så att de kan vara null (t.ex. `bool?` i stället för `bool`). Om du använder en icke-nullbar egenskap måste du **se till** att inga dokument i indexet innehåller ett null-värde för motsvarande fält. Varken SDK eller Azure Search-tjänsten hjälper dig med detta.

Detta är inte bara ett hypotetiskt problem. Tänk dig ett scenario där du lägger till ett nytt fält till ett befintligt index som är av typen `DataType.Int32`. När du har uppdaterat indexdefinitionen har alla dokument ett null-värde för det nya fältet (eftersom alla typer kan vara null i Azure Search). Om du sedan använder en modellklass med en icke-nullbar `int`-egenskap för det fältet returneras ett `JsonSerializationException` som detta när du försöker hämta dokument:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Av den anledningen rekommenderar vi att du använder nullbara typer i dina modellklasser som bästa praxis.

## <a name="next-steps"></a>Nästa steg
När du har fyllt Azure Search-indexet kan du börja skicka frågor för att söka efter dokument. Mer information finns i [Fråga ditt Azure Search-index](search-query-overview.md).


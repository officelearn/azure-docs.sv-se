---
title: Läsa in data till ett Azure Search-index i C# (.NET SDK) – Azure Search
description: Lär dig att överföra data till ett helt sökbart textindex i Azure Search med C#-exempelkod och .NET SDK.
author: heidisteen
manager: cgronlun
ms.author: heidist
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/20/2019
ms.openlocfilehash: d2d54d1425bbb67a3f5ba1b6081a9f74ff87f4d6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871054"
---
# <a name="quickstart-2---load-data-to-an-azure-search-index-using-c"></a>Snabbstart: 2 – Läs in data till en Azure Search-index medC#

Den här artikeln visar hur du importerar data till [ett Azure Search-index](search-what-is-an-index.md) med C# och [.NET SDK](https://aka.ms/search-sdk). Skicka dokument till ditt index åstadkoms genom att utföra dessa uppgifter:

> [!div class="checklist"]
> * Skapa en [ `SearchIndexClient` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient?view=azure-dotnet) objekt för att ansluta till ett search-index.
> * Skapa en [ `IndexBatch` ](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexbatch?view=azure-dotnet) objekt som innehåller dokumenten som ska läggas till, ändrats eller tagits bort.
> * Anropa den `Documents.Index` metoden på `SearchIndexClient` ladda upp dokument till ett index.

## <a name="prerequisites"></a>Nödvändiga komponenter

[Skapa ett Azure Search-index](search-create-index-dotnet.md) och en `SearchServiceClient` objekt enligt [”skapa en klient”](search-create-index-dotnet.md#CreateSearchServiceClient).


## <a name="create-a-client"></a>Skapa en klient
Om du vill importera data, behöver du en instans av den `SearchIndexClient` klass. Det finns flera metoder för att skapa den här klassen, inklusive användning av den `SearchServiceClient` -instans som redan har skapats. 

Som i följande exempel visas, kan du använda den `SearchServiceClient` instans och anropa dess `Indexes.GetClient` metod. Det här kodfragmentet hämtar en `SearchIndexClient` för indexet med namnet ”hotels” från en `SearchServiceClient` med namnet `serviceClient`.

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

`SearchIndexClient` har en `Documents`-egenskap. Den här egenskapen tillhandahåller alla metoder som du behöver för att lägga till, ändra, ta bort eller fråga dokument i ditt index.

> [!NOTE]
> I ett typiskt sökprogram hanteras frågor och indexering separat. Medan `Indexes.GetClient` är praktiskt eftersom du kan återanvända objekt som `SearchCredentials`, ett mer robust sätt innebär att du skapar den `SearchIndexClient` direkt så att du kan skicka en frågenyckel i stället för en administratörsnyckel. Den här övningen är konsekvent med den [principen om lägsta behörighet](https://en.wikipedia.org/wiki/Principle_of_least_privilege) och hjälper till att göra programmet säkrare. Du måste skapa en `SearchIndexClient` i nästa övning. Mer information om nycklar finns i [skapa och hantera api-nycklar för en Azure Search-tjänst](search-security-api-keys.md).
> 
> 

<a name="construct-indexbatch"></a>

## <a name="construct-indexbatch"></a>Skapa IndexBatch

Om du vill importera data med hjälp av .NET SDK, packa upp data till en `IndexBatch` objekt. En `IndexBatch` kapslar in en samling `IndexAction` objekt, vart och ett innehåller ett dokument och en egenskap som meddelar Azure Search vilken åtgärd som ska utföras för dokumentet (ladda upp, sammanfoga, ta bort och mergeOrUpload). Mer information om indexering åtgärder finns i [indexering åtgärder: ladda upp, sammanfoga, mergeOrUpload, ta bort](search-what-is-data-import.md#indexing-actions).

Om vi antar att du vet vilka åtgärder som ska utföras på dina dokument, är du redo att konstruera den `IndexBatch`. Exemplet nedan visar hur du skapar en batch med några olika åtgärder. I exemplet används en anpassad klass som heter `Hotel` som mappar till ett dokument i indexet ”hotels”.

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

## <a name="call-documentsindex"></a>Anropa Documents.Index
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

Läs mer om Dokumentbearbetning [”hur .NET SDK hanterar dokument”](search-howto-dotnet-sdk.md#how-dotnet-handles-documents).


## <a name="next-steps"></a>Nästa steg
Nästa steg är när du har fyllt Azure Search-index, skicka frågor för att söka efter dokument. 

> [!div class="nextstepaction"]
> [Fråga ett Azure Search-index iC#](search-query-dotnet.md)

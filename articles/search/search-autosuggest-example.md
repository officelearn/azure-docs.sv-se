---
title: Automatiska förslag i exemplet för att lägga till listrutan villkoren i en sökruta – Azure Search
description: Lägg till indata för föreslagna frågan genom att skapa förslagsställare och utformningen av begäranden som anropar föreslagna frågor i Azure Search.
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 03/22/2019
ms.author: heidist
ms.openlocfilehash: 1db085d61c60d303aaff5c3b0d16998805fcda90
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58373082"
---
# <a name="example-add-autosuggest-for-dropdown-query-selections"></a>Exempel: Lägga till automatiska förslag för listrutan fråga val

Termen sökinmatningar kan innehålla en listruta för föreslagna sökord. Du har sett den här funktionen i kommersiella sökmotorer och du kan implementera en liknande upplevelse i Azure Search med hjälp av en [förslagsställare konstruktion](index-add-suggesters.md) och en förslag-åtgärd i en fråga. Den här artikeln använder exemplen för att demonstrera utformningen av en fråga för automatiska förslag, med hjälp av en förslagsställare som du redan har definierat. 

## <a name="rest-api"></a>REST-API

Du kan använda [Postman](search-fiddler.md) eller [PowerShell](search-create-index-rest-api.md) och [REST API](https://docs.microsoft.com/rest/api/searchservice/) du prova att använda det här exemplet, men resultaten returneras som JSON-dokument. En mer realistisk och visuell upplevelse kan hittas med hjälp av den [exempelkoden för automatisk komplettering](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

### <a name="1---index-with-a-suggester-construct"></a>1 – indexera med en förslagsställare-konstruktion

Automatiska förslag i börjar med en [förslagsställare konstruktion](index-add-suggesters.md) till ett index, består av fält som bidrar listrutan värden. Med följande schema kan kan föreslagna frågor formuleras med värden från fälten hotelName och kategori.

Under förutsättning att minimal exempeldatauppsättningarna hittades i snabbstarter, hotell namn inkluderar ”avancerad förblir” och ”Roach motel” och kategorier inkluderar ”Lyxig” och ”Budget”.

Om du redan har hotellindexet bör du ta bort och återskapa den med följande schema. Det här schemat lägger till en förslagsställare. Kom ihåg att hämta informationen även på nytt.

```json
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ],
  "suggesters": [
    {
      "name": "sg",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": ["hotelName", "category"]
    }
  ]
}

```

### <a name="2---query-with-suggestions-operator"></a>2 – fråga med förslag operator

Du använder en förslagsställare och anropa autoförslag, måste du skicka en [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions) begär via GET eller POST. På begäran, söktjänsten söker efter möjliga matchningar när de första tre tecknen tas emot. 

I huvudet för begäran och ange **api-nyckeln** att en administratör eller fråga nyckel, och **Content-Type** till application/json. 

```http
GET https://mydemo.search.windows.net/indexes/hotels/docs/suggest?search=fan&&suggesterName=sg
```

Begäran-genomsökningar alla fält ingår i förslagsställare (hotelName och kategori), returnerar följande svar:

```
{
    "@odata.context": "https://mydemo.search.windows.net/indexes('hotels')/$metadata#docs(*)",
    "value": [
        {
            "@search.text": "Fancy Stay",
            "hotelId": "1"
        }
    ]
}
```

För att leverera förväntat resultat, skulle klientkoden renderas resultaten som listrutan under ett sökfält.

## <a name="net-sdk-c"></a>.NET SDK (C#)

### <a name="1---index-with-a-suggester-construct"></a>1 – indexera med en förslagsställare-konstruktion

I .NET-SDK använder en [förslagsställare klass](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Förslagsställare är en samling, men det kan bara ta ett objekt.

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelId", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

### <a name="2---query-with-suggest-method"></a>2 – fråga med förslag, metod

Den [DocumentsOperationsExtensions.Suggest metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet) används för att returnera föreslagna frågesträngar.

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult suggestResult = _indexClient.Documents.Suggest(term, "sg",sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = suggestResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

## <a name="see-also"></a>Se också

+ [Utforska REST-API med hjälp av Postman](search-fiddler.md)
+ [Exempel: Automatisk komplettering](search-autocomplete-tutorial.md)
+ [Lägg till förslagsställare till ett index](index-add-suggesters.md)
+ [Lägg till en förslagsställare klass med hjälp av .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)
+ [Anropa förslag med hjälp av GET eller POST (REST API)](https://docs.microsoft.com/rest/api/searchservice/suggestions)
+ [Anropa förslag med SuggestWithHttpMessagesAsync (.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) eller 
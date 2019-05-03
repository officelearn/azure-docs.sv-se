---
title: Lägg till typeahead frågor till ett index – Azure Search
description: Aktivera frågeifyllningsförslag fråga åtgärder i Azure Search genom att skapa förslagsställare och utformningen av begäranden som anropar Komplettera automatiskt eller autosuggested sökord.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
ms.manager: cgronlun
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 400b1613a87d4de65879a512642e16884c7d03b4
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/02/2019
ms.locfileid: "65021884"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Lägg till förslagsställare till ett index för typeahead i Azure Search

En **förslagsställare** är en konstruktion i en [Azure Search-index](search-what-is-an-index.md) som har stöd för en ”sökning-som-du-type”-upplevelse. Den innehåller en lista med fält som du vill aktivera typeahead fråga indata. I ett index i samma förslagsställare har stöd för ett eller båda av dessa två typeahead varianter: *automatisk komplettering* slutförs den term eller en fras som du skriver, *förslag* ger en kort lista med resultat. 

Följande skärmbild illustrerar både typeahead-funktionerna. I den här Xbox-söksidan tar automatisk komplettering objekt dig till en ny sida med sökresultat för den frågan, medan de faktiska resultaten som tar dig till en sida för det specifika spelet förslagen. Du kan begränsa Komplettera automatiskt till ett objekt i ett sökfält eller ange en lista som den som visas här. Förslag, kan du ge någon del av ett dokument som bäst beskriver resultatet.

![Visual jämförelse av automatisk komplettering och föreslagna frågor](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual jämförelse av automatisk komplettering och föreslagna frågor")

För att implementera dessa beteenden i Azure Search, finns det ett index och fråga komponent. 

+ Indexkomponenten är en förslagsställare. Du kan använda portalen, REST API eller .NET SDK för att skapa en förslagsställare. 

+ Frågekomponenten är en åtgärd som anges på query-fråga (åtgärd förslag eller Komplettera automatiskt). 

Sök-som-du-type-stöd är aktiverat på basis av per fält. Du kan implementera båda typeahead beteenden inom samma söklösning om du vill att en upplevelse liknar det som anges i skärmbilden. Både begäranden mål den *dokument* samling specifikt index och -svar returneras när en användare har tillhandahållit Indatasträngen på minst tre tecken.

## <a name="create-a-suggester"></a>Skapa en förslagsställare

Även om en förslagsställare har flera egenskaper, är främst en uppsättning fält som du aktiverar en typeahead upplevelse. En reseapp kanske exempelvis vill aktivera typeahead sökning på mål och städer attraktioner. Därför måste övergår alla tre fält i fältsamlingen.

Lägg till en till ett indexschema för att skapa en förslagsställare. Du kan ha en förslagsställare i ett index (mer specifikt en förslagsställare i samlingen förslagsställare). 

### <a name="use-the-rest-api"></a>Använd REST API

REST API, du kan lägga till förslagsställare via [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatera Index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels",
    "fields": [
      . . .
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["hotelName", "category"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```
När en förslagsställare har skapats kan du lägga till den [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [automatisk komplettering API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) i din fråga logik för att anropa funktionen.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

I C#, definiera en [förslagsställare objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` är en samling, men det kan bara ta ett objekt. 

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

## <a name="property-reference"></a>Referens för egenskaper

Nyckeln pekar på meddelande om förslagsställare är att det finns ett namn (förslagsställare refereras av namn på en begäran) och searchMode (för närvarande bara ett ”analyzingInfixMatching”) i listan över fält som typeahead är aktiverat. 

Egenskaper som definierar en förslagsställare inkluderar följande:

|Egenskap       |Beskrivning      |
|--------------|-----------------|
|`name`        |Namnet på förslagsställare. Du använder namnet på förslagsställare när du anropar den [förslag REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [REST-API för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Den strategi som används för att söka efter kandidat fraser. Det enda läge som stöds för närvarande är `analyzingInfixMatching`, vilket genomför flexibla matchningar av fraser i början eller mitten meningar.|
|`sourceFields`|En lista över en eller flera fält som är källan till innehållet för förslag. Endast fält av typen `Edm.String` och `Collection(Edm.String)` kan vara källor förslag. Endast fält som inte har ett anpassat språkanalysverktyg ange kan användas.<p/>Ange endast de fält som lämpar sig i ett förväntat och att rätt svar, oavsett om det är en sträng som slutförda i ett sökfält eller en listruta.<p/>Ett hotellnamn är en bra kandidat eftersom den har precision. Utförlig fält som beskrivningar och kommentarer är för kompakta. På samma sätt är repetitiva fält, till exempel kategorier och taggar, mindre effektiva. I exemplen är inkluderar vi ”kategori” ändå för att visa att du kan ta flera fält. |

## <a name="when-to-create-a-suggester"></a>När du vill skapa en förslagsställare

Att undvika en återskapning av ett index, en förslagsställare och fälten som anges i `sourceFields` måste skapas på samma gång.

Om du lägger till en förslagsställare till ett befintligt index, där befintliga fält ingår i `sourceFields`, fältdefinitionen grunden ändras och återskapas krävs. Mer information finns i [återskapar ett Azure Search-index](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Hur du använder en förslagsställare

Som vi nämnde tidigare kan använda du en förslagsställare för föreslagna frågor, automatisk komplettering, eller båda. 

En förslagsställare refereras på begäran tillsammans med åtgärden. Ange till exempel antingen på ett GET REST-anrop `suggest` eller `autocomplete` på samlingen dokument. REST, när en förslagsställare har skapats kan använda den [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [automatisk komplettering API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) i frågans logik.

.NET, använda [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) eller [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Ett exempel som visar båda förfrågningarna finns i [exempel för att lägga till automatisk komplettering och förslag i Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Exempelkod

Den [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) exempel innehåller båda C# och Java-kod, och visar en förslagsställare konstruktion, föreslagna frågor, automatisk komplettering och aspekten navigering. 

Den använder en sandbox Azure Search-tjänst och ett förinstallerade index så att allt du behöver göra är genom att trycka på F5 för att köra den. Ingen prenumeration eller logga in krävs.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar följande exempel för att se hur begäranden formuleras.

> [!div class="nextstepaction"]
> [Förslag och automatisk komplettering exempel](search-autocomplete-tutorial.md) 

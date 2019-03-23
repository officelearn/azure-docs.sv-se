---
title: Lägg till typeahead frågor till ett index – Azure Search
description: Aktivera frågeifyllningsförslag fråga åtgärder i Azure Search genom att skapa förslagsställare och utformningen av begäranden som anropar Komplettera automatiskt eller autosuggested sökord.
ms.date: 03/22/2019
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
ms.openlocfilehash: 877294e80d655ab75be78a5aa57854a03a5f267a
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370660"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Lägg till förslagsställare till ett index för typeahead i Azure Search

En **förslagsställare** är en konstruktion i en [Azure Search-index](search-what-is-an-index.md) som har stöd för en ”sökning-som-du-type”-upplevelse. Den innehåller en lista med fält som du vill aktivera typeahead fråga indata. Det finns två varianter av typeahead: [ *automatisk komplettering* ](search-autocomplete-tutorial.md) slutförs den term eller en fras som du skriver, [ *automatiska förslag i* ](search-autosuggest-example.md) ger en kort lista över termer eller fraser som du kan välja som en fråga som indata. Du har otvivelaktigt sett dessa beteenden innan i kommersiella sökmotorer.

![Visuell jämförelse av automatisk komplettering och automatiska förslag i](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visual jämförelse av automatisk komplettering och automatiska förslag")

För att implementera dessa beteenden i Azure Search, finns det ett index och fråga komponent. 

+ Lägg till en förslagsställare i ett index. Du kan använda portal, REST API eller .NET SDK för att skapa en förslagsställare. 

+ Ange antingen ett automatiska förslag eller Komplettera automatiskt åtgärd på en fråga. 

> [!Important]
> Automatisk komplettering är för närvarande i förhandsversion, finns i förhandsversion REST API: er och SDK för .NET och stöds inte för produktionsprogram. 

Typeahead stöd är aktiverat på basis av per fält. Du kan implementera båda typeahead beteenden inom samma söklösning om du vill att en upplevelse liknar det som anges i skärmbilden. Både begäranden mål den *dokument* samling specifikt index och -svar returneras när en användare har tillhandahållit Indatasträngen på minst tre tecken.

## <a name="create-a-suggester"></a>Skapa en förslagsställare

Även om en förslagsställare har flera egenskaper, är främst en uppsättning fält som du aktiverar en typeahead upplevelse. En reseapp kanske exempelvis vill aktivera typeahead sökning på mål och städer attraktioner. Därför måste övergår alla tre fält i fältsamlingen.

Lägg till en till ett indexschema för att skapa en förslagsställare. Du kan ha en förslagsställare i ett index (mer specifikt en förslagsställare i samlingen förslagsställare). 

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

Nyckeln pekar på meddelande om förslagsställare är att det finns ett namn (förslagsställare refereras av namn på en begäran) och searchMode (för närvarande bara ett ”analyzingInfixMatching”) i listan över fält som typeahead är aktiverat. 

När en förslagsställare har skapats kan du lägga till den [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions) i din fråga logik för att anropa funktionen.

### <a name="property-reference"></a>Referens för egenskaper

Egenskaper som definierar en förslagsställare inkluderar följande:

|Egenskap       |Beskrivning      |
|--------------|-----------------|
|`name`        |Namnet på förslagsställare. Du använder namnet på förslagsställare när du anropar den [förslag REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [automatisk komplettering REST API (förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Den strategi som används för att söka efter kandidat fraser. Det enda läge som stöds för närvarande är `analyzingInfixMatching`, vilket genomför flexibla matchningar av fraser i början eller mitten meningar.|
|`sourceFields`|En lista över en eller flera fält som är källan till innehållet för förslag. Endast fält av typen `Edm.String` och `Collection(Edm.String)` kan vara källor förslag. Endast fält som inte har ett anpassat språkanalysverktyg ange kan användas.<p/>Ange endast de fält som lämpar sig i ett förväntat och att rätt svar, oavsett om det är en sträng som slutförda i ett sökfält eller en listruta.<p/>Ett hotellnamn är en bra kandidat eftersom den har precision. Utförlig fält som beskrivningar och kommentarer är för kompakta. På samma sätt är repetitiva fält, till exempel kategorier och taggar, mindre effektiva. I exemplen är inkluderar vi ”kategori” ändå för att visa att du kan ta flera fält. |

### <a name="index-rebuilds-for-existing-fields"></a>Index återskapar för befintliga fält

Förslagsställare innehålla fält och om du lägger till en förslagsställare till ett befintligt index eller ändrar dess fält sammansättning du troligen måste indexet återskapas.

| Åtgärd | Påverkan |
|--------|--------|
| Skapa nya fält och skapa en ny förslagsställare samtidigt i samma uppdatering | Minst påverkan. Om ett index innehåller fält som tidigare har lagts till, påverkar lägga till nya fält och en ny förslagsställare inte befintliga fält. |
| Lägga till befintliga fält i en förslagsställare | Hög inverkan. Att lägga till ett fält ändras fältdefinition, vilket kräver en [återskapa](search-howto-reindex.md).|

## <a name="use-a-suggester"></a>Använda en förslagsställare

Som vi nämnde tidigare kan använda du en förslagsställare för automatiska förslag, automatisk komplettering, eller båda. 

En förslagsställare refereras på begäran tillsammans med åtgärden. Ange till exempel antingen på ett GET REST-anrop `suggest` eller `autocomplete` på samlingen dokument. REST, när en förslagsställare har skapats kan använda den [förslag API](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [automatisk komplettering API (förhandsversion)](https://docs.microsoft.com/rest/api/searchservice/autocomplete) i frågans logik.

.NET, använda [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet-preview) eller [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet-preview&viewFallbackFrom=azure-dotnet).

Exempel som visar varje begäran:

+ [Lägga till automatiska förslag för listrutan fråga val](search-autosuggest-example.md)

+ [Lägg till automatisk komplettering i partiella termen indata i Azure Search](search-autocomplete-tutorial.md) (i förhandsversion) 

## <a name="sample-code"></a>Exempelkod

Den [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) exempel innehåller båda C# och Java-kod, och visar en förslagsställare-konstruktion för automatiska förslag, automatisk komplettering och aspekten navigering. 

Den använder en sandbox Azure Search-tjänst och ett förinstallerade index så att allt du behöver göra är genom att trycka på F5 för att köra den. Ingen prenumeration eller logga in krävs.

## <a name="next-steps"></a>Nästa steg

Granska följande exempel för att se hur begäranden formuleras:

+ [Exempel på autosuggested sökfråga](search-autosuggest-example.md) 
+ [Exempel på sökfråga kompletterat (förhandsversion)](search-autocomplete-tutorial.md) 

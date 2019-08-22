---
title: Lägg till typeahead-frågor till ett index – Azure Search
description: Aktivera åtgärder av typen fråga i Azure Search genom att skapa förslag och utforma begär Anden som anropar Autoavsluta eller automatiska förslag på sökord.
ms.date: 05/02/2019
services: search
ms.service: search
ms.topic: conceptual
author: Brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: 73cfdb6a4185689a6485f55a4f6bdd1e7e3b14be
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648845"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Lägg till förslag till ett index för typeahead i Azure Search

En **förslags ställare** är en konstruktion i ett [Azure Search-index](search-what-is-an-index.md) som stöder en "sökning efter typ"-upplevelse. Den innehåller en lista över fält som du vill aktivera typeahead-indata för. I ett index stöder samma förslags ställare antingen eller båda av dessa två typeahead-varianter: *Autoavsluta* Slutför termen eller frasen som du skriver, *förslag* innehåller en kort lista över resultat. 

Följande skärm bild visar både typeahead-funktioner. På den här Xbox search-sidan tar du till en ny Sök resultat sida för den frågan, medan förslagen är faktiska resultat som tar dig till en sida för just det spelet. Du kan begränsa Autoavsluta till ett objekt i ett sökfält eller ange en lista som det som visas här. För förslag kan du välja en del av ett dokument som bäst beskriver resultatet.

![Visuell jämförelse av Autoavsluta och föreslagna frågor](./media/index-add-suggesters/visual-comparison-suggest-complete.png "Visuell jämförelse av Autoavsluta och föreslagna frågor")

Det finns ett index och en frågekomponent-komponent för att implementera dessa beteenden i Azure Search. 

+ Index komponenten är en förslags ställare. Du kan använda portalen, REST API eller .NET SDK för att skapa en förslags ställare. 

+ Frågekomponenten är en åtgärd som anges för förfrågan (antingen ett förslag eller en åtgärd för automatisk komplettering). 

Stöd för sökning efter typ har Aktiver ATS per fält. Du kan implementera båda typeahead-beteenden inom samma Sök lösning om du vill ha en upplevelse som liknar den som anges i skärm bilden. Båda begär Anden mål *dokument* samlingen för ett särskilt index och svar returneras efter att en användare har angett minst en sträng med tre tecken.

## <a name="create-a-suggester"></a>Skapa en förslagsställare

Även om en förslags ställare har flera egenskaper, är det i första hand en samling fält för vilka du aktiverar en typeahead-upplevelse. Till exempel kanske en rese app vill aktivera typeahead-sökning på destinationer, städer och Attractions. Därför skulle alla tre fälten gå in i fält samlingen.

Om du vill skapa en förslags pekare, lägger du till ett i ett index schema. Du kan ha en förslags ställare i ett index (särskilt en förslags ställare i samlingen med förslag). 

### <a name="use-the-rest-api"></a>Använd REST API

I REST API kan du lägga till förslag via [create index](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [Update index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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
När en förslags ställare har skapats lägger du till API [: et](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [Autoavsluta-API:](https://docs.microsoft.com/rest/api/searchservice/autocomplete) et i din fråge logik för att anropa funktionen.

### <a name="use-the-net-sdk"></a>Använda .NET SDK

I C#definierar du ett [förslags objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters`är en samling, men det kan bara ta ett objekt. 

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

## <a name="property-reference"></a>Egenskaps referens

Viktiga punkter att observera om förslag är att det finns ett namn (förslags ställare refereras till efter namn på en begäran), en searchMode (för närvarande bara ett, "analyzingInfixMatching") och listan över fält som typeahead är aktive rad för. 

Egenskaper som definierar en förslags ställare inkluderar följande:

|Egenskap      |Beskrivning      |
|--------------|-----------------|
|`name`        |Förslagets namn. Du använder namnet på förslags mottagaren när du anropar [förslagen REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [autocomplete REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete).|
|`searchMode`  |Strategin som används för att söka efter kandidat fraser. Det enda läge som stöds för `analyzingInfixMatching`närvarande är, som utför flexibel matchning av fraser i början eller i mitten av meningar.|
|`sourceFields`|En lista med ett eller flera fält som är källan till innehållet för förslag. Endast fält av typen `Edm.String` och `Collection(Edm.String)` kan vara källor för förslag. Endast fält som inte har någon anpassad språk analys uppsättning kan användas.<p/>Ange endast de fält som lånas ut till ett förväntat och lämpligt svar, oavsett om det är en slutförd sträng i ett sökfält eller en listruta.<p/>Ett hotell namn är en bra kandidat eftersom det har precision. Utförliga fält som beskrivningar och kommentarer är för kompakta. På samma sätt är upprepade fält, till exempel kategorier och taggar, mindre effektiva. I exemplen inkluderar vi "Category" ändå för att demonstrera att du kan inkludera flera fält. |

#### <a name="analysis-of-sourcefields-in-a-suggester"></a>Analys av SourceFields i en förslags ställare

Azure Search analyserar fält innehållet för att aktivera frågor om enskilda villkor. Förslags ställare kräver att prefix indexeras förutom fullständiga villkor, vilket kräver ytterligare analys över käll fälten. Anpassade Analyzer-konfigurationer kan kombinera alla olika tokenizers och filter, ofta på sätt som gör det möjligt att skapa de prefix som krävs för förslag. Därför **förhindrar Azure Search fält med anpassade analys verktyg att tas med i en förslags ställare**.

> [!NOTE] 
>  Den rekommenderade metoden för att undvika ovanstående begränsning är att använda två separata fält för samma innehåll. Detta gör att ett av fälten får förslags ställare och det andra kan konfigureras med en anpassad Analyzer-konfiguration.

## <a name="when-to-create-a-suggester"></a>När du ska skapa en förslags ställare

För att undvika att en index återskapas måste en förslags ställare och `sourceFields` de fält som anges i skapas samtidigt.

Om du lägger till en förslags pekare till ett befintligt index, där befintliga fält `sourceFields`ingår i, så krävs fält definitionen grundläggande ändringar och en återskapande. Mer information finns i [så här återskapar du ett Azure Search-index](search-howto-reindex.md).

## <a name="how-to-use-a-suggester"></a>Så här använder du en förslags ställare

Som tidigare nämnts kan du använda en förslags ställare för föreslagna frågor, komplettera automatiskt eller både och. 

En förslags ställare refererar till begäran tillsammans med åtgärden. Till exempel, på ett Get rest-anrop, anger `suggest` du `autocomplete` antingen eller i samlingen dokument. När en förslags ställare har skapats kan du använda API [: et](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller [API: et för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) i din fråge logik.

För .NET använder du [SuggestWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet) eller [AutocompleteWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet).

Ett exempel som demonstrerar båda förfrågningarna finns i [exempel på hur du lägger till Autoavsluta och förslag i Azure Search](search-autocomplete-tutorial.md).

## <a name="sample-code"></a>Exempelkod

[DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) -exemplet innehåller både C# och Java-kod och visar en förslags konstruktion, föreslagna frågor, Autoavsluta och aspekt navigering. 

Tjänsten använder en sandbox-Azure Search tjänst och ett förinstallerat index så att du kan köra det genom att trycka på F5. Ingen prenumeration eller behöver logga in.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar följande exempel för att se hur begäran formuleras.

> [!div class="nextstepaction"]
> [Förslag och Autoavsluta-exempel](search-autocomplete-tutorial.md) 

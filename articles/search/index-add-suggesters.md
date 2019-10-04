---
title: Lägg till typeahead-frågor till ett index – Azure Search
description: Aktivera åtgärder av typen fråga i Azure Search genom att skapa förslag och utforma begär Anden som anropar Autoavsluta eller automatiska förslag på sökord.
ms.date: 09/30/2019
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
ms.openlocfilehash: d3f934bea5df821e51a4747170af4f7efd1eaacc
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828301"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-search"></a>Lägg till förslag till ett index för typeahead i Azure Search

I Azure Search baseras "Sök-som-du-typ" eller typeahead-funktionerna på en **förslags** konstruktion som du lägger till i ett [sökindex](search-what-is-an-index.md). Det är en lista över ett eller flera fält som du vill att typeahead ska aktive ras för.

En förslags ställare stöder två typeahead-varianter: *Autoavsluta*, som slutför den term eller fras som du skriver och *förslag* som returnerar en kort lista med matchande dokument.  

Följande skärm bild, från [skapa din första app i C# ](tutorial-csharp-type-ahead-and-suggestions.md) exemplet, visar typeahead. Autoavsluta förväntar sig att användaren kan skriva i sökrutan. Den faktiska indatatypen är "TW", vilken komplettera automatiskt Slutför med "i", så att den kan matchas som "delad" som den potentiella Sök termen. Förslag visualiseras i list rutan. För förslag kan du välja en del av ett dokument som bäst beskriver resultatet. I det här exemplet är förslagen hotell namn. 

![Visuell jämförelse av Autoavsluta och föreslagna frågor,](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "visuell jämförelse av Autoavsluta och föreslagna frågor")

Det finns ett index och en frågekomponent-komponent för att implementera dessa beteenden i Azure Search. 

+ I indexet lägger du till en förslags pekare till ett index. Du kan använda portalen, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Resten av den här artikeln fokuserar på att skapa en förslags ställare. 

+ Anropa ett av [API: erna som anges nedan](#how-to-use-a-suggester)i förfrågan.

Stöd för sökning efter typ har Aktiver ATS per fält. Du kan implementera båda typeahead-beteenden inom samma Sök lösning om du vill ha en upplevelse som liknar den som anges i skärm bilden. Båda begär Anden mål *dokument* samlingen för ett särskilt index och svar returneras efter att en användare har angett minst en sträng med tre tecken.

## <a name="create-a-suggester"></a>Skapa en förslagsställare

Även om en förslags ställare har flera egenskaper, är det i första hand en samling fält för vilka du aktiverar en typeahead-upplevelse. Till exempel kanske en rese app vill aktivera typeahead-sökning på destinationer, städer och Attractions. Därför skulle alla tre fälten gå in i fält samlingen.

Om du vill skapa en förslags pekare, lägger du till ett i ett index schema. Du kan ha en förslags ställare i ett index (särskilt en förslags ställare i samlingen med förslag). 

### <a name="when-to-create-a-suggester"></a>När du ska skapa en förslags ställare

Det bästa sättet att skapa en förslags ställare är när du också skapar själva fält definitionen.

Om du försöker skapa en förslags ställare med redan existerande fält kommer API: et att tillåta det. Typeahead-text skapas vid indexering, om del termer i två eller flera teckenkombinationer är token i hela villkoren. Eftersom befintliga fält redan har tokens, måste du återskapa indexet om du vill lägga till dem i en förslags ställare. Mer information om att indexera om finns i [återskapa ett Azure Search-index](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Skapa med hjälp av REST API

Lägg till förslag via [skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index)i REST API. 

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


### <a name="create-using-the-net-sdk"></a>Skapa med .NET SDK

I C#definierar du ett [förslags objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). `Suggesters` är en samling, men det kan bara ta ett objekt. 

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

### <a name="property-reference"></a>Egenskaps referens

|Egenskap      |Beskrivning      |
|--------------|-----------------|
|`name`        |Förslagets namn.|
|`searchMode`  |Strategin som används för att söka efter kandidat fraser. Det enda läge som stöds för närvarande är `analyzingInfixMatching`, som utför flexibel matchning av fraser i början eller i mitten av meningar.|
|`sourceFields`|En lista med ett eller flera fält som är källan till innehållet för förslag. Fälten måste vara av typen `Edm.String` och `Collection(Edm.String)`. Om en analys anges i fältet måste det vara en namngiven analys från [den här listan](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (inte en anpassad analys).<p/>Vi rekommenderar att du bara anger de fält som lånar ut sig till ett förväntat och lämpligt svar, oavsett om det är en slutförd sträng i ett sökfält eller i en nedrullningsbar listruta.<p/>Ett hotell namn är en bra kandidat eftersom det har precision. Utförliga fält som beskrivningar och kommentarer är för kompakta. På samma sätt är upprepade fält, till exempel kategorier och taggar, mindre effektiva. I exemplen inkluderar vi "Category" ändå för att demonstrera att du kan inkludera flera fält. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Analys begränsningar för sourceFields i en förslags ställare

Azure Search analyserar fält innehållet för att aktivera frågor om enskilda villkor. Förslags ställare kräver att prefix indexeras förutom fullständiga villkor, vilket kräver ytterligare analys över käll fälten. Anpassade Analyzer-konfigurationer kan kombinera alla olika tokenizers och filter, ofta på sätt som gör det möjligt att skapa de prefix som krävs för förslag. Därför förhindrar Azure Search fält med anpassade analys verktyg att tas med i en förslags ställare.

> [!NOTE] 
>  Om du behöver kringgå ovanstående begränsning använder du två separata fält för samma innehåll. Detta gör att ett av fälten kan ha en förslags ställare, medan det andra kan konfigureras med en anpassad Analyzer-konfiguration.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Använda en förslags ställare i en fråga

När en förslags ställare har skapats anropar du rätt API i din fråge logik för att anropa funktionen. 

+ [Förslag REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Autoavsluta-REST API](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [AutocompleteWithHttpMessagesAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

API-användningen illustreras i följande anrop till REST API för automatisk komplettering. Det finns två takeaways i det här exemplet. För det första, som med alla frågor, är åtgärden mot dokument samlingen för ett index. Sedan kan du lägga till frågeparametrar. Även om många frågeparametrar är gemensamma för båda API: erna, är listan olika för var och en.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Om en förslags ställare inte har definierats i indexet kommer ett anrop till komplettera automatiskt eller förslag att Miss Missing.

## <a name="sample-code"></a>Exempelkod

+ [Skapa din första app i C# ](tutorial-csharp-type-ahead-and-suggestions.md) exemplet visar en förslags konstruktion, föreslagna frågor, Autoavsluta och fasett-navigering. Det här kod exemplet körs på en sandbox-Azure Search tjänst och använder ett förinställt hotell index så att allt du behöver göra är att trycka på F5 för att köra programmet. Ingen prenumeration eller inloggning är nödvändig.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) är ett äldre exempel som innehåller C# både och Java-kod. Det visar också en förslags konstruktion, föreslagna frågor, komplettera automatiskt och fasett-navigering. I det här kod exemplet används exempel data från värdbaserade [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) . 


## <a name="next-steps"></a>Nästa steg

Vi rekommenderar följande exempel för att se hur begäran formuleras.

> [!div class="nextstepaction"]
> [Förslag och Autoavsluta-exempel](search-autocomplete-tutorial.md) 

---
title: Lägga till typeahead-frågor i ett index
titleSuffix: Azure Cognitive Search
description: Aktivera frågeåtgärder för typ framåt i Azure Cognitive Search genom att skapa förslagsförespråkare och formulera begäranden som anropar automatiskt kompatibel eller efterföljande frågetermer.
manager: nitinme
author: Brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
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
ms.openlocfilehash: a312068d5c8c574e7b069263cf37e3b855810e4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72790108"
---
# <a name="add-suggesters-to-an-index-for-typeahead-in-azure-cognitive-search"></a>Lägga till förslagsskrivare i ett index för typeahead i Azure Cognitive Search

I Azure Cognitive Search baseras "sök-som-du-typ" eller typeahead-funktioner på en **förslagsförd** konstruktion som du lägger till i ett [sökindex](search-what-is-an-index.md). Det är en lista över ett eller flera fält som du vill ha typhuvud aktiverat för.

En förslagsställare stöder två typeahead-varianter: *komplettera automatiskt*, som kompletterar termen eller frasen du skriver, och förslag som *returnerar* en kort lista med matchande dokument.  

Följande skärmbild, från appen [Skapa din första app i C#-exemplet,](tutorial-csharp-type-ahead-and-suggestions.md) illustrerar typeahead. Komplettera automatiskt förutser vad användaren kan skriva i sökrutan. Faktisk ingång är "tw", som komplettera automatiskt slutar med "i", lösa som "twin" som den potentiella söktermen. Förslag visualiseras i listrutan. Förslag på förslag kan du visa alla delar av ett dokument som bäst beskriver resultatet. I det här exemplet är förslagen hotellnamn. 

![Visuell jämförelse av automatisk komplettering och föreslagna frågor](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuell jämförelse av automatisk komplettering och föreslagna frågor")

För att implementera dessa beteenden i Azure Cognitive Search finns det en index- och frågekomponent. 

+ Lägg till en förslagsman i ett index i indexet. Du kan använda portalen, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Resten av den här artikeln är inriktad på att skapa en förslagsman. 

+ Anropa ett av de [API:er som anges nedan](#how-to-use-a-suggester)i frågebegäran.

Stöd för söktyp är aktiverat per fält. Du kan implementera båda typeahead-beteenden i samma söklösning om du vill ha en upplevelse som liknar den som anges i skärmbilden. Båda begärandena riktar sig till *dokumentsamlingen* med specifikt index och svar returneras efter att en användare har angett minst en indatasträng med tre tecken.

## <a name="create-a-suggester"></a>Skapa en förslagsställare

Även om en förslagsansvarig har flera egenskaper är det i första hand en samling fält som du aktiverar en typeahead-upplevelse för. En reseapp kanske till exempel vill aktivera typrubriksökning på destinationer, städer och attraktioner. Därför skulle alla tre fälten gå i fältsamlingen.

Om du vill skapa en förslagsfören lägger du till ett i ett indexschema. Du kan ha en förslagsman i ett index (närmare bestämt en förslagsman i förslagssamlingen). 

### <a name="when-to-create-a-suggester"></a>När du ska skapa en förslagsman

Den bästa tiden att skapa en förslagsman är när du också skapar själva fältdefinitionen.

Om du försöker skapa en förslagsställare med befintliga fält kommer API:et inte att tillåta det. Typeahead-text skapas under indexeringen, när partiella termer i två eller flera teckenkombinationer tokeniseras tillsammans med hela termer. Med tanke på att befintliga fält redan är tokeniserade måste du återskapa indexet om du vill lägga till dem i en förslagsförenare. Mer information om omindexering finns i [Så här återskapar du ett Azure Cognitive Search-index](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Skapa med REST API

Lägg till förslagsföreare i REST API via [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

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


### <a name="create-using-the-net-sdk"></a>Skapa med hjälp av .NET SDK

Definiera ett [Suggester-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)i C#. `Suggesters`är en samling, men den kan bara ta ett objekt. 

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

### <a name="property-reference"></a>Egenskapsreferens

|Egenskap      |Beskrivning      |
|--------------|-----------------|
|`name`        |Namnet på förslagsen.|
|`searchMode`  |Den strategi som används för att söka efter kandidatfraser. Det enda läge `analyzingInfixMatching`som för närvarande stöds är , som utför flexibel matchning av fraser i början eller i mitten av meningar.|
|`sourceFields`|En lista över ett eller flera fält som är källan till innehållet för förslag. Fälten måste `Edm.String` vara `Collection(Edm.String)`av typen och . Om en analysator anges i fältet måste den vara en namngiven analysator från [den här listan](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (inte en anpassad analysator).<p/>Det bästa är att ange endast de fält som lämpar sig för ett förväntat och lämpligt svar, oavsett om det är en slutförd sträng i ett sökfält eller en listruta.<p/>Ett hotellnamn är en bra kandidat eftersom det har precision. Utförliga fält som beskrivningar och kommentarer är för täta. På samma sätt är repetitiva fält, till exempel kategorier och taggar, mindre effektiva. I exemplen inkluderar vi "kategori" ändå för att visa att du kan inkludera flera fält. |

### <a name="analyzer-restrictions-for-sourcefields-in-a-suggester"></a>Analysatorbegränsningar för sourceFields i en förslagskälla

Azure Cognitive Search analyserar fältinnehållet för att aktivera frågor om enskilda termer. Förslagsställare kräver prefix som ska indexeras utöver fullständiga termer, vilket kräver ytterligare analys över källfälten. Anpassade analysatorkonfigurationer kan kombinera någon av de olika tokenizers och filter, ofta på ett sätt som skulle göra producera prefix som krävs för förslag omöjligt. Därför förhindrar Azure Cognitive Search att fält med anpassade analysatorer inkluderas i en förslagsskrivare.

> [!NOTE] 
>  Om du behöver kringgå ovanstående begränsning använder du två separata fält för samma innehåll. Detta gör att ett av fälten kan ha en förslagsman, medan det andra kan ställas in med en anpassad analysatorkonfiguration.

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester-in-a-query"></a>Använda en förslagsman i en fråga

När en förslagsman har skapats anropar du lämpligt API i frågelogiken för att anropa funktionen. 

+ [REST API-förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Rest-API för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Komplettera automatiskt medhttpMessagesAsync-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

API-användning illustreras i följande anrop till REST API för komplettera automatiskt. Det finns två takeaways från detta exempel. För det första, som med alla frågor, är åtgärden mot dokumentsamlingen i ett index. För det andra kan du lägga till frågeparametrar. Många frågeparametrar är gemensamma för båda API:erna, men listan är olika för var och en.

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs/autocomplete?[query parameters]  
api-key: [admin or query key]
```

Om en förslagsförenare inte definieras i indexet misslyckas ett anrop till automatisk komplettering eller förslag.

## <a name="sample-code"></a>Exempelkod

+ [Skapa din första app i C#](tutorial-csharp-type-ahead-and-suggestions.md) exempel visar en förslagsförrättning konstruktion, föreslagna frågor, komplettera automatiskt och fasterad navigering. Det här kodexemplet körs på en Azure Cognitive Search-tjänst i begränsat läge och använder ett förinlät Hotellindex så allt du behöver göra är att trycka på F5 för att köra programmet. Ingen prenumeration eller inloggning är nödvändig.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) är ett äldre urval som innehåller både C# och Java-kod. Det visar också en förslagsfören konstruktion, föreslagna frågor, komplettera automatiskt och fasterad navigering. Det här kodexemplet använder de värdbaserade EXEMPELDATA FÖR [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 


## <a name="next-steps"></a>Nästa steg

Vi rekommenderar följande exempel för att se hur begäranden formuleras.

> [!div class="nextstepaction"]
> [Förslag och komplettera automatiskt](search-autocomplete-tutorial.md) 

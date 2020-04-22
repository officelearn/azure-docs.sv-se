---
title: Skapa en förslagsställare
titleSuffix: Azure Cognitive Search
description: Aktivera frågeåtgärder för typ framåt i Azure Cognitive Search genom att skapa förslagsförespråkare och formulera begäranden som anropar automatiskt kompatibel eller efterföljande frågetermer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 7eb2988628d60fa72c7d83b81a58a1e0fae5de33
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770087"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Skapa en förslagsman för att aktivera komplettera automatiskt och föreslagna resultat i en fråga

I Azure Cognitive Search aktiveras "sök-som-du-typ" genom en **förslagsförd** konstruera som läggs till i ett [sökindex](search-what-is-an-index.md). En förslagsställare stöder två upplevelser: *komplettera automatiskt*, som slutför en partiell indata för en hel termfråga, och *förslag* som inbjuder klickar sig vidare till en viss matchning. Komplettera automatiskt skapar en fråga. Förslag ger ett matchande dokument.

Följande skärmbild från [Skapa din första app i C#](tutorial-csharp-type-ahead-and-suggestions.md) illustrerar båda. Komplettera automatiskt förutser en potentiell term, efterbehandling "tw" med "i". Förslag är minisökresultat, där ett fält som hotellnamn representerar ett matchande hotellsökdokument från indexet. Om du vill ha förslag kan du visa alla fält som innehåller beskrivande information.

![Visuell jämförelse av automatisk komplettering och föreslagna frågor](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuell jämförelse av automatisk komplettering och föreslagna frågor")

Du kan använda dessa funktioner separat eller tillsammans. För att implementera dessa beteenden i Azure Cognitive Search finns det en index- och frågekomponent. 

+ Lägg till en förslagsman i ett index i indexet. Du kan använda portalen, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Resten av den här artikeln är inriktad på att skapa en förslagsman.

+ Anropa ett av de [API:er som anges nedan](#how-to-use-a-suggester)i frågebegäran.

Stöd för söktyp är aktiverat per fält för strängfält. Du kan implementera båda typeahead-beteenden i samma söklösning om du vill ha en upplevelse som liknar den som anges i skärmbilden. Båda begärandena riktar sig till *dokumentsamlingen* med specifikt index och svar returneras efter att en användare har angett minst en indatasträng med tre tecken.

## <a name="what-is-a-suggester"></a>Vad är en förslagsman?

En förslagsställare är en intern datastruktur som stöder sök-som-du-typ beteenden genom att lagra prefix för matchning på partiella frågor. Precis som med tokeniserade termer lagras prefix i inverterade index, ett för varje fält som anges i en förslagsfältssamling.

## <a name="define-a-suggester"></a>Definiera en förslagsman

Om du vill skapa en förslagsförrättning lägger du till ett i ett [indexschema](https://docs.microsoft.com/rest/api/searchservice/create-index) och [anger varje egenskap](#property-reference). Den bästa tiden att skapa en förslagsman är när du också definierar det fält som ska använda den.

+ Använd endast strängfält

+ Använd standardanalysatorn för Lucene (`"analyzer": null`) eller en [språkanalysator](index-add-language-analyzers.md) (till `"analyzer": "en.Microsoft"`exempel) på fältet

### <a name="choose-fields"></a>Välj fält

Även om en förslagsansvarig har flera egenskaper är det i första hand en samling strängfält som du aktiverar en sökupplevelse för dig. Det finns en förslagsställare för varje index, så förslagslistan måste innehålla alla fält som bidrar med innehåll för både förslag och komplettera automatiskt.

Komplettera automatiskt drar nytta av en större grupp fält att dra från eftersom det ytterligare innehållet har större potential för slutförande av termen.

Förslag ger å andra sidan bättre resultat när ditt fältval är selektivt. Kom ihåg att förslaget är en proxy för ett sökdokument så att du vill ha fält som bäst representerar ett enda resultat. Namn, titlar eller andra unika fält som skiljer mellan flera matchningar fungerar bäst. Om fälten består av repetitiva värden består förslagen av identiska resultat och en användare vet inte vilken som ska klickas.

Om du vill uppfylla både sök-som-du-typ-upplevelser lägger du till alla fält som du behöver för automatisk komplettering, men använder sedan **$select,** **$top,** **$filter**och **sökfält** för att styra resultat för förslag.

### <a name="choose-analyzers"></a>Välj analysatorer

Ditt val av en analysator avgör hur fält tokeniseras och därefter föregås. För en avstavad sträng som "kontextkänslig" resulterar till exempel dessa tokenkombinationer med hjälp av en språkanalysator: "kontext", "känslig", "kontextkänslig". Om du hade använt Lucene-standardanalysatorn skulle den avstavade strängen inte existera. 

När du utvärderar analysatorer bör du överväga att använda [API:et](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) analysera text för att få insikt i hur termer tokeniseras och därefter föregås. När du har byggt ett index kan du prova olika analysatorer på en sträng för att visa tokenutdata.

Fält som använder [anpassade analysatorer](index-add-custom-analyzers.md) eller [fördefinierade analysatorer](index-add-custom-analyzers.md#predefined-analyzers-reference) (med undantag för standard Lucene) tillåts uttryckligen för att förhindra dåliga resultat.

> [!NOTE]
> Om du behöver kringgå analysbegränsningen, till exempel om du behöver ett nyckelord eller ngram-analysator för vissa frågescenarier, bör du använda två separata fält för samma innehåll. Detta gör att ett av fälten kan ha en förslagsman, medan det andra kan ställas in med en anpassad analysatorkonfiguration.

### <a name="when-to-create-a-suggester"></a>När du ska skapa en förslagsman

Den bästa tiden att skapa en förslagsman är när du också skapar själva fältdefinitionen.

Om du försöker skapa en förslagsställare med befintliga fält kommer API:et inte att tillåta det. Prefix genereras under indexeringen, när partiella termer i två eller flera teckenkombinationer tokeniseras tillsammans med hela termer. Med tanke på att befintliga fält redan är tokeniserade måste du återskapa indexet om du vill lägga till dem i en förslagsförenare. Mer information finns i [Så här återskapar du ett Azure Cognitive Search-index](search-howto-reindex.md).

## <a name="create-using-rest"></a>Skapa med REST

Lägg till förslagsföreare i REST API via [Skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) eller [uppdatera index](https://docs.microsoft.com/rest/api/searchservice/update-index). 

  ```json
  {
    "name": "hotels-sample-index",
    "fields": [
      . . .
          {
              "name": "HotelName",
              "type": "Edm.String",
              "facetable": false,
              "filterable": false,
              "key": false,
              "retrievable": true,
              "searchable": true,
              "sortable": false,
              "analyzer": "en.microsoft",
              "indexAnalyzer": null,
              "searchAnalyzer": null,
              "synonymMaps": [],
              "fields": []
          },
    ],
    "suggesters": [
      {
        "name": "sg",
        "searchMode": "analyzingInfixMatching",
        "sourceFields": ["HotelName"]
      }
    ],
    "scoringProfiles": [
      . . .
    ]
  }
  ```

## <a name="create-using-net"></a>Skapa med .NET

Definiera ett [Suggester-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet)i C#. `Suggesters`är en samling, men den kan bara ta ett objekt. 

```csharp
private static void CreateHotelsIndex(SearchServiceClient serviceClient)
{
    var definition = new Index()
    {
        Name = "hotels-sample-index",
        Fields = FieldBuilder.BuildForType<Hotel>(),
        Suggesters = new List<Suggester>() {new Suggester()
            {
                Name = "sg",
                SourceFields = new string[] { "HotelName", "Category" }
            }}
    };

    serviceClient.Indexes.Create(definition);

}
```

## <a name="property-reference"></a>Egenskapsreferens

|Egenskap      |Beskrivning      |
|--------------|-----------------|
|`name`        |Namnet på förslagsen.|
|`searchMode`  |Den strategi som används för att söka efter kandidatfraser. Det enda läge `analyzingInfixMatching`som för närvarande stöds är , som för närvarande matchar i början av en term.|
|`sourceFields`|En lista över ett eller flera fält som är källan till innehållet för förslag. Fälten måste `Edm.String` vara `Collection(Edm.String)`av typen och . Om en analysator anges i fältet måste den vara en namngiven analysator från [den här listan](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (inte en anpassad analysator).<p/> Det bästa är att ange endast de fält som lämpar sig för ett förväntat och lämpligt svar, oavsett om det är en slutförd sträng i ett sökfält eller en listruta.<p/>Ett hotellnamn är en bra kandidat eftersom det har precision. Utförliga fält som beskrivningar och kommentarer är för täta. På samma sätt är repetitiva fält, till exempel kategorier och taggar, mindre effektiva. I exemplen inkluderar vi "kategori" ändå för att visa att du kan inkludera flera fält. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Använda en förslagshållare

En förslagsman används i en fråga. När en förslagsfören har skapats anropar du någon av följande API:er för en sökupplevelse som du skriver:

+ [REST API-förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Rest-API för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Komplettera automatiskt medhttpMessagesAsync-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

I ett sökprogram bör klientkoden utnyttja ett bibliotek som [automatisk komplettering av jQuery UI](https://jqueryui.com/autocomplete/) för att samla in den partiella frågan och tillhandahålla matchningen. Mer information om den här uppgiften finns i [Lägga till komplettera automatiskt eller föreslagna resultat i klientkoden](search-autocomplete-tutorial.md).

API-användning illustreras i följande anrop till REST API för komplettera automatiskt. Det finns två takeaways från detta exempel. För det första, som med alla frågor, är åtgärden mot dokumentsamlingen för ett index och frågan innehåller en **sökparameter,** som i det här fallet tillhandahåller den partiella frågan. För det andra måste du lägga till **suggesterName** i begäran. Om en förslagsförenare inte definieras i indexet misslyckas ett anrop till automatisk komplettering eller förslag.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Exempelkod

+ [Skapa din första app i C# (lektion 3 - Lägg till sök-som-du-typ)](tutorial-csharp-type-ahead-and-suggestions.md) exempel visar en förslagsförrättning konstruktion, föreslagna frågor, komplettera automatiskt och fakulterad navigering. Det här kodexemplet körs på en Azure Cognitive Search-tjänst i begränsat läge och använder ett förinlät Hotellindex så allt du behöver göra är att trycka på F5 för att köra programmet. Ingen prenumeration eller inloggning är nödvändig.

+ [DotNetHowToAutocomplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete) är ett äldre urval som innehåller både C# och Java-kod. Det visar också en förslagsfören konstruktion, föreslagna frågor, komplettera automatiskt och fasterad navigering. Det här kodexemplet använder de värdbaserade EXEMPELDATA FÖR [NYCJobs.](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) 

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar följande artikel om du vill veta mer om hur formuleringen begärs.

> [!div class="nextstepaction"]
> [Lägga till komplettera automatiskt och förslag i klientkoden](search-autocomplete-tutorial.md) 

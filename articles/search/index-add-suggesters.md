---
title: Skapa en förslagsställare
titleSuffix: Azure Cognitive Search
description: Aktivera frågeåtgärder för typ framåt i Azure Cognitive Search genom att skapa förslagsförespråkare och formulera begäranden som anropar automatiskt kompatibel eller efterföljande frågetermer.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/10/2020
ms.openlocfilehash: d40d4cfe1b86448f1e8df307013905d69f203dcd
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261065"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Skapa en förslagsman för att aktivera komplettera automatiskt och föreslagna resultat i en fråga

I Azure Cognitive Search aktiveras "sök-som-du-typ" genom en **förslagsförd** konstruera som läggs till i ett [sökindex](search-what-is-an-index.md). En förslagsställare stöder två upplevelser: *komplettera automatiskt*, som kompletterar termen eller frasen, och förslag som *returnerar* en kort lista med matchande dokument.  

Följande skärmbild från [Skapa din första app i C#](tutorial-csharp-type-ahead-and-suggestions.md) illustrerar båda. Komplettera automatiskt förutser en potentiell term, efterbehandling "tw" med "i". Förslag är minisökresultat, där ett fält som hotellnamn representerar ett matchande hotellsökdokument från indexet. Om du vill ha förslag kan du visa alla fält som innehåller beskrivande information.

![Visuell jämförelse av automatisk komplettering och föreslagna frågor](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuell jämförelse av automatisk komplettering och föreslagna frågor")

Du kan använda dessa funktioner separat eller tillsammans. För att implementera dessa beteenden i Azure Cognitive Search finns det en index- och frågekomponent. 

+ Lägg till en förslagsman i ett index i indexet. Du kan använda portalen, [REST API](https://docs.microsoft.com/rest/api/searchservice/create-index)eller [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggester?view=azure-dotnet). Resten av den här artikeln är inriktad på att skapa en förslagsman.

+ Anropa ett av de [API:er som anges nedan](#how-to-use-a-suggester)i frågebegäran.

Stöd för söktyp är aktiverat per fält för strängfält. Du kan implementera båda typeahead-beteenden i samma söklösning om du vill ha en upplevelse som liknar den som anges i skärmbilden. Båda begärandena riktar sig till *dokumentsamlingen* med specifikt index och svar returneras efter att en användare har angett minst en indatasträng med tre tecken.

## <a name="what-is-a-suggester"></a>Vad är en förslagsman?

En förslagsställare är en datastruktur som stöder sök-som-du-typ beteenden genom att lagra prefix för matchning på partiella frågor. I likhet med tokeniserade termer lagras prefix i inverterade index, ett för varje fält som anges i en förslagsfältssamling.

När du skapar prefix har en förslagsställare en egen analyskedja, liknande den som används för fulltextsökning. Till skillnad från analys i fulltextsökning kan dock en förslagsförenare bara arbeta över fält som använder standard Lucene-analysatorn (standard) eller en [språkanalysator](index-add-language-analyzers.md). Fält som använder [anpassade analysatorer](index-add-custom-analyzers.md) eller [fördefinierade analysatorer](index-add-custom-analyzers.md#predefined-analyzers-reference) (med undantag för standard Lucene) tillåts uttryckligen för att förhindra dåliga resultat.

> [!NOTE]
> Om du behöver kringgå analysatorbegränsningen använder du två separata fält för samma innehåll. Detta gör att ett av fälten kan ha en förslagsman, medan det andra kan ställas in med en anpassad analysatorkonfiguration.

## <a name="define-a-suggester"></a>Definiera en förslagsman

Även om en förslagsansvarig har flera egenskaper är det i första hand en samling fält som du aktiverar en sökupplevelse som du skriver. En reseapp kanske till exempel vill aktivera komplettera automatiskt på destinationer, städer och attraktioner. Därför skulle alla tre fälten gå i fältsamlingen.

Om du vill skapa en förslagsfören lägger du till ett i ett indexschema. Du kan ha en förslagsman i ett index (närmare bestämt en förslagsman i förslagssamlingen). En förslagsman tar en lista med fält. 

+ Förslag på förslag genom att välja fält som bäst representerar ett enda resultat. Namn, titlar eller andra unika fält som skiljer mellan dokument fungerar bäst. Om fälten består av liknande eller identiska värden kommer förslagen att bestå av identiska resultat och en användare vet inte vilken som ska klickas.

+ Kontrollera att varje fält `sourceFields` i förslagslistan använder antingen standardmodellen`"analyzer": null`Lucene () eller `"analyzer": "en.Microsoft"`en [språkanalysator](index-add-language-analyzers.md) (till exempel ). 

  Ditt val av en analysator avgör hur fält tokeniseras och därefter föregås. För en avstavad sträng som "kontextkänslig" resulterar till exempel dessa tokenkombinationer med hjälp av en språkanalysator: "kontext", "känslig", "kontextkänslig". Om du hade använt Lucene-standardanalysatorn skulle den avstavade strängen inte existera.

> [!TIP]
> Överväg att använda [API:et](https://docs.microsoft.com/rest/api/searchservice/test-analyzer) analysera text för att få insikt i hur termer tokeniseras och därefter föregås. När du har byggt ett index kan du prova olika analysatorer på en sträng för att visa de token som det avger.

### <a name="when-to-create-a-suggester"></a>När du ska skapa en förslagsman

Den bästa tiden att skapa en förslagsman är när du också skapar själva fältdefinitionen.

Om du försöker skapa en förslagsställare med befintliga fält kommer API:et inte att tillåta det. Prefix genereras under indexeringen, när partiella termer i två eller flera teckenkombinationer tokeniseras tillsammans med hela termer. Med tanke på att befintliga fält redan är tokeniserade måste du återskapa indexet om du vill lägga till dem i en förslagsförenare. Mer information finns i [Så här återskapar du ett Azure Cognitive Search-index](search-howto-reindex.md).

### <a name="create-using-the-rest-api"></a>Skapa med REST API

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

### <a name="create-using-the-net-sdk"></a>Skapa med hjälp av .NET SDK

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

### <a name="property-reference"></a>Egenskapsreferens

|Egenskap      |Beskrivning      |
|--------------|-----------------|
|`name`        |Namnet på förslagsen.|
|`searchMode`  |Den strategi som används för att söka efter kandidatfraser. Det enda läge `analyzingInfixMatching`som för närvarande stöds är , som utför flexibel matchning av fraser i början eller i mitten av meningar.|
|`sourceFields`|En lista över ett eller flera fält som är källan till innehållet för förslag. Fälten måste `Edm.String` vara `Collection(Edm.String)`av typen och . Om en analysator anges i fältet måste den vara en namngiven analysator från [den här listan](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.analyzername?view=azure-dotnet) (inte en anpassad analysator).<p/> Det bästa är att ange endast de fält som lämpar sig för ett förväntat och lämpligt svar, oavsett om det är en slutförd sträng i ett sökfält eller en listruta.<p/>Ett hotellnamn är en bra kandidat eftersom det har precision. Utförliga fält som beskrivningar och kommentarer är för täta. På samma sätt är repetitiva fält, till exempel kategorier och taggar, mindre effektiva. I exemplen inkluderar vi "kategori" ändå för att visa att du kan inkludera flera fält. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Använda en förslagshållare

En förslagsman används i en fråga. När en förslagsman har skapats anropar du lämpligt API i frågelogiken för att anropa funktionen. 

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

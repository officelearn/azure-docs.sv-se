---
title: Skapa en förslagsställare
titleSuffix: Azure Cognitive Search
description: Aktivera åtgärder för typ i förväg i Azure Kognitiv sökning genom att skapa förslag och utforma begär Anden som anropar Autoavsluta eller automatiska förslag på sökord.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ae25c63e9c6e3bf6ad363cde9eb641703562811
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360028"
---
# <a name="create-a-suggester-to-enable-autocomplete-and-suggested-results-in-a-query"></a>Skapa en förslags ställare för att aktivera Autoavsluta och föreslagna resultat i en fråga

I Azure Kognitiv sökning aktive ras "Sök som-du-typ" via en **förslags** konstruktion som läggs till i ett [sökindex](search-what-is-an-index.md). En förslags ställare stöder två upplevelser: *komplettera automatiskt* , som slutför indatamängden för en fullständig term fråga och *förslag* som bjuder in genom att klicka igenom till en viss matchning. Funktionen Komplettera automatiskt skapar en fråga. Förslag skapar ett matchande dokument.

Följande skärm bild från [skapa din första app i C#](tutorial-csharp-type-ahead-and-suggestions.md) visar båda. Autoavsluta förväntar sig en möjlig term, och avslutar "TW" med "in". Förslag är små Sök resultat, där ett fält som ett hotell namn representerar ett matchande hotell Sök dokument från indexet. För förslag kan du Visa alla fält som innehåller beskrivande information.

![Visuell jämförelse av Autoavsluta och föreslagna frågor](./media/index-add-suggesters/hotel-app-suggestions-autocomplete.png "Visuell jämförelse av Autoavsluta och föreslagna frågor")

Du kan använda dessa funktioner separat eller tillsammans. Det finns ett index och en frågekomponent-komponent för att implementera dessa beteenden i Azure Kognitiv sökning. 

+ I indexet lägger du till en förslags pekare till ett index. Du kan använda portalen, [REST API](/rest/api/searchservice/create-index)eller [.NET SDK](/dotnet/api/microsoft.azure.search.models.suggester). Resten av den här artikeln fokuserar på att skapa en förslags ställare.

+ Anropa ett av [API: erna som anges nedan](#how-to-use-a-suggester)i förfrågan.

Stöd för sökning efter typ har Aktiver ATS baserat på fältet per fält för sträng fält. Du kan implementera båda typeahead-beteenden inom samma Sök lösning om du vill ha en upplevelse som liknar den som anges i skärm bilden. Båda begär Anden mål *dokument* samlingen för ett särskilt index och svar returneras efter att en användare har angett minst en sträng med tre tecken.

## <a name="what-is-a-suggester"></a>Vad är en förslags ställare?

En förslags ställare är en intern data struktur som stöder sökning efter typ genom att lagra prefix för matchning på ofullständiga frågor. Precis som med token-termer lagras prefix i inverterade index, en för varje fält som anges i en förslags fält samling.

## <a name="define-a-suggester"></a>Definiera en förslags ställare

Om du vill skapa en förslags pekare, lägger du till ett i ett [index schema](/rest/api/searchservice/create-index) och [anger varje egenskap](#property-reference). Det bästa sättet att skapa en förslags ställare är när du även definierar det fält som ska använda det.

+ Använd endast sträng fält

+ Använd standard standard Lucene Analyzer ( `"analyzer": null` ) eller en [språk analys](index-add-language-analyzers.md) (till exempel `"analyzer": "en.Microsoft"` ) i fältet

Om du försöker skapa en förslags ställare med redan existerande fält kommer API: et att tillåta det. Prefix skapas vid indexering, om del termer i två eller flera teckenkombinationer är token i hela termer. Eftersom befintliga fält redan har tokens, måste du återskapa indexet om du vill lägga till dem i en förslags ställare. Mer information finns i [så här återskapar du ett Azure kognitiv sökning-index](search-howto-reindex.md).

### <a name="choose-fields"></a>Välj fält

Även om en förslags ställare har flera egenskaper, är det i första hand en samling sträng fält för vilka du aktiverar en sökning efter typ. Det finns en förslags ställare för varje index, så att listan över förslag måste innehålla alla fält som bidrar med innehåll för både förslag och Autoavsluta.

Autoavsluta-förmåner från en större pool med fält att rita från eftersom det ytterligare innehållet har mer långsiktigt slut för ande.

Förslag ger å andra sidan bättre resultat när valet av fält är selektivt. Kom ihåg att förslaget är en proxy för ett Sök dokument, så att du vill att fält som bäst representerar ett enda resultat. Namn, titlar eller andra unika fält som skiljer sig mellan flera matchningar fungerar bäst. Om fälten består av upprepade värden, består förslagen av identiska resultat och en användare vet inte vilken av dem som ska klicka.

Lägg till alla fält som du behöver för Komplettera automatiskt, men Använd **$Select** , **$Top** , **$filter** och **searchFields** för att kontrol lera resultaten för förslag.

### <a name="choose-analyzers"></a>Välj analys verktyg

Ditt val av analys bestämmer hur fälten ska vara token och sedan föregås. För en avstavad sträng som "Sammanhangs känslig", används till exempel följande token för att använda en språk analys: "context", "känslig", "kontext känslig". Hade du använt standard Lucene Analyzer, den avstavade strängen finns inte. 

När du utvärderar analyserare bör du överväga att använda [API: et för analys av text](/rest/api/searchservice/test-analyzer) för att lära dig mer om hur termerna är tokens och senare. När du skapar ett index kan du prova olika analys verktyg i en sträng för att visa utdata för token.

Fält som använder [anpassade analyserare](index-add-custom-analyzers.md) eller [fördefinierade analys](index-add-custom-analyzers.md#predefined-analyzers-reference) verktyg (med undantag för standard Lucene) är uttryckligen inte tillåtna för att förhindra dåliga resultat.

> [!NOTE]
> Om du behöver använda ett nyckelord eller ngram Analyzer för vissa fråge scenarier, bör du använda två separata fält för samma innehåll. Detta gör att ett av fälten kan ha en förslags ställare, medan det andra kan konfigureras med en anpassad Analyzer-konfiguration.

## <a name="create-using-rest"></a>Skapa med REST

Lägg till förslag via [skapa index](/rest/api/searchservice/create-index) eller [uppdatera index](/rest/api/searchservice/update-index)i REST API. 

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

Definiera ett [förslags objekt](/dotnet/api/microsoft.azure.search.models.suggester)i C#. `Suggesters` är en samling, men det kan bara ta ett objekt. 

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

## <a name="property-reference"></a>Egenskaps referens

|Egenskap      |Beskrivning      |
|--------------|-----------------|
|`name`        |Förslagets namn.|
|`searchMode`  |Strategin som används för att söka efter kandidat fraser. Det enda läge som stöds för närvarande är `analyzingInfixMatching` , som för närvarande matchar i början av en term.|
|`sourceFields`|En lista med ett eller flera fält som är källan till innehållet för förslag. Fält måste vara av typen `Edm.String` och `Collection(Edm.String)` . Om en analys anges i fältet måste det vara en namngiven analys från [den här listan](/dotnet/api/microsoft.azure.search.models.analyzername) (inte en anpassad analys).<p/> Vi rekommenderar att du bara anger de fält som lånar ut sig till ett förväntat och lämpligt svar, oavsett om det är en slutförd sträng i ett sökfält eller i en nedrullningsbar listruta.<p/>Ett hotell namn är en bra kandidat eftersom det har precision. Utförliga fält som beskrivningar och kommentarer är för kompakta. På samma sätt är upprepade fält, till exempel kategorier och taggar, mindre effektiva. I exemplen inkluderar vi "Category" ändå för att demonstrera att du kan inkludera flera fält. |

<a name="how-to-use-a-suggester"></a>

## <a name="use-a-suggester"></a>Använd en förslags ställare

En förslags ställare används i en fråga. När en förslags ställare har skapats anropar du en av följande API: er för en sökning efter typ:

+ [Förslag REST API](/rest/api/searchservice/suggestions)
+ [Autoavsluta-REST API](/rest/api/searchservice/autocomplete)
+ [SuggestWithHttpMessagesAsync-metod](/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync)
+ [AutocompleteWithHttpMessagesAsync-metod](/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync)

I ett sökprogram bör klient koden använda ett bibliotek som [JQUERY UI komplettera automatiskt](https://jqueryui.com/autocomplete/) för att samla in den partiella frågan och ange matchningen. Mer information om den här uppgiften finns i [lägga till komplettera automatiskt eller föreslagna resultat till klient koden](search-autocomplete-tutorial.md).

API-användningen illustreras i följande anrop till REST API för automatisk komplettering. Det finns två takeaways i det här exemplet. För det första, som med alla frågor, är åtgärden mot dokument samlingen för ett index och frågan innehåller en **Sök** parameter, som i det här fallet innehåller den partiella frågan. Därefter måste du lägga till **suggesterName** i begäran. Om en förslags ställare inte har definierats i indexet kommer ett anrop till komplettera automatiskt eller förslag att Miss Missing.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2020-06-30
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

## <a name="sample-code"></a>Exempelkod

+ [Skapa din första app i C# (Lektion 3 – Lägg till sökning efter typ)](tutorial-csharp-type-ahead-and-suggestions.md) visar en förslags konstruktion, föreslagna frågor, Autoavsluta och fasett-navigering. Det här kod exemplet körs på en sandbox Azure Kognitiv sökning-tjänst och använder ett förinställt hotell index så att allt du behöver göra är att trycka på F5 för att köra programmet. Ingen prenumeration eller inloggning är nödvändig.

## <a name="next-steps"></a>Nästa steg

Vi rekommenderar följande artikel för att lära dig mer om hur begär ande formulering.

> [!div class="nextstepaction"]
> [Lägg till komplettera automatiskt och förslag till klient kod](search-autocomplete-tutorial.md)
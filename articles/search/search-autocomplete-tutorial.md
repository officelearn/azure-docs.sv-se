---
title: Lägga till komplettera automatiskt och förslag i en sökruta
titleSuffix: Azure Cognitive Search
description: Aktivera sök-som-du-typ frågeåtgärder i Azure Cognitive Search genom att skapa förslagsställare och formulera begäranden som automatiskt komplettera en sökruta med färdiga termer eller fraser. Du kan också returnera föreslagna matchningar.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 60e9a435d705ee0fee6509e92cdcb056ac7ab609
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758121"
---
# <a name="add-autocomplete-and-suggestions-to-client-apps"></a>Lägga till komplettera automatiskt och förslag i klientappar

Search-as-you-type är en vanlig teknik för att förbättra produktiviteten i användarinitierade frågor. I Azure Cognitive Search stöds den här upplevelsen genom *komplettera automatiskt*, som avslutar en term eller fras baserad på partiell indata (fyller i "micro" med "microsoft"). Ett annat formulär är *förslag:* en kort lista med matchande dokument (returnera boktitlar med ett ID så att du kan länka till en detaljsida). Både komplettera automatiskt och förslag bygger på en matchning i indexet. Tjänsten erbjuder inte frågor som ger noll resultat.

Om du vill implementera dessa upplevelser i Azure Cognitive Search behöver du:

+ En *förslagsenare* på baksidan.
+ En *fråga* som anger API för automatisk [komplettering](https://docs.microsoft.com/rest/api/searchservice/suggestions) eller förslag på begäran. [Autocomplete](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
+ En *gränssnittskontroll* för att hantera interaktioner med sök-som du-typ i klientappen. Vi rekommenderar att du använder ett befintligt JavaScript-bibliotek för detta ändamål.

I Azure Cognitive Search hämtas automatiskt tillslutna frågor och föreslagna resultat från sökindexet, från valda fält som du har registrerat med en förslagsställare. En förslagsställare är en del av indexet och anger vilka fält som ska tillhandahålla innehåll som antingen slutför en fråga, föreslår ett resultat eller gör båda. När indexet skapas och läses in skapas en förslagsställardatastruktur internt för att lagra prefix som används för matchning på partiella frågor. För förslag, att välja lämpliga områden som är unika, eller åtminstone inte repetitiva, är avgörande för upplevelsen. Mer information finns i [Skapa en förslagsman](index-add-suggesters.md).

Resten av den här artikeln är inriktad på frågor och klientkod. Den använder JavaScript och C # för att illustrera viktiga punkter. REST API-exempel används för att kortfattat presentera varje åtgärd. Länkar till heltäckande kodexempel finns i [Nästa steg](#next-steps).

## <a name="set-up-a-request"></a>Konfigurera en begäran

Element i en begäran inkluderar en av API:erna för sökning som du skriver, en partiell fråga och en förslagsställare. Följande skript illustrerar komponenter i en begäran med hjälp av REST API för automatisk komplettering som ett exempel.

```http
POST /indexes/myxboxgames/docs/autocomplete?search&api-version=2019-05-06
{
  "search": "minecraf",
  "suggesterName": "sg"
}
```

**Förslagsställarens namn** ger dig de förslagsställare som används för att slutföra termer eller förslag. Särskilt för förslag bör fältlistan bestå av dem som erbjuder tydliga val bland matchande resultat. På en webbplats som säljer dataspel kan fältet vara spelets titel.

**Sökparametern** tillhandahåller den partiella frågan, där tecken matas till frågebegäran via kontrollen jQuery Autocomplete. I exemplet ovan är "minecraf" en statisk illustration av vad kontrollen kan ha passerat in.

API:erna ställer inte minimikrav på den partiella frågan. det kan vara så lite som en karaktär. JQuery Autocomplete ger dock en minsta längd. Minst två eller tre tecken är typiskt.

Matchningar är i början av en term var som helst i indatasträngen. Med tanke på "den snabba bruna räven", både komplettera automatiskt och förslag kommer att matcha på partiella versioner av "den", "snabb", "brun" eller "räv" men inte på partiell infix termer som "rown" eller "ox". Dessutom anger varje match utrymme för utvidgningar nedströms. En partiell fråga om "quick br" kommer att matcha på "quick brown" eller "snabbt bröd", men varken "brun" eller "bröd" i sig skulle matcha om inte "snabb" föregår dem.

### <a name="apis-for-search-as-you-type"></a>API:er för sök-som-du-typ

Följ dessa länkar för referenssidorna REST och .NET SDK:

+ [REST API-förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) 
+ [Rest-API för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) 
+ [SuggestWithHttpMessagesAsync-metod](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.suggestwithhttpmessagesasync?view=azure-dotnet)
+ [Komplettera automatiskt medhttpMessagesAsync-metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.idocumentsoperations.autocompletewithhttpmessagesasync?view=azure-dotnet&viewFallbackFrom=azure-dotnet)

## <a name="structure-a-response"></a>Strukturera ett svar

Svar för komplettera automatiskt och förslag är vad du kan förvänta dig för mönstret: [Komplettera automatiskt](https://docs.microsoft.com/rest/api/searchservice/autocomplete#response) returnerar en lista med termer, [Förslag returnerar](https://docs.microsoft.com/rest/api/searchservice/suggestions#response) termer plus ett dokument-ID så att du kan hämta dokumentet (använd [uppslagsdokument-API:et](https://docs.microsoft.com/rest/api/searchservice/lookup-document) för att hämta det specifika dokumentet för en detaljsida).

Svaren formas av parametrarna på begäran. För Komplettera automatiskt anger du [**komplettera automatisktMode**](https://docs.microsoft.com/rest/api/searchservice/autocomplete#autocomplete-modes) för att avgöra om texten ska slutföras på en eller två termer. För förslag avgör det fält du väljer innehållet i svaret.

För förslag bör du ytterligare förfina svaret för att undvika dubbletter eller vad som verkar vara orelaterade resultat. Om du vill styra resultaten inkluderar du fler parametrar för begäran. Följande parametrar gäller både komplettera automatiskt och förslag, men är kanske mer nödvändiga för förslag, särskilt när en förslagsförenare innehåller flera fält.

| Parameter | Användning |
|-----------|-------|
| **$select** | Om du har flera **sourceFields** i en förslagsman använder du`$select=GameTitle` **$select** för att välja vilket fält som bidrar med värden ( ). |
| **sökFält** | Begränsa frågan till specifika fält. |
| **$filter** | Använd matchningsvillkor på`$filter=Category eq 'ActionAdventure'`resultatuppsättningen ( ). |
| **$top** | Begränsa resultaten till ett`$top=5`visst nummer ( ).|

## <a name="add-user-interaction-code"></a>Lägga till användarinteraktionskod

För att fylla i en frågeterm eller släppa en lista med matchande länkar krävs användarinteraktionskod, vanligtvis JavaScript, som kan använda begäranden från externa källor, till exempel automatisk komplettering eller förslagsfrågor mot ett Cognitive Azure Search-index.

Även om du kan skriva den här koden inbyggt är det mycket enklare att använda funktioner från det befintliga JavaScript-biblioteket. Den här artikeln visar två, en för förslag och en annan för komplettera automatiskt. 

+ [Widgeten Komplettera automatiskt (jQuery UI)](https://jqueryui.com/autocomplete/) används i exemplet Förslag. Du kan skapa en sökruta och sedan referera till den i en JavaScript-funktion som använder widgeten Komplettera automatiskt. Egenskaper på widgeten anger källan (en automatisk komplettering eller förslagsfunktion), minsta längd på indatatecken innan åtgärder vidtas och placering.

+ [XDSoft Komplettera automatiskt plug-in](https://xdsoft.net/jqplugins/autocomplete/) används komplettera automatiskt exempel.

Vi använder dessa bibliotek för att skapa sökrutan som stöder både förslag och komplettera automatiskt. Indata som samlas in i sökrutan paras ihop med förslag och åtgärder för automatisk komplettering.

## <a name="suggestions"></a>Förslag

I det här avsnittet får du hjälp med en implementering av föreslagna resultat, med början i sökrutans definition. Den visar också hur och skript som anropar det första automatiskt kompletteringsbiblioteket för JavaScript som refereras i den här artikeln.

### <a name="create-a-search-box"></a>Skapa en sökruta

Om du antar [att biblioteket för automatisk komplettering av jQuery UI](https://jqueryui.com/autocomplete/) och ett MVC-projekt i C#, kan du definiera sökrutan med JavaScript i filen **Index.cshtml.** Biblioteket lägger till interaktionen sök-som-du skriver i sökrutan genom att ringa asynkrona anrop till MVC-styrenheten för att hämta förslag.

I **Index.cshtml** under mappen \Views\Home kan en rad för att skapa en sökruta vara följande:

```html
<input class="searchBox" type="text" id="searchbox1" placeholder="search">
```

Det här exemplet är en enkel textruta med en klass för styling, ett ID som ska refereras av JavaScript och platshållartext.  

I samma fil bäddar du in JavaScript som refererar till sökrutan. Följande funktion anropar Föreslå API, som begär föreslagna matchande dokument baserat på partiella termindata:

```javascript
$(function () {
    $("#searchbox1").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Den `source` berättar jQuery UI Komplettera automatiskt funktion där du kan få listan över objekt som ska visas under sökrutan. Eftersom det här projektet är ett MVC-projekt anropas funktionen **Föreslå** i **HomeController.cs** som innehåller logiken för att returnera frågeförslag. Den här funktionen skickar också några parametrar för att styra högdagrar, luddig matchning och term. JavaScript-API:et för automatisk komplettering lägger till term-parametern.

De `minLength: 3` säkerställer att rekommendationer endast visas när det finns minst tre tecken i sökrutan.

### <a name="enable-fuzzy-matching"></a>Aktivera suddig matchning

Med fuzzy-sökningar kan du få resultat för nära matchningar även om användaren stavar fel på ett ord i sökrutan. Redigeringsavståndet är 1, vilket innebär att det kan finnas en maximal avvikelse för ett tecken mellan användarens inmatning och en matchning. 

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

### <a name="enable-highlighting"></a>Aktivera markering

Markeringen använder teckensnittsformatet på tecknen i resultatet som motsvarar indata. Om den partiella indata till exempel är "mikro", visas resultatet som **mikromjuk,** **mikroomfång**och så vidare. Markeringen baseras på parametrarna HighlightPreTag och HighlightPostTag, definierade infogade med funktionen Förslag.

```javascript
source: "/home/suggest?highlights=true&fuzzy=true&",
```

### <a name="suggest-function"></a>Föreslå funktion

Om du använder C# och ett MVC-program **kan du HomeController.cs** filen under controllerkatalogen skapa en klass för föreslagna resultat. I .NET baseras funktionen Föreslå på [metoden DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

Metoden `InitSearch` skapar en autentiserat HTTP-indexklient till Azure Cognitive Search-tjänsten. Mer information om .NET SDK finns i [Så här använder du Azure Cognitive Search från ett .NET-program](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

```csharp
public ActionResult Suggest(bool highlights, bool fuzzy, string term)
{
    InitSearch();

    // Call suggest API and return results
    SuggestParameters sp = new SuggestParameters()
    {
        Select = HotelName,
        SearchFields = HotelName,
        UseFuzzyMatching = fuzzy,
        Top = 5
    };

    if (highlights)
    {
        sp.HighlightPreTag = "<b>";
        sp.HighlightPostTag = "</b>";
    }

    DocumentSuggestResult resp = _indexClient.Documents.Suggest(term, "sg", sp);

    // Convert the suggest query results to a list that can be displayed in the client.
    List<string> suggestions = resp.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = suggestions
    };
}
```

Funktionen Suggest (Föreslå) tar två parametrar som bestämmer om träffmarkeringar returneras eller om fuzzy-matchning används utöver sökordsindata. Metoden skapar ett [SuggestParameters-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), som sedan skickas till Föreslå API. Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

## <a name="autocomplete"></a>Komplettera automatiskt

Hittills har sök-UX-koden centrerats på förslag. Nästa kodblock visar komplettera automatiskt, med hjälp av funktionen XDSoft jQuery UI Komplettera automatiskt, som skickar in en begäran om automatisk komplettering av Azure Cognitive Search. Som med förslagen, i ett C#-program, kod som stöder användarinteraktion går i **index.cshtml**.

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#searchbox1").autocompleteInline({
        appendMethod: "replace",
        source: [
            function (text, add) {
                if (!text) {
                    return;
                }

                $.getJSON("/home/autocomplete?term=" + text, function (data) {
                    if (data && data.length > 0) {
                        currentSuggestion2 = data[0];
                        add(data);
                    }
                });
            }
        ]
    });

    // complete on TAB and clear on ESC
    $("#searchbox1").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#searchbox1").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#searchbox1").val("");
        }
    });
});
```

### <a name="autocomplete-function"></a>Funktionen Komplettera automatiskt

Komplettera automatiskt baseras på [metoden DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet). Som med förslag, skulle detta kodblock gå i **HomeController.cs** filen.

```csharp
public ActionResult AutoComplete(string term)
{
    InitSearch();
    //Call autocomplete API and return results
    AutocompleteParameters ap = new AutocompleteParameters()
    {
        AutocompleteMode = AutocompleteMode.OneTermWithContext,
        UseFuzzyMatching = false,
        Top = 5
    };
    AutocompleteResult autocompleteResult = _indexClient.Documents.Autocomplete(term, "sg", ap);

    // Convert the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funktionen Komplettera automatiskt tar ingången till söktermen. Metoden skapar ett [Komplettera automatisktParameters-objekt](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

## <a name="next-steps"></a>Nästa steg

Följ dessa länkar för helhets-till-slut-instruktioner eller kod som visar både sök-som-du-typ upplevelser. Båda kodexemplen är hybridimplementeringar av förslag och komplettera automatiskt tillsammans.

+ [Självstudiekurs: Skapa din första app i C# (lektion 3)](tutorial-csharp-type-ahead-and-suggestions.md)
+ [Exempel på C#-kod: azure-search-dotnet-samples/create-first-app/3-add-typeahead/](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/3-add-typeahead)
+ [C# och JavaScript med REST sida vid sida kodexempel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete)
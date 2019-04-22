---
title: Lägg till förslag och Komplettera automatiskt i en sökruta – Azure Search
description: Aktivera typeahead fråga åtgärder i Azure Search genom att skapa förslagsställare och utformningen av begäranden som fyller du i en sökruta med färdiga termer eller fraser.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: ed2e0bd352823a932cfea719c18e05ae6c913621
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495744"
---
# <a name="example-add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Exempel: Lägg till förslag eller Komplettera automatiskt i ditt Azure Search-program

I den här artikeln lär du dig hur du använder [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) och [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) att skapa en kraftfull kryssruta som har stöd för sökning-som-du-type-beteenden.

+ *Förslag* är föreslagna resultaten som genereras när du skriver, där varje förslag är ett enskilt resultat från indexet som matchar vad du har skrivit hittills. 

+ *Automatisk komplettering*, [en förhandsgranskningsfunktion](search-api-preview.md), ”är klar” ord eller fraser som en användare är för närvarande för att skriva. Den är klar en fråga som du kan sedan köra för att returnera resultat istället för att returnera resultat. Precis som med förslag, förutsätter en slutförd ord eller fraser i en fråga på en matchning i indexet. Tjänsten kommer inte erbjuder frågor som returnerar resultat i indexet.

Du kan ladda ned och kör exempelkoden i **DotNetHowToAutocomplete** att utvärdera dessa funktioner. Exempelkoden riktar sig mot ett fördefinierade index som fyllts med [NYCJobs demonstrera data](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). NYCJobs indexet innehåller en [förslagsställare konstruktion](index-add-suggesters.md), vilket är ett krav för att använda förslag eller Komplettera automatiskt. Du kan använda förberedda indexet finns i en sandbox-tjänst eller [fylla i din egen index](#configure-app) med hjälp av en inläsaren av inventeringsdata i exempellösningen NYCJobs. 

Den **DotNetHowToAutocomplete** exempel visar både förslag och Komplettera automatiskt i både C# och JavaScript-språkversioner. C#utvecklare kan gå igenom ett ASP.NET MVC-baserade program som använder den [Azure Search .NET SDK](https://aka.ms/search-sdk). Logiken för att göra automatisk komplettering och föreslagna fråga anrop finns i filen homecontroller.CS. JavaScript-utvecklare hittar motsvarande frågans logik i IndexJavaScript.cshtml som innehåller direct anrop till den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). 

För båda språkversioner frontend användarupplevelsen baseras på den [jQuery UI](https://jqueryui.com/autocomplete/) och [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) bibliotek. Vi kan använda dessa bibliotek för att skapa sökrutan stöder både förslag och automatisk komplettering. Indata som samlas in i sökrutan paras ihop med förslag och automatisk komplettering åtgärder, till exempel de som definierats i HomeController.cs eller IndexJavaScript.cshtml.

Den här övningen vägleder dig genom följande uppgifter:

> [!div class="checklist"]
> * Implementera sökrutan i JavaScript och problemet begäranden för föreslagna matchningar eller kompletterat villkor
> * I C#, definiera förslag och automatisk komplettering åtgärder i HomeController.cs
> * Anropa REST-API: er direkt om du vill ge samma funktioner i JavaScript,

## <a name="prerequisites"></a>Nödvändiga komponenter

En Azure Search-tjänst är valfritt för den här övningen eftersom lösningen använder en live sandbox-tjänst som är värd för en förberedd NYCJobs demo index. Om du vill köra det här exemplet på en egen söktjänst [konfigurera NYC Jobs index](#configure-app) anvisningar.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), alla versioner. Exempelkod och instruktioner har testats på den kostnadsfria Community-versionen.

* Ladda ned den [DotNetHowToAutoComplete exempel](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

I exemplet är omfattande, vilket täcker förslag, automatisk komplettering, aspektbaserad navigering och klientcachelagring. Du bör granska viktig information och kommentarer för en fullständig beskrivning av exemplet erbjuder.

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna **AutocompleteTutorial.sln** i Visual Studio. Lösningen innehåller ett ASP.NET MVC-projekt med en anslutning till NYC Jobs-demo-index.

2. Tryck på F5 för att köra projektet och läsa in sidan i din webbläsare.

Överst på sidan visas en möjlighet att välja C# eller JavaScript. Den C# alternativet anrop till HomeController från webbläsaren och använder Azure Search .NET SDK för att hämta resultaten. 

JavaScript-alternativet anropar REST-API:et för Azure Search direkt från webbläsaren. Det här alternativet har normalt märkbart bättre prestanda eftersom kontrollanten tas bort från flödet. Du kan välja det alternativ som passar dina behov och språkinställningar. Det finns flera exempel på automatisk komplettering på sidan med viss vägledning för vart och ett av dem. Varje exempel har rekommenderad exempeltext som du kan prova.  

Prova att skriva in ett par tecken i varje sökruta och se vad som händer.

## <a name="search-box"></a>Sökruta

För både C# och JavaScript-versioner, box sökimplementering är exakt samma. 

Öppna den **Index.cshtml** filen under mappen \Views\Home att visa koden:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Det här är en enkel textruta för textinmatning med en klass för stil, ett ID som refereras av JavaScript och platshållartext.  Hemligheten sitter i inbäddade JavaScript.

Den C# kodexempel använder JavaScript i Index.cshtml för att utnyttja den [jQuery UI automatisk komplettering biblioteket](https://jqueryui.com/autocomplete/). Det här biblioteket lägger till automatisk komplettering upplevelse i sökrutan genom att göra asynkrona anrop till MVC-kontrollanten för att hämta förslag. Språkversion JavaScript är i IndexJavaScript.cshtml. Den innehåller skriptet nedan för sökfältet, samt REST API-anrop till Azure Search.

Låt oss titta på JavaScript-koden för det första exemplet som anropar jQuery UI automatisk komplettering funktion, skicka en begäran för förslag:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: "/home/suggest?highlights=false&fuzzy=false&",
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Koden ovan körs i webbläsaren på sidhämtning konfigurera jQuery UI automatisk komplettering för textrutan ”example1a”.  `minLength: 3` ser till att rekommendationerna endast visas när det finns minst tre tecken i sökrutan.  Källvärdet är viktigt:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Raden ovan informerar jQuery UI automatisk komplettering funktionen var du vill hämta en lista över objekt att visa under sökrutan. Eftersom det här är ett MVC-projekt, anropar funktionen Föreslå i HomeController.cs som innehåller logik för att returnera frågeförslag (mer om föreslå i nästa avsnitt). Den här funktionen skickar även några parametrar till kontroll höjdpunkter, partiell matchning och period. JavaScript-API:et för automatisk komplettering lägger till term-parametern.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Utöka exemplet för att stödja fuzzy-matchning

Med fuzzy-sökningar kan du få resultat för nära matchningar även om användaren stavar fel på ett ord i sökrutan. Måste inte, förbättrar det avsevärt stabilitet av en typeahead-upplevelse. Vi testar detta genom att ändra källraden för att aktivera fuzzy-matchning.

Ändra följande rad från det här:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

till det här:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Starta programmet genom att trycka på F5.

Prova att skriva något som ”execative” och observera hur du får resultat för ”executive”, även om det inte är en exakt matchning på de bokstäver som du skrev.

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery automatisk komplettering backas upp av Azure Search automatisk komplettering

Sök UX-koden har hittills har fokus på förslagen. Nästa kodblock visar jQuery UI automatisk komplettering funktionen (rad 91 i index.cshtml), skicka en begäran för Komplettera automatiskt i Azure Search:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 http://xdsoft.net/jqplugins/autocomplete/
    // $.autocomplete -> $.autocompleteInline
    $("#example2").autocompleteInline({
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
    $("#example2").keydown(function (evt) {
        if (evt.keyCode === 9 /* TAB */ && currentSuggestion2) {
            $("#example2").val(currentSuggestion2);
            return false;
        } else if (evt.keyCode === 27 /* ESC */) {
            currentSuggestion2 = "";
            $("#example2").val("");
        }
    });
});
```

## <a name="c-example"></a>C#-exempel

Nu när vi har granskat JavaScript-koden för webbsidan, ska vi titta på den C# serversidan kontrollantkoden som faktiskt hämtar de föreslagna matchningar med hjälp av Azure Search .NET SDK.

Öppna den **HomeController.cs** fil under katalogen domänkontrollanter. 

Det första märker du är en metod högst upp i klassen kallas `InitSearch`. Metoden skapar en autentiserad HTTP-indexklient till Azure Search-tjänsten. Mer information finns i [hur du använder Azure Search från .NET-program](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Observera föreslå-funktionen på rad 41. Den är baserad på den [DocumentsOperationsExtensions.Suggest metoden](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet-preview).

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

Funktionen Suggest (Föreslå) tar två parametrar som bestämmer om träffmarkeringar returneras eller om fuzzy-matchning används utöver sökordsindata. Metoden skapar en [SuggestParameters objektet](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), som sedan skickas till API: et rekommenderar. Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

Observera funktionen Komplettera automatiskt på rad 69. Den är baserad på den [DocumentsOperationsExtensions.Autocomplete metoden](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet-preview).

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

    // Conver the Suggest results to a list that can be displayed in the client.
    List<string> autocomplete = autocompleteResult.Results.Select(x => x.Text).ToList();
    return new JsonResult
    {
        JsonRequestBehavior = JsonRequestBehavior.AllowGet,
        Data = autocomplete
    };
}
```

Funktionen för automatisk komplettering tar termen sökinmatning. Metoden skapar en [AutoCompleteParameters objektet](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

(Valfritt) Lägg till en brytpunkt i början av Suggest-funktionen och stega igenom koden. Observera svaret som returnerades av SDK och hur den konverteras till resultatet som returneras från metoden.

Andra exempel på sidan följer samma mönster för att lägga till markering av träffar och aspekter att stödja klientcachelagring resultatets automatisk komplettering. Granska varje exempel så att du förstår hur de fungerar och hur du kan använda dem i din sökfunktion.

## <a name="javascript-example"></a>JavaScript-exempel

En Javascript-implementering av automatisk komplettering och förslag anropar REST-API som använder en URI som källa för att ange index och åtgärden. 

Om du vill granska JavaScript-implementeringen, öppna **IndexJavaScript.cshtml**. Observera att jQuery UI automatisk komplettering funktionen används också för sökrutan insamling av termen sökinmatningar och asynkrona anrop till Azure Search hämta föreslås matchningar eller slutförts villkor. 

Låt oss titta på JavaScript-koden i det första exemplet:

```javascript
$(function () {
    $("#example1a").autocomplete({
        source: function (request, response) {
        $.ajax({
            type: "POST",
            url: suggestUri,
            dataType: "json",
            headers: {
                "api-key": searchServiceApiKey,
                "Content-Type": "application/json"
            },
            data: JSON.stringify({
                top: 5,
                fuzzy: false,
                suggesterName: "sg",
                search: request.term
            }),
                success: function (data) {
                    if (data.value && data.value.length > 0) {
                        response(data.value.map(x => x["@@search.text"]));
                    }
                }
            });
        },
        minLength: 3,
        position: {
            my: "left top",
            at: "left-23 bottom+10"
        }
    });
});
```

Om du jämför detta med exemplet ovan som anropar HomeController hittar vi flera likheter.  Konfigurationen för automatisk komplettering för `minLength` och `position` är identisk. 

Den stora ändringen här är källan. I stället för att anropa metoden föreslå i kontrollanten hem, en REST-begäran har skapats i en JavaScript-funktion och körs med Ajax. Svaret bearbetas sedan i ”success” och används som källa.

REST-anrop Använd URI: er för att ange om en [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) eller [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) API-anrop görs. Följande URI: er finns på raderna 9 och 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

På raden 148, hittar du ett skript som anropar den `autocompleteUri`. Det första anropet till `suggestUri` är på rad 39.

> [!Note]
> Att göra REST-anrop till tjänsten i JavaScript erbjuds här som en praktisk demonstration av REST API, men ska inte betraktas som en bästa praxis eller rekommendation. Inkludering av en API-nyckel och slutpunkt i ett skript öppnas din tjänst upp till DOS-angrepp för alla som kan läsa dessa värden av skriptet. När dess säkert att använda JavaScript i utbildningssyfte, kanske på index på den kostnadsfria tjänsten bör du använda Java eller C# för indexerings- och åtgärder i produktionskod. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurera NYCJobs ska köras på din tjänst

Fram till nu har du använt värdbaserade NYCJobs demo indexet. Om du vill full insyn i alla kod, inklusive index, följer du dessa instruktioner för att skapa och läsa in index i din egen search-tjänst.

1. [Skapa en Azure Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst i det här exemplet. 

   > [!Note]
   > Om du använder den kostnadsfria Azure Search-tjänsten är du begränsad till tre index. NYCJobs-datainläsaren skapar två index. Se till att det finns utrymme på din tjänst för dessa nya index.

1. Ladda ned [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) exempelkoden.

1. Öppna i mappen DataLoader exempelkod NYCJobs **DataLoader.sln** i Visual Studio.

1. Lägg till anslutningsinformation för Azure Search-tjänsten. Öppna App.config i projektet DataLoader och ändra TargetSearchServiceName och TargetSearchServiceApiKey i appinställningarna så att de motsvarar din Azure Search-tjänst och API-nyckeln för din Azure Search-tjänst. Dessa hittar du i Azure-portalen.

1. Tryck på F5 för att starta programmet, skapar två index och importera exempeldata NYCJob.

1. Öppna **AutocompleteTutorial.sln** och redigera filen Web.config i den **AutocompleteTutorial** projekt. Ändra värdet SearchServiceName och SearchServiceApiKey till värden som är giltiga för din söktjänst.

1. Tryck på F5 för att köra appen. Exempelwebbappen öppnas i standardwebbläsaren. Upplevelsen är identisk med den sandbox-versionen, endast index och data finns på din tjänst.

## <a name="next-steps"></a>Nästa steg

Det här exemplet visar de grundläggande stegen för att skapa en sökruta som har stöd för automatisk komplettering och förslag. Såg du hur du kan skapa en ASP.NET MVC-App och använda Azure Search .NET SDK eller REST API för att hämta förslag.

I nästa steg, försöker integrera förslag och Komplettera automatiskt i användningen. I följande referensartiklar bör hjälpa.

> [!div class="nextstepaction"]
> [REST-API för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [REST-API för förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Fasettindexattribut på ett REST-API för att skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)


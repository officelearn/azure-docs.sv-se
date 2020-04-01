---
title: Lägga till förslag och komplettera automatiskt i en sökruta
titleSuffix: Azure Cognitive Search
description: Aktivera typeahead-frågeåtgärder i Azure Cognitive Search genom att skapa förslagsställare och formulera begäranden som fyller i en sökruta med slutförda termer eller fraser.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 64c4e65ca7b69c7d61c706b48591ac19be3bfcf5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "72792512"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-cognitive-search-application"></a>Lägga till förslag eller komplettera automatiskt i ditt Azure Cognitive Search-program

I den här artikeln kan du läsa om hur du använder [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) och [komplettera automatiskt](https://docs.microsoft.com/rest/api/searchservice/autocomplete) för att skapa en kraftfull sökruta som stöder sök-som-du-typ beteenden.

+ *Förslag föreslås* resultat som genereras när du skriver, där varje förslag är ett enda resultat från indexet som matchar det du har skrivit hittills. 

+ *Komplettera automatiskt* "avslutar" ordet eller frasen som en användare för närvarande skriver. I stället för att returnera resultat slutförs en fråga som du sedan kan köra för att returnera resultat. Precis som med förslag baseras ett färdigt ord eller en fras i en fråga på en matchning i indexet. Tjänsten erbjuder inte frågor som returnerar noll resultat i indexet.

Du kan hämta och köra exempelkoden i **DotNetHowToAutocomplete** för att utvärdera dessa funktioner. Exempelkoden är inriktad på ett fördefinierat index som fylls med [NYCJobs demodata](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). Den NYCJobs index innehåller en [Suggester konstruera](index-add-suggesters.md), vilket är ett krav för att använda antingen förslag eller komplettera automatiskt. Du kan använda det förberedda indexet som finns i en sandbox-tjänst eller [fylla i ditt eget index](#configure-app) med hjälp av en datainläsare i EXEMPELlösningen FÖR NYCJobs. 

**Exemplet DotNetHowToAutocomplete** visar både förslag och komplettera automatiskt, i både C# och JavaScript-språkversioner. C#-utvecklare kan gå igenom ett ASP.NET MVC-baserat program som använder [Azure Cognitive Search .NET SDK](https://aka.ms/search-sdk). Logiken för att göra automatisk komplettering och föreslagna frågeanrop finns i HomeController.cs filen. JavaScript-utvecklare hittar motsvarande frågelogik i IndexJavaScript.cshtml, som innehåller direkta anrop till [AZURE Cognitive Search REST API](https://docs.microsoft.com/rest/api/searchservice/). 

För båda språkversionerna baseras frontend-användarupplevelsen på [biblioteken jQuery UI](https://jqueryui.com/autocomplete/) och [XDSoft.](https://xdsoft.net/jqplugins/autocomplete/) Vi använder dessa bibliotek för att skapa sökrutan som stöder både förslag och komplettera automatiskt. Indata som samlas in i sökrutan paras ihop med förslag och åtgärder för automatisk komplettering, till exempel de som definieras i HomeController.cs eller IndexJavaScript.cshtml.

Den här övningen går igenom följande uppgifter:

> [!div class="checklist"]
> * Implementera en sökinmatningsruta i JavaScript och utfärda begäranden om föreslagna matchningar eller autocompleted termer
> * I C#, definiera förslag och komplettera automatiskt åtgärder i HomeController.cs
> * I JavaScript anropar du REST-API:erna direkt för att tillhandahålla samma funktioner

## <a name="prerequisites"></a>Krav

En Azure Cognitive Search-tjänst är valfri för den här övningen eftersom lösningen använder en live-sandlådetjänst som är värd för ett förberett NYCJobs-demoindex. Om du vill köra det här exemplet på din egen söktjänst läser [du Konfigurera NYC Jobs-index](#configure-app) för instruktioner.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst. Exempelkod och instruktioner testades på den kostnadsfria gemenskapsutgåvan.

* Ladda ner [exemplet DotNetHowToAutoComplete](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Exemplet är omfattande, som omfattar förslag, komplettera automatiskt, fasterad navigering och cachelagring på klientsidan. Granska readme och kommentarer för en fullständig beskrivning av vad exemplet erbjuder.

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna **Komplettera automatisktTutorial.sln** i Visual Studio. Lösningen innehåller ett ASP.NET MVC-projekt med en anslutning till NYC Jobs demoindex.

2. Tryck på F5 för att köra projektet och läsa in sidan i din webbläsare.

Överst på sidan visas en möjlighet att välja C# eller JavaScript. Alternativet C# anropar HomeController från webbläsaren och använder Azure Cognitive Search .NET SDK för att hämta resultat. 

JavaScript-alternativet anropar AZURE Cognitive Search REST API direkt från webbläsaren. Det här alternativet har normalt märkbart bättre prestanda eftersom kontrollanten tas bort från flödet. Du kan välja det alternativ som passar dina behov och språkinställningar. Det finns flera exempel på automatisk komplettering på sidan med viss vägledning för vart och ett av dem. Varje exempel har rekommenderad exempeltext som du kan prova.  

Prova att skriva in ett par tecken i varje sökruta och se vad som händer.

## <a name="search-box"></a>Sökruta

För både C# och JavaScript-versioner är sökrutans implementering exakt densamma. 

Öppna filen **Index.cshtml** under mappen \Views\Home för att visa koden:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Det här exemplet är en enkel textruta med en klass för styling, ett ID som ska refereras av JavaScript och platshållartext.  Magin finns i det inbäddade JavaScript.

I exemplet med C#används JavaScript i Index.cshtml för att utnyttja [biblioteket för automatisk komplettering av jQuery UI.](https://jqueryui.com/autocomplete/) Det här biblioteket lägger till den automatiska kompletteringsupplevelsen i sökrutan genom att ringa asynkrona anrop till MVC-styrenheten för att hämta förslag. JavaScript-språkversionen finns i IndexJavaScript.cshtml. Den innehåller skriptet nedan för sökfältet, samt REST API-anrop till Azure Cognitive Search.

Låt oss titta på JavaScript-koden för det första exemplet, som anropar jQuery UI Autocomplete-funktionen, och skickar in en begäran om förslag:

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

Ovanstående kod körs i webbläsaren på sidan belastning för att konfigurera jQuery UI komplettera automatiskt för "example1a" inmatningsrutan.  `minLength: 3` ser till att rekommendationerna endast visas när det finns minst tre tecken i sökrutan.  Källvärdet är viktigt:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Ovanstående rad talar om för funktionen automatisk komplettering av jQuery UI var listan med objekt ska visas under sökrutan. Eftersom det här projektet är ett MVC-projekt anropas funktionen Föreslå i HomeController.cs som innehåller logiken för att returnera frågeförslag (mer om Föreslå i nästa avsnitt). Den här funktionen skickar också några parametrar för att styra högdagrar, luddig matchning och term. JavaScript-API:et för automatisk komplettering lägger till term-parametern.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Utöka exemplet för att stödja fuzzy-matchning

Med fuzzy-sökningar kan du få resultat för nära matchningar även om användaren stavar fel på ett ord i sökrutan. Även om det inte krävs, förbättrar det avsevärt robustheten i en typeahead erfarenhet. Vi testar detta genom att ändra källraden för att aktivera fuzzy-matchning.

Ändra följande rad från det här:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

till denna:

```javascript
source: "/home/suggest?highlights=false&fuzzy=true&",
```

Starta programmet genom att trycka på F5.

Prova att skriva något som ”execative” och observera hur du får resultat för ”executive”, även om det inte är en exakt matchning på de bokstäver som du skrev.

### <a name="jquery-autocomplete--backed-by-azure-cognitive-search-autocomplete"></a>jQuery Komplettera automatiskt backas upp av Azure Cognitive Search komplettera automatiskt

Hittills har sök-UX-koden centrerats på förslagen. Nästa kodblock visar funktionen automatisk komplettering av jQuery UI (rad 91 i index.cshtml), som skickar in en begäran om automatisk komplettering av Azure Cognitive Search:

```javascript
$(function () {
    // using modified jQuery Autocomplete plugin v1.2.6 https://xdsoft.net/jqplugins/autocomplete/
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

## <a name="c-example"></a>C# exempel

Nu när vi har granskat JavaScript-koden för webbsidan, låt oss titta på C # server-side controller kod som faktiskt hämtar de föreslagna matchningarna med Hjälp av Azure Cognitive Search .NET SDK.

Öppna **HomeController.cs-filen** under katalogen Controllers. 

Det första du kanske märker är en metod `InitSearch`högst upp i klassen som kallas . Den här metoden skapar en autentiserat HTTP-indexklient till Azure Cognitive Search-tjänsten. Mer information finns i [Så här använder du Azure Cognitive Search från ett .NET-program](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

På linje 41, lägg märke till funktionen Föreslå. Den är baserad på [metoden DocumentsOperationsExtensions.Suggest](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

Funktionen Suggest (Föreslå) tar två parametrar som bestämmer om träffmarkeringar returneras eller om fuzzy-matchning används utöver sökordsindata. Metoden skapar ett [SuggestParameters-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), som sedan skickas till Föreslå API. Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

På linje 69 lägger du märke till funktionen Komplettera automatiskt. Den är baserad på [metoden DocumentsOperationsExtensions.Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

Funktionen Komplettera automatiskt tar ingången till söktermen. Metoden skapar ett [Komplettera automatisktParameters-objekt](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

(Valfritt) Lägg till en brytpunkt i början av Suggest-funktionen och stega igenom koden. Lägg märke till svaret som returneras av SDK och hur det konverteras till resultatet som returneras från metoden.

De andra exemplen på sidan följer samma mönster för att lägga till träffmarkering och fasetter för att stödja cachelagring på klientsidan av resultaten för automatisk komplettering. Granska varje exempel så att du förstår hur de fungerar och hur du kan använda dem i din sökfunktion.

## <a name="javascript-example"></a>JavaScript-exempel

En Javascript-implementering av komplettera automatiskt och förslag anropar REST API, med hjälp av en URI som källa för att ange index och åtgärder. 

Om du vill granska JavaScript-implementeringen öppnar du **IndexJavaScript.cshtml**. Observera att funktionen automatisk komplettering av jQuery UI också används för sökrutan, samla in söktermsindata och ringa asynkrona anrop till Azure Cognitive Search för att hämta föreslagna matchningar eller slutförda termer. 

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

Om du jämför det här exemplet med exemplet ovan som anropar hemkontrollanten ser du flera likheter.  Konfigurationen för automatisk komplettering för `minLength` och `position` är identisk. 

Den stora ändringen här är källan. I stället för att anropa föreslå-metoden i hemkontrollanten skapas en REST-begäran i en JavaScript-funktion och körs med Ajax. Svaret bearbetas sedan i ”success” och används som källa.

REST-anrop använder URI:er för att ange om ett [API-anrop för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) eller [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) ska göras. Följande URI:er finns på raderna 9 respektive 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

På rad 148 hittar du ett `autocompleteUri`skript som anropar . Det första `suggestUri` samtalet till är på linje 39.

> [!Note]
> Att göra REST-anrop till tjänsten i JavaScript erbjuds här som en bekväm demonstration av REST API, men bör inte tolkas som en bästa praxis eller rekommendation. Införandet av en API-nyckel och slutpunkt i ett skript öppnar tjänsten upp till denial of service-attacker till alla som kan läsa dessa värden utanför skriptet. Medan dess säkra att använda JavaScript för inlärningsändamål, kanske på index värd på den kostnadsfria tjänsten, rekommenderar vi att du använder Java eller C # för indexering och frågor i produktionskod. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurera NYCJobs så att de körs på din tjänst

Hittills har du använt värd NYCJobs demoindex. Om du vill ha full insyn i all kod, inklusive indexet, följer du dessa instruktioner för att skapa och läsa in indexet i din egen söktjänst.

1. [Skapa en Azure Cognitive Search-tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnadsfri tjänst i det här exemplet. 

   > [!Note]
   > Om du använder den kostnadsfria Azure Cognitive Search-tjänsten är du begränsad till tre index. NYCJobs-datainläsaren skapar två index. Se till att det finns utrymme på din tjänst för dessa nya index.

1. Ladda ner [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) exempelkod.

1. Öppna **DataLoader.sln** i Visual Studio i mappen DataLoader iMPLS-exempelkoden.

1. Lägg till anslutningsinformationen för din Azure Cognitive Search-tjänst. Öppna App.config i DataLoader-projektet och ändra TargetSearchServiceName- och TargetSearchServiceApiKey-appSettings för att återspegla din Azure Cognitive Search-tjänst och Azure Cognitive Search service API-nyckel. Den här informationen finns i Azure-portalen.

1. Tryck på F5 för att starta programmet, skapa två index och importera NYCJob-exempeldata.

1. Öppna **Komplettera automatisktTutorial.sln** och redigera web.config i projektet **Komplettera** automatiskt. Ändra värdena SearchServiceName och SearchServiceApiKey till värden som är giltiga för söktjänsten.

1. Tryck på F5 för att köra appen. Exempelwebbappen öppnas i standardwebbläsaren. Upplevelsen är identisk med sandlådeversionen, bara index och data finns på din tjänst.

## <a name="next-steps"></a>Nästa steg

Det här exemplet visar de grundläggande stegen för att skapa en sökruta som stöder automatisk komplettering och förslag. Du såg hur du kunde skapa ett ASP.NET MVC-program och använda azure cognitive search .NET SDK eller REST API för att hämta förslag.

Som ett nästa steg, försöker integrera förslag och komplettera automatiskt i din sökupplevelse. Följande referensartiklar bör vara till hjälp.

> [!div class="nextstepaction"]
> [Komplettera REST API-förslag](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> automatiskt REST
> [API-indexattribut för REST API-fasetter på ett REST-API för skapa index på ett rest-API för skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index) [Suggestions REST API](https://docs.microsoft.com/rest/api/searchservice/suggestions)


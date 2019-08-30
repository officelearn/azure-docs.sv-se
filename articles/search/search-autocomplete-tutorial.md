---
title: Lägg till förslag och Autoavsluta i en sökruta – Azure Search
description: Aktivera typeahead fråge åtgärder i Azure Search genom att skapa förslag och utforma förfrågningar som fyller i en sökruta med slutförda villkor eller fraser.
manager: nitinme
author: mrcarter8
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: mcarter
ms.openlocfilehash: 1ec343228e32c9dd6fb126560a7a17b54c5e36cb
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183288"
---
# <a name="add-suggestions-or-autocomplete-to-your-azure-search-application"></a>Lägg till förslag eller Autoavsluta till ditt Azure Search program

I den här artikeln får du lära dig hur du använder [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions) och [Autoavsluta](https://docs.microsoft.com/rest/api/searchservice/autocomplete) för att bygga en kraftfull sökruta som stöder beteenden för sökning efter typ.

+ *Förslag* är föreslagna resultat som skapas när du skriver, där varje förslag är ett enda resultat från det index som matchar det du har skrivit hittills. 

+ *Autoavsluta* "avslutar" ordet eller frasen som en användare skriver för närvarande. I stället för att returnera resultat slutförs en fråga som du sedan kan köra för att returnera resultat. Precis som med förslag används ett ifyllt ord eller en fras i en fråga för en matchning i indexet. Tjänsten erbjuder inte frågor som returnerar noll resultat i indexet.

Du kan hämta och köra exempel koden i **DotNetHowToAutocomplete** för att utvärdera dessa funktioner. Exempel koden riktar sig till ett fördefinierat index som är ifyllt med [NYCJobs demo-data](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs). NYCJobs-indexet innehåller en [förslags konstruktion](index-add-suggesters.md), vilket är ett krav för att använda antingen förslag eller Autoavsluta. Du kan använda det för beredda index som finns i en sandbox-tjänst eller [fylla i ditt eget index](#configure-app) med en data inläsare i NYCJobs-exempel lösningen. 

I **DotNetHowToAutocomplete** -exemplet visas både förslag och Autoavsluta i både C# och språk versionerna i Java Script. C#utvecklare kan gå igenom ett ASP.NET MVC-baserat program som använder [Azure Search .NET SDK](https://aka.ms/search-sdk). Logiken för att skapa Autoavsluta-och föreslagna fråge anrop finns i filen HomeController.cs. JavaScript-utvecklare hittar motsvarande frågesträng i IndexJavaScript. cshtml, som innehåller direkta anrop till [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/). 

För båda språk versionerna är klient delen av användar upplevelsen baserad på jQuery- [gränssnittet](https://jqueryui.com/autocomplete/) och [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) -biblioteken. Vi använder dessa bibliotek för att bygga sökrutan som stöder både förslag och Autoavsluta. Indata som samlas in i sökrutan kombineras med förslag och åtgärder för automatisk komplettering, till exempel de som definieras i HomeController.cs eller IndexJavaScript. cshtml.

Den här övningen vägleder dig genom följande uppgifter:

> [!div class="checklist"]
> * Implementera en sökruta i Java Script och utfärda begär Anden för föreslagna matchningar eller komplettera villkor
> * I C#definierar du förslag och åtgärder för automatisk komplettering i HomeController.CS
> * Anropa REST-API: er direkt i Java Script för att ge samma funktioner

## <a name="prerequisites"></a>Förutsättningar

En Azure Search tjänst är valfri för den här övningen eftersom lösningen använder en Live sandbox-tjänst som värd för ett för berett NYCJobs demo index. Om du vill köra det här exemplet på din egen Sök tjänst, se [Konfigurera NYC Jobs index](#configure-app) för instruktioner.

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), vilken utgåva som helst. Exempel kod och instruktioner har testats i den kostnads fria community-versionen.

* Hämta [DotNetHowToAutoComplete-exemplet](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToAutocomplete).

Exemplet är omfattande, täcker förslag, Autoavsluta, upprullningsbar navigering och cachelagring på klient sidan. Läs igenom viktigt och kommentarer för en fullständig beskrivning av vad exemplet erbjuder.

## <a name="run-the-sample"></a>Kör exemplet

1. Öppna **AutocompleteTutorial. SLN** i Visual Studio. Lösningen innehåller ett ASP.NET MVC-projekt med en anslutning till NYC-jobbets demo index.

2. Tryck på F5 för att köra projektet och läsa in sidan i din webbläsare.

Överst på sidan visas en möjlighet att välja C# eller JavaScript. C# Alternativet anropar HomeController från webbläsaren och använder Azure Search .NET SDK för att hämta resultat. 

JavaScript-alternativet anropar REST-API:et för Azure Search direkt från webbläsaren. Det här alternativet har normalt märkbart bättre prestanda eftersom kontrollanten tas bort från flödet. Du kan välja det alternativ som passar dina behov och språkinställningar. Det finns flera exempel på automatisk komplettering på sidan med viss vägledning för vart och ett av dem. Varje exempel har rekommenderad exempeltext som du kan prova.  

Prova att skriva in ett par tecken i varje sökruta och se vad som händer.

## <a name="search-box"></a>Sökruta

För både C# -och JavaScript-versioner är sökrutan är exakt densamma. 

Öppna filen **index. cshtml** under mappen \Views\Home för att visa koden:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Det här exemplet är en enkel text ruta med en klass för formatering, ett ID som ska refereras till av Java Script och platshållartext.  Magic är i det inbäddade Java Script.

C# Språk exemplet använder Java Script i index. cshtml för att utnyttja [jQuery UI-biblioteket för automatisk komplettering](https://jqueryui.com/autocomplete/). Det här biblioteket lägger till autocomplete-upplevelsen i sökrutan genom att göra asynkrona anrop till MVC-kontrollanten för att hämta förslag. Språk versionen för Java Script är i IndexJavaScript. cshtml. Den innehåller skriptet nedan för Sök fältet och REST API anrop till Azure Search.

Nu ska vi titta på JavaScript-koden för det första exemplet, som anropar jQuery UI komplettera automatiskt och skickar en begäran om förslag:

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

Ovanstående kod körs i webbläsaren på sid inläsning för att konfigurera jQuery UI komplettera automatiskt för indatamängden "example1a".  `minLength: 3` ser till att rekommendationerna endast visas när det finns minst tre tecken i sökrutan.  Källvärdet är viktigt:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Ovanstående rad visar funktionen jQuery UI komplettera automatiskt där du kan hämta listan över objekt som ska visas under sökrutan. Eftersom projektet är ett MVC-projekt anropas funktionen föreslå i HomeController.cs som innehåller logiken för att returnera fråge förslag (mer information om föreslå i nästa avsnitt). Den här funktionen skickar också några parametrar för att kontrol lera högdagrar, fuzzy Matching och term. JavaScript-API:et för automatisk komplettering lägger till term-parametern.

### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Utöka exemplet för att stödja fuzzy-matchning

Med fuzzy-sökningar kan du få resultat för nära matchningar även om användaren stavar fel på ett ord i sökrutan. Även om det inte krävs förbättrar den stabilheten hos en typeahead upplevelse. Vi testar detta genom att ändra källraden för att aktivera fuzzy-matchning.

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

### <a name="jquery-autocomplete--backed-by-azure-search-autocomplete"></a>jQuery-Kompletteraren backas upp av Azure Search Autoavsluta

Hittills har Sök-UX-koden centrerats på förslagen. Nästa kodblock visar funktionen Komplettera automatiskt i jQuery UI (rad 91 i index. cshtml), som skickar en begäran om att Azure Search komplettera automatiskt:

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

## <a name="c-example"></a>C#exempel

Nu när vi har granskat JavaScript-koden för webb sidan ska vi titta på C# Server sidans styrenhets kod som faktiskt hämtar de föreslagna matchningarna med hjälp av Azure Search .NET SDK.

Öppna filen **HomeController.cs** i katalogen controllers. 

Det första du kanske märker är en metod överst i klassen som kallas `InitSearch`. Den här metoden skapar en autentiserad HTTP-index-klient till tjänsten Azure Search. Mer information finns i [så här använder du Azure Search från ett .NET-program](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk).

Lägg märke till funktionen föreslå på rad 41. Den baseras på [metoden DocumentsOperationsExtensions. föreslå](/dotnet/api/microsoft.azure.search.documentsoperationsextensions.suggest?view=azure-dotnet).

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

Funktionen Suggest (Föreslå) tar två parametrar som bestämmer om träffmarkeringar returneras eller om fuzzy-matchning används utöver sökordsindata. Metoden skapar ett [SuggestParameters-objekt](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.suggestparameters?view=azure-dotnet), som sedan skickas till föreslå API. Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

Lägg märke till funktionen Komplettera automatiskt på rad 69. Den baseras på [metoden DocumentsOperationsExtensions. Autocomplete](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions.autocomplete?view=azure-dotnet).

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

Funktionen Autoavsluta tar search term-indatamängden. Metoden skapar ett [AutoCompleteParameters-objekt](https://docs.microsoft.com/rest/api/searchservice/autocomplete). Resultatet konverteras sedan till JSON, så att det kan visas i klienten.

(Valfritt) Lägg till en brytpunkt i början av Suggest-funktionen och stega igenom koden. Observera svaret som returnerades av SDK och hur det konverteras till det resultat som returneras från-metoden.

De andra exemplen på sidan följer samma mönster för att lägga till träff markeringar och fasetter som stöder cachelagring på klient sidan av Autoavsluta-resultatet. Granska varje exempel så att du förstår hur de fungerar och hur du kan använda dem i din sökfunktion.

## <a name="javascript-example"></a>JavaScript-exempel

En JavaScript-implementering av Autoavsluta och Suggestions anropar REST API med hjälp av en URI som källa för att ange index och åtgärd. 

Om du vill granska JavaScript-implementeringen öppnar du **IndexJavaScript. cshtml**. Observera att funktionen Komplettera automatiskt i jQuery-gränssnittet används även för sökrutan, samlar in sökord och gör asynkrona anrop till Azure Search för att hämta föreslagna matchningar eller slutförda villkor. 

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

Om du jämför det här exemplet med exemplet ovan som anropar start styrenheten, ser du flera likheter.  Konfigurationen för automatisk komplettering för `minLength` och `position` är identisk. 

Den stora ändringen här är källan. I stället för att anropa metoden föreslå i Start styrenheten skapas en REST-begäran i en JavaScript-funktion och körs med Ajax. Svaret bearbetas sedan i ”success” och används som källa.

REST-anrop använder URI: er för [](https://docs.microsoft.com/rest/api/searchservice/autocomplete) att ange om ett för kompletterings-eller [förslags](https://docs.microsoft.com/rest/api/searchservice/suggestions) -API-anrop görs. Följande URI: er finns på raderna 9 respektive 10.

```javascript
var suggestUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/suggest?api-version=" + apiVersion;
var autocompleteUri = "https://" + searchServiceName + ".search.windows.net/indexes/" + indexName + "/docs/autocomplete?api-version=" + apiVersion;
```

På rad 148 hittar du ett skript som anropar `autocompleteUri`. Det första anropet `suggestUri` till finns på rad 39.

> [!Note]
> Att göra REST-anrop till tjänsten i Java Script erbjuds här som en bekväm demonstration av REST API, men bör inte tolkas som en bästa praxis eller rekommendation. Inkludering av en API-nyckel och en slut punkt i ett skript öppnar din tjänst upp till denial of Service-attacker till alla som kan läsa dessa värden från skriptet. Även om det är säkert att använda Java Script i inlärnings syfte, kanske vi på index som finns på den kostnads fria tjänsten C# , rekommenderar att du använder Java eller för indexering och frågor i produktions koden. 

<a name="configure-app"></a>

## <a name="configure-nycjobs-to-run-on-your-service"></a>Konfigurera NYCJobs så att de körs på din tjänst

Fram till nu har du använt demonstrations indexet för den värdbaserade NYCJobs. Om du vill ha fullständig insyn i all kod, inklusive indexet, följer du dessa instruktioner för att skapa och läsa in indexet i din egen Sök tjänst.

1. [Skapa en Azure Search tjänst](search-create-service-portal.md) eller [hitta en befintlig tjänst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) under din aktuella prenumeration. Du kan använda en kostnads fri tjänst för det här exemplet. 

   > [!Note]
   > Om du använder den kostnadsfria Azure Search-tjänsten är du begränsad till tre index. NYCJobs-datainläsaren skapar två index. Se till att det finns utrymme på din tjänst för dessa nya index.

1. Hämta [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) exempel kod.

1. I mappen DataLoader i exempel koden NYCJobs öppnar du **DataLoader. SLN** i Visual Studio.

1. Lägg till anslutnings informationen för din Azure Search-tjänst. Öppna App.config i projektet DataLoader och ändra TargetSearchServiceName och TargetSearchServiceApiKey i appinställningarna så att de motsvarar din Azure Search-tjänst och API-nyckeln för din Azure Search-tjänst. Du hittar den här informationen i Azure Portal.

1. Tryck på F5 för att starta programmet, skapa två index och importera NYCJob-exempel data.

1. Öppna **AutocompleteTutorial. SLN** och redigera Web. config i **AutocompleteTutorial** -projektet. Ändra värdena för SearchServiceName och SearchServiceApiKey till värden som är giltiga för din Sök tjänst.

1. Tryck på F5 för att köra appen. Exempel webb programmet öppnas i standard webbläsaren. Upplevelsen är identisk med sandbox-versionen, bara index och data finns på din tjänst.

## <a name="next-steps"></a>Nästa steg

Det här exemplet demonstrerar de grundläggande stegen för att skapa en sökruta som stöder Autoavsluta och förslag. Du har sett hur du kan bygga ett ASP.NET MVC-program och använda antingen Azure Search .NET SDK eller REST API för att hämta förslag.

Nästa steg är att försöka integrera förslag och komplettera automatiskt i din Sök funktion. Följande referens artiklar bör hjälpa dig.

> [!div class="nextstepaction"]
> [REST-API för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [REST-API för förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Fasettindexattribut på ett REST-API för att skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)


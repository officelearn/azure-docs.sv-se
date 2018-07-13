---
title: 'Självstudie: Lägga till automatisk komplettering i sökrutan med Azure Search | Microsoft Docs'
description: Exempel på hur vi kan förbättra användarens upplevelse av ditt datacentrerade program med hjälp av Azure Search-API:er för automatisk komplettering och förslag.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: mcarter
ms.openlocfilehash: 90e99e7d44183d70f4e348c7b9070001fa3c6329
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101055"
---
# <a name="tutorial-add-auto-complete-to-your-search-box-using-azure-search"></a>Självstudie: Lägga till automatisk komplettering i sökrutan med Azure Search

I den här självstudien får du lära dig hur du använder [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions), [automatisk komplettering](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete) och [fasetter](search-faceted-navigation.md) i [REST-API:et för Azure Search](https://docs.microsoft.com/rest/api/searchservice/) och [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) för att skapa en kraftfull sökruta. Med *förslag* ges rekommendationer av faktiska resultat utifrån det som användaren har skrivit hittills. *Komplettera automatiskt* är [en ny funktion (förhandsversion)](search-api-preview.md) i Azure Search, som tillhandahåller ord från index för att komplettera det som användaren skriver. Vi jämför flera tekniker för att förbättra användarproduktiviteten så att användarna snabbt och enkelt kan hitta det som de söker efter direkt medan de skriver.

Den här självstudien vägleder dig genom ett ASP.NET MVC-baserat program som använder C# för att anropa [Azure Search .NET-klientbiblioteken](https://aka.ms/search-sdk) och JavaScript för att direkt anropa REST-API:et för Azure Search. Programmet för den här självstudien använder ett index som fyllts i med [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)-exempeldata. Du kan antingen använda det index som redan har konfigurerats i NYC Jobs-demon eller fylla i ett eget index med en datainläsare i NYCJobs-exempellösningen. I exemplet används JavaScript-biblioteken [jQuery UI](https://jqueryui.com/autocomplete/) och [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) för att skapa en sökruta som stöder automatisk komplettering. Genom att använda de här komponenterna med Azure Search kan du se flera exempel på hur du kan ge stöd för automatisk komplettering med förifyllning av förslag i sökrutan.

Du kommer att utföra följande uppgifter:

> [!div class="checklist"]
> * Hämta och konfigurera lösningen
> * Lägga till söktjänstinformation i programinställningar
> * Implementera en sökruta
> * Lägga till stöd för en lista för automatisk komplettering som hämtar data från en extern källa 
> * Hämta förslag och automatisk komplettering med .Net SDK och REST-API:et
> * Lägga till cachelagring på klientsidan för att förbättra prestandan 

## <a name="prerequisites"></a>Krav

* Visual Studio 2017. Du kan använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/), som är kostnadsfri. 

* Ladda ned [exempelkällkoden](https://github.com/azure-samples/search-dotnet-getting-started) för självstudien.

* (Valfritt) Ett aktivt Azure-konto och en Azure Search-tjänst. Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/free/). Läs [Skapa en söktjänst](search-create-service-portal.md) om du vill ha hjälp med tjänstetablering. Kontot och tjänsten är valfria eftersom den här självstudien kan utföras med ett värdbaserat NYCJobs-index som redan finns för en annan demo.

* (Valfritt) Ladda ned [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)-exempelkoden för att importera NYCJobs-data till ett index i din egen Azure Search-tjänst.

> [!Note]
> Om du använder den kostnadsfria Azure Search-tjänsten är du begränsad till tre index. NYCJobs-datainläsaren skapar två index. Se till att det finns utrymme på din tjänst för dessa nya index.

### <a name="set-up-azure-search-optional"></a>Konfigurera Azure Search (valfritt)

Följ stegen i det här avsnittet om du vill importera data för exempelprogrammet NYCJobs till ditt eget index. Det här steget är valfritt.  Om du vill använda exempelindexet hoppar du till nästa avsnitt för att köra exemplet.

1. I mappen DataLoader i NYCJobs-exempelkoden öppnar du filen DataLoader.sln i Visual Studio.

1. Uppdatera anslutningsinformationen för Azure Search-tjänsten.  Öppna App.config i projektet DataLoader och ändra TargetSearchServiceName och TargetSearchServiceApiKey i appinställningarna så att de motsvarar din Azure Search-tjänst och API-nyckeln för din Azure Search-tjänst.  Dessa hittar du i Azure-portalen.

1. Tryck på F5 för att starta programmet.  Detta skapar två index och importerar NYCJob-exempeldata.

1. Öppna filen AutocompleteTutorial.sln (i exempelkoden för den här självstudien) i Visual Studio.  Öppna Web.config i projektet AutocompleteTutorial och ändra värdena för SearchServiceName och SearchServiceApiKey till samma värden som ovan.

### <a name="running-the-sample"></a>Köra exemplet

Nu är du redo att köra exempelprogrammet.  Öppna filen AutocompleteTutorial.sln i Visual Studio för att köra självstudien.  Lösningen innehåller ett ASP.NET MVC-projekt.  Tryck på F5 för att köra projektet och läsa in sidan i din webbläsare.  Överst på sidan visas en möjlighet att välja C# eller JavaScript.  C#-alternativet anropar HomeController från webbläsaren och använder Azure Search .Net SDK för att hämta resultat.  JavaScript-alternativet anropar REST-API:et för Azure Search direkt från webbläsaren.  Det här alternativet har normalt märkbart bättre prestanda eftersom kontrollanten tas bort från flödet.  Du kan välja det alternativ som passar dina behov och språkinställningar.  Det finns flera exempel på automatisk komplettering på sidan med viss vägledning för vart och ett av dem.  Varje exempel har rekommenderad exempeltext som du kan prova.  Prova att skriva in ett par tecken i varje sökruta och se vad som händer.

## <a name="how-this-works-in-code"></a>Så här fungerar det i koden

Nu när du har sett exemplen i webbläsaren är det dags att gå igenom det första exemplet i detalj för att ta en titt på de komponenter som ingår och hur de fungerar.

### <a name="search-box"></a>Sökruta

Sökrutan är exakt densamma för båda språkalternativen.  Öppna filen Index.cshtml under mappen \Views\Home. Själva sökrutan är enkel:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Det är en enkel ruta för inmatning av text med en klass för stil, ett id som refereras till av JavaScript och platshållartext.  Magin sitter i JavaScript-koden.

### <a name="javascript-code-c"></a>JavaScript-kod (C#)

C# använder JavaScript i Index.cshtml för att utnyttja jQuery UI-biblioteket för automatisk komplettering.  Det här biblioteket lägger till funktionen för automatisk komplettering i sökrutan genom asynkrona anrop till MVC-kontrollanten för att hämta rekommendationer.  Låt oss titta på JavaScript-koden i det första exemplet:

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

Den här koden körs i webbläsaren vid sidinläsningen för att konfigurera automatisk komplettering för inmatningsrutan "example1a".  `minLength: 3` ser till att rekommendationerna endast visas när det finns minst tre tecken i sökrutan.  Källvärdet är viktigt:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Den här raden informerar API:et för automatisk komplettering var listan med objekt som ska visas under sökrutan finns.  Eftersom det här är ett MVC-projekt anropas funktionen Suggest (Förslag) i HomeController.cs.  Vi ska titta mer på detta i nästa avsnitt.  Den skickar även några parametrar för att kontrollera markeringar, fuzzy-matchningar och termer.  JavaScript-API:et för automatisk komplettering lägger till term-parametern.

#### <a name="extending-the-sample-to-support-fuzzy-matching"></a>Utöka exemplet för att stödja fuzzy-matchning

Med fuzzy-sökningar kan du få resultat för nära matchningar även om användaren stavar fel på ett ord i sökrutan.  Vi testar detta genom att ändra källraden för att aktivera fuzzy-matchning.

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

### <a name="homecontrollercs-c"></a>HomeController.cs (C#)

Nu när vi har granskat JavaScript-koden för exemplet ska vi titta på C#-kontrollantkoden som faktiskt hämtar rekommendationerna med hjälp av Azure Search .NET SDK.

1. Öppna filen HomeController.cs under katalogen Controllers (Kontrollanter). 

1. Det första du kanske märker är att det finns en metod som heter InitSearch längst upp i klassen.  Metoden skapar en autentiserad HTTP-indexklient till Azure Search-tjänsten.  Om du vill ha mer information om hur detta fungerar kan du gå till följande självstudie: [How to use Azure Search from a .NET Application](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) (Så använder du Azure Search från .NET-program)

1. Gå till funktionen Suggest (Föreslå).

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

Funktionen Suggest (Föreslå) tar två parametrar som bestämmer om träffmarkeringar returneras eller om fuzzy-matchning används utöver sökordsindata.  Metoden skapar ett SuggestParameters-objekt, som sedan skickas till Suggest-API:et. Resultatet konverteras sedan till JSON, så att det kan visas i klienten.
(Valfritt) Lägg till en brytpunkt i början av Suggest-funktionen och stega igenom koden.  Observera svaret som returneras av SDK och hur det konverteras till det resultat som returneras från metoden.

De övriga exemplen på sidan följer samma mönster för att lägga till träffmarkering, rekommendationer för automatisk komplettering i förhand och fasetter för att stödja cachelagring av resultat för automatisk komplettering på klientsidan.  Granska varje exempel så att du förstår hur de fungerar och hur du kan använda dem i din sökfunktion.

### <a name="javascript-language-example"></a>JavaScript-exempel

I JavaScript-exemplet används biblioteket jQuery UI Autocomplete i JavaScript-koden på sidan IndexJavaScript.cshtml.  Det här biblioteket gör det mesta av grovjobbet för att presentera en snygg sökruta och gör det enkelt att göra asynkrona anrop till Azure Search för att hämta rekommendationer.  Låt oss titta på JavaScript-koden i det första exemplet:

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

Om du jämför detta med exemplet ovan som anropar HomeController hittar vi flera likheter.  Konfigurationen för automatisk komplettering för `minLength` och `position` är identiska.  Den stora ändringen här är källan.  I stället för att anropa metoden Suggest (Föreslå) i HomeController skapas en REST-begäran i en JavaScript-funktion och körs med Ajax.  Svaret bearbetas sedan i ”success” och används som källa.

## <a name="takeaways"></a>Lärdomar

I den här självstudien visas de grundläggande stegen för hur du skapar en sökruta med stöd för automatisk komplettering och förslag.  Du fick se hur du kan skapa ett ASP.NET MVC-program och använda antingen .Net SDK eller REST-API:et för Azure Search för att hämta förslag.

## <a name="next-steps"></a>Nästa steg

Integrera förslag och automatisk komplettering i din sökfunktion.  Fundera över hur direkt användning av .Net SDK eller REST-API:et kan ge dina användare tillgång till kraftfulla Azure Search-funktioner när de skriver och göra dem mer produktiva.

> [!div class="nextstepaction"]
> [REST-API för automatisk komplettering](https://docs.microsoft.com/en-us/rest/api/searchservice/autocomplete)
> [REST-API för förslag](https://docs.microsoft.com/en-us/rest/api/searchservice/suggestions)
> [Fasettindexattribut på ett REST-API för att skapa index](https://docs.microsoft.com/en-us/rest/api/searchservice/create-index)


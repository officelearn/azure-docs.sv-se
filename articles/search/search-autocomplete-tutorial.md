---
title: Exempel för att lägga till automatisk komplettering din sökrutan – Azure Search
description: Exempel på hur du kan förbättra slutanvändarens upplevelse av dina datacentrerade program med hjälp av Azure Search-API:er för automatisk komplettering och förslag.
manager: pablocas
author: mrcarter8
services: search
ms.service: search
ms.devlang: NA
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: mcarter
ms.custom: seodec2018
ms.openlocfilehash: b754f00e9bed34717734c4aec81e5489d2c12b63
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200284"
---
# <a name="example-add-autocomplete-to-your-search-box-using-azure-search"></a>Exempel: Lägga till automatisk komplettering i sökrutan med hjälp av Azure Search

I det här exemplet får du lära dig hur du använder [förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions), [automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete) och [fasetter](search-faceted-navigation.md) i den [Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/) och [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.documentsoperationsextensions?view=azure-dotnet) att skapa en kraftfull sökrutan. 

+ Med *förslag* ges rekommendationer av faktiska resultat utifrån det som användaren har skrivit hittills. 
+ *Komplettera automatiskt* är [en ny funktion (förhandsversion)](search-api-preview.md) i Azure Search, som tillhandahåller ord från index för att komplettera det som användaren håller på att skriva. 

Vi jämför flera tekniker för att förbättra användarproduktiviteten genom att ge gedigen sökning till användarna direkt medan de skriver.

Det här exemplet går igenom ett ASP.NET MVC-baserade program som använder C# att anropa den [Azure Search .NET-klientbibliotek](https://aka.ms/search-sdk), och JavaScript för att anropa Azure Search REST API direkt. Programmet för det här exemplet riktar sig mot ett index som fylls i den [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs) exempeldata. Du kan antingen använda det index som redan har konfigurerats i NYC Jobs-demon eller fylla i ett eget index med en datainläsare i NYCJobs-exempellösningen. I exemplet används JavaScript-biblioteken [jQuery UI](https://jqueryui.com/autocomplete/) och [XDSoft](https://xdsoft.net/jqplugins/autocomplete/) för att skapa en sökruta som stöder automatisk komplettering. Genom att använda de här komponenterna med Azure Search kan du se flera exempel på hur du kan ge stöd för automatisk komplettering med förifyllning av förslag i sökrutan.

Du kommer att utföra följande uppgifter:

> [!div class="checklist"]
> * Hämta och konfigurera lösningen
> * Lägga till söktjänstinformation i programinställningar
> * Implementera en sökruta
> * Lägga till stöd för en lista för automatisk komplettering som hämtar data från en extern källa 
> * Hämta förslag och automatisk komplettering med hjälp av .NET SDK och REST API
> * Lägga till cachelagring på klientsidan för att förbättra prestandan 

## <a name="prerequisites"></a>Krav

* Visual Studio 2017. Du kan använda [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/), som är kostnadsfri. 

* Ladda ned exemplet [källkod](https://github.com/azure-samples/search-dotnet-getting-started) för det här exemplet.

* (Valfritt) Ett aktivt Azure-konto och en Azure Search-tjänst. Om du inte har ett Azure-konto, kan du registrera dig för en [kostnadsfri utvärdering](https://azure.microsoft.com/free/). Läs [Skapa en söktjänst](search-create-service-portal.md) om du vill ha hjälp med tjänstetablering. Det kontot och tjänsten är valfritt eftersom det här exemplet kan utföras med hjälp av en värdbaserad NYCJobs index redan på plats för en annan demonstration.

* (Valfritt) Ladda ned [NYCJobs](https://github.com/Azure-Samples/search-dotnet-asp-net-mvc-jobs)-exempelkoden för att importera NYCJobs-data till ett index i din egen Azure Search-tjänst.

> [!Note]
> Om du använder den kostnadsfria Azure Search-tjänsten är du begränsad till tre index. NYCJobs-datainläsaren skapar två index. Se till att det finns utrymme på din tjänst för dessa nya index.

### <a name="set-up-azure-search-optional"></a>Konfigurera Azure Search (valfritt)

Följ stegen i det här avsnittet om du vill importera data för exempelprogrammet NYCJobs till ditt eget index. Det här steget är valfritt.  Om du vill använda exempelindexet hoppar du till nästa avsnitt för att köra exemplet.

1. I mappen DataLoader i NYCJobs-exempelkoden öppnar du filen DataLoader.sln i Visual Studio.

1. Uppdatera anslutningsinformationen för Azure Search-tjänsten.  Öppna App.config i projektet DataLoader och ändra TargetSearchServiceName och TargetSearchServiceApiKey i appinställningarna så att de motsvarar din Azure Search-tjänst och API-nyckeln för din Azure Search-tjänst.  Dessa hittar du i Azure-portalen.

1. Tryck på F5 för att starta programmet.  Detta skapar två index och importerar NYCJob-exempeldata.

1. I exempelkoden exempel öppna AutocompleteTutorial.sln lösningsfilen i Visual Studio.  Öppna Web.config i projektet AutocompleteTutorial och ändra värdena för SearchServiceName och SearchServiceApiKey till samma värden som ovan.

### <a name="running-the-sample"></a>Köra exemplet

Du är nu redo att köra exemplet exempelprogrammet.  Öppna AutocompleteTutorial.sln lösningsfilen i Visual Studio för att köra exemplet.  Lösningen innehåller ett ASP.NET MVC-projekt.  Tryck på F5 för att köra projektet och läsa in sidan i din webbläsare.  Överst på sidan visas en möjlighet att välja C# eller JavaScript.  Den C# alternativet anrop till HomeController från webbläsaren och använder Azure Search .NET SDK för att hämta resultaten.  JavaScript-alternativet anropar REST-API:et för Azure Search direkt från webbläsaren.  Det här alternativet har normalt märkbart bättre prestanda eftersom kontrollanten tas bort från flödet.  Du kan välja det alternativ som passar dina behov och språkinställningar.  Det finns flera exempel på automatisk komplettering på sidan med viss vägledning för vart och ett av dem.  Varje exempel har rekommenderad exempeltext som du kan prova.  Prova att skriva in ett par tecken i varje sökruta och se vad som händer.

## <a name="how-this-works-in-code"></a>Så här fungerar det i koden

Nu när du har sett exemplen i webbläsaren är det dags att gå igenom det första exemplet i detalj för att ta en titt på de komponenter som ingår och hur de fungerar.

### <a name="search-box"></a>Sökruta

Sökrutan är exakt densamma för båda språkalternativen.  Öppna filen Index.cshtml under mappen \Views\Home. Själva sökrutan är enkel:

```html
<input class="searchBox" type="text" id="example1a" placeholder="search">
```

Det här är en enkel textruta för textinmatning med en klass för stil, ett ID som refereras av JavaScript och platshållartext.  Magin sitter i JavaScript-koden.

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

Den här koden körs i webbläsaren vid sidinläsningen för att konfigurera automatisk komplettering för inmatningsrutan ”example1a”.  `minLength: 3` ser till att rekommendationerna endast visas när det finns minst tre tecken i sökrutan.  Källvärdet är viktigt:

```javascript
source: "/home/suggest?highlights=false&fuzzy=false&",
```

Den här raden informerar API:et för automatisk komplettering var den lista med objekt som ska visas under sökrutan finns.  Eftersom det här är ett MVC-projekt anropas funktionen Suggest (Förslag) i HomeController.cs.  Vi ska titta mer på detta i nästa avsnitt.  Den skickar även några parametrar för att kontrollera markeringar, fuzzy-matchningar och termer.  JavaScript-API:et för automatisk komplettering lägger till term-parametern.

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

1. Det första du kanske märker är att det finns en metod som heter InitSearch längst upp i klassen.  Metoden skapar en autentiserad HTTP-indexklient till Azure Search-tjänsten.  Om du vill ha mer information om hur det fungerar finns i följande exempel: [How to use Azure Search from a .NET Application](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk) (Så använder du Azure Search från .NET-program)

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

Om du jämför detta med exemplet ovan som anropar HomeController hittar vi flera likheter.  Konfigurationen för automatisk komplettering för `minLength` och `position` är identisk.  Den stora ändringen här är källan.  I stället för att anropa metoden föreslå i kontrollanten hem, en REST-begäran har skapats i en JavaScript-funktion och körs med Ajax.  Svaret bearbetas sedan i ”success” och används som källa.

## <a name="takeaways"></a>Lärdomar

Det här exemplet visar de grundläggande stegen för att skapa en sökruta som har stöd för automatisk komplettering och förslag.  Såg du hur du kan skapa en ASP.NET MVC-App och använda Azure Search .NET SDK eller REST API för att hämta förslag.

## <a name="next-steps"></a>Nästa steg

Integrera förslag och automatisk komplettering i din sökfunktion.  Överväg att använda .NET SDK eller REST API direkt hur ta kraften i Azure Search till användarna när de skriver för att göra dem mer produktiva.

> [!div class="nextstepaction"]
> [REST-API för automatisk komplettering](https://docs.microsoft.com/rest/api/searchservice/autocomplete)
> [REST-API för förslag](https://docs.microsoft.com/rest/api/searchservice/suggestions)
> [Fasettindexattribut på ett REST-API för att skapa index](https://docs.microsoft.com/rest/api/searchservice/create-index)


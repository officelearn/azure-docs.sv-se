---
title: Webbprogram för Bing webbsökning sida | Microsoft Docs
description: Visar hur du använder Bing webb-API för sökning i en enda sida webbprogram.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 10/04/2017
ms.author: v-jerkin
ms.openlocfilehash: f22e38a1d6ee4042684b9822b58669bed6fe29a0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354603"
---
# <a name="tutorial-single-page-web-app"></a>Självstudier: Single-page-webbprogram

Bing Web Sök API kan du söka på webben och hämta resultaten av olika typer som är relevanta för en sökfråga. I den här självstudiekursen vi skapa en enkel sida webbapp som använder Bing Web Sök API för att visa sökresultat höger på sidan. Programmet innehåller komponenter för HTML, CSS och JavaScript.

<!-- Remove until this can be replaced with a sanitized version.
![[Single-page Bing Web Search app]](media/cognitive-services-bing-web-api/web-search-spa-demo.png)
-->

> [!NOTE]
> JSON- och HTTP-rubriker längst ned på sidan avslöja JSON-svar och HTTP-begäraninformation när du klickar på. Dessa uppgifter är användbara när utforska tjänsten.

Appen självstudiekurs visar hur du:

> [!div class="checklist"]
> * Utför ett Bing Web Sök API-anrop i JavaScript
> * Skicka Sökalternativ till Bing webb-API för sökning
> * Visa Web, nyheter, bild och video sökresultat
> * Bläddra igenom sökresultat
> * Hantera Bing klient-ID och API-prenumeration nyckeln
> * Hantera fel som kan uppstå

Sidan självstudiekursen är helt självständigt; den använder inte någon extern ramverk, formatmallar eller även bildfiler. Den använder endast brett stöd JavaScript språkfunktioner och fungerar med aktuella versioner av alla större webbläsare.

I den här självstudien diskuterar vi endast delar av källkoden. Fullständig källkoden finns [på en separat sida](tutorial-bing-web-search-single-page-app-source.md). Kopiera och klistra in den här koden i en textredigerare och spara den som `bing.html`.

## <a name="app-components"></a>Komponenter för appar

Som ett enda sida webbprogram innehåller självstudiekursen programmet tre delar:

> [!div class="checklist"]
> * HTML - definierar struktur och innehåll på sidan
> * CSS - definierar utseendet på sidan
> * JavaScript - definierar beteendet för sidan

Den här kursen täcker inte de flesta av HTML- eller CSS i detalj, som de är enkla.

HTML-koden innehåller formuläret som användaren anger en fråga och väljer sökalternativ. Formuläret är ansluten till JavaScript som faktiskt utför sökning efter den `<form>` taggens `onsubmit` attribut:

```html
<form name="bing" onsubmit="return newBingWebSearch(this)">
```

Den `onsubmit` hanteraren returnerar `false`, vilket håller formuläret från att skickas till en server. JavaScript-koden i själva verket har arbetet med att samla in nödvändig information i formuläret och sökningen.

HTML-koden innehåller även avdelningar (HTML `<div>` taggar) där sökresultatet visas.

## <a name="managing-subscription-key"></a>Hantera prenumerationen nyckel

Om du vill undvika att behöva innehåller nyckel för Bing Search API-prenumeration i koden, använder vi webbläsarens beständig lagring för att lagra nyckeln. Om ingen nyckel lagras vi efterfrågar den nyckeln och spara den för senare användning. Om nyckeln senare avvisas av API: Ogiltig vi lagrade nyckeln så att användaren kommer att tillfrågas igen.

Vi definiera `storeValue` och `retrieveValue` funktioner som använder antingen det `localStorage` objekt (om webbläsaren stöder den) eller en cookie. Vår `getSubscriptionKey()` funktionen använder dessa funktioner för att lagra och hämta användarens nyckel.

```javascript
// cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/search";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey() {
    var key = retrieveValue(API_KEY_COOKIE);
    while (key.length !== 32) {
        key = prompt("Enter Bing Search API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(API_KEY_COOKIE, key);
    return key;
}
```

HTML `form` taggen `onsubmit` anrop av `bingWebSearch` funktionen för att returnera sökresultat. `bingWebSearch` använder `getSubscriptionKey` att autentisera varje fråga. Enligt tidigare definition `getSubscriptionKey` efterfrågar nyckeln om nyckeln inte har registrerats. Nyckeln lagras sedan för fortlöpande användning av programmet.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="selecting-search-options"></a>Att välja alternativ för sökning

![[Bing webbsökning formuläret]](media/cognitive-services-bing-web-api/web-search-spa-form.png)

HTML-formulär innehåller element med följande namn:

| | |
|-|-|
| `where` | En nedrullningsbar meny för att välja marknaden (plats och language) som används för sökningen. |
| `query` | Fältet att ange sökvillkor. |
| `what` | Kryssrutorna för att främja olika typer av resultaten. Uppgradera bilder, ökar till exempel rangordning av avbildningar. |
| `when` | Listrutan för att du kan också begränsa sökningen till den senaste dag, vecka eller månad. |
| `safe` | En kryssruta som anger om du använder Bings säker sökning för att filtrera bort ”vuxna” resultat. |
| `count` | Dolda fält. Antal sökresultat ska returneras för varje begäran. Ändra om du vill visa färre eller fler resultat per sida. |
| `offset` | Dolda fält. Förskjutning av första sökresultatet i begäran. används för sidindelning. Den återställs till `0` på en ny begäran. |

> [!NOTE]
> Bing webbsökning erbjuder många fler Frågeparametrar. Vi använder bara några av dem här.

JavaScript-funktionen `bingSearchOptions()` konverterar fälten till det format som krävs av API: T för Bing Search.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var what = [];
    for (var i = 0; i < form.what.length; i++) 
        if (form.what[i].checked) what.push(form.what[i].value);
    if (what.length) {
        options.push("promote=" + what.join(","));
        options.push("answerCount=9");
    }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    options.push("textDecorations=true");
    options.push("textFormat=HTML");
    return options.join("&");
}
```

Till exempel den `SafeSearch` parameter i ett verkligt API-anrop kan vara `strict`, `moderate`, eller `off`, med `moderate` som standard. Vår formuläret, men använder en kryssruta som har bara två lägen. JavaScript-kod konverterar den här inställningen på antingen `strict` eller `off` (`moderate` inte används).

Om något av de **befordra** kryssrutorna är markerade kan vi också lägga till en `answerCount` parameter i frågan. `answerCount` krävs när du använder den `promote` parameter. Vi bara ändra värdet till `9` (antal resultattyper som stöds av Bing Web Sök API) och kontrollera vi få det maximala antalet resultattyper.

> [!NOTE]
> Uppgradera en resultattyp inte *garantera* att sökresultatet innehåller den typ av resultat. I stället ökar befordran rangordningen för dessa typer av resultat i förhållande till deras vanliga rangordning. Använd för att begränsa sökningen till olika typer av resultat av `responseFilter` Frågeparametern eller anropa en mer specifik slutpunkt, till exempel Bing Image-sökning eller Bing News Search.

Vi även skicka `textDecoration` och `textFormat` fråga parametrar för att orsaka sökordet som ska vara fetstil i sökresultaten. Dessa värden är hårdkodad i skriptet.

## <a name="performing-the-request"></a>Begäran utfördes

Baserat på frågan, alternativ strängen och API-nyckeln i `BingWebSearch` använder funktionen ett `XMLHttpRequest` objekt att begära Bing webbsökning slutpunkten.

```javascript
// perform a search given query, options string, and API key
function bingWebSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
    var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

    // open the request
    try {
        request.open("GET", queryurl);
    } 
    catch (e) {
        renderErrorMessage("Bad request (invalid URL)\n" + queryurl);
        return false;
    }

    // add request headers
    request.setRequestHeader("Ocp-Apim-Subscription-Key", key);
    request.setRequestHeader("Accept", "application/json");
    var clientid = retrieveValue(CLIENT_ID_COOKIE);
    if (clientid) request.setRequestHeader("X-MSEdge-ClientID", clientid);
    
    // event handler for successful response
    request.addEventListener("load", handleBingResponse);
    
    // event handler for erorrs
    request.addEventListener("error", function() {
        renderErrorMessage("Error completing request");
    });

    // event handler for aborted request
    request.addEventListener("abort", function() {
        renderErrorMessage("Request aborted");
    });

    // send the request
    request.send();
    return false;
}
```

Vid slutförande av HTTP-begäran JavaScript-anrop vår `load` händelsehanterare, den `handleBingResponse()` funktionen sköta en lyckad HTTP GET-begäran-API: et. 

```javascript
// handle Bing search request results
function handleBingResponse() {
    hideDivs("noresults");

    var json = this.responseText.trim();
    var jsobj = {};

    // try to parse JSON results
    try {
        if (json.length) jsobj = JSON.parse(json);
    } catch(e) {
        renderErrorMessage("Invalid JSON response");
        return;
    }

    // show raw JSON and HTTP request
    showDiv("json", preFormat(JSON.stringify(jsobj, null, 2)));
    showDiv("http", preFormat("GET " + this.responseURL + "\n\nStatus: " + this.status + " " + 
        this.statusText + "\n" + this.getAllResponseHeaders()));

    // if HTTP response is 200 OK, try to render search results
    if (this.status === 200) {
        var clientid = this.getResponseHeader("X-MSEdge-ClientID");
        if (clientid) retrieveValue(CLIENT_ID_COOKIE, clientid);
        if (json.length) {
            if (jsobj._type === "SearchResponse" && "rankingResponse" in jsobj) {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    }

    // Any other HTTP response is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSubscriptionKey();

        // some error responses don't have a top-level errors object, so gin one up
        var errors = jsobj.errors || [jsobj];
        var errmsg = [];

        // display HTTP status code
        errmsg.push("HTTP Status " + this.status + " " + this.statusText + "\n");

        // add all fields from all error responses
        for (var i = 0; i < errors.length; i++) {
            if (i) errmsg.push("\n");
            for (var k in errors[i]) errmsg.push(k + ": " + errors[i][k]);
        }

        // also display Bing Trace ID if it isn't blocked by CORS
        var traceid = this.getResponseHeader("BingAPIs-TraceId");
        if (traceid) errmsg.push("\nTrace ID " + traceid);

        // and display the error message
        renderErrorMessage(errmsg.join("\n"));
    }
}
```

> [!IMPORTANT]
> En lyckad HTTP-begäran har *inte* nödvändigtvis som själva lyckades sökningen. Om ett fel uppstår i search-åtgärd, returnerar en 200 HTTP - statuskod Bing webb-API för sökning och innehåller information om fel i JSON-svar. Dessutom returnerar begäran har begränsad hastighet, ett tomt svar på API: et.

Mycket av koden i båda av de här funktionerna är dedikerad till felhantering. Fel kan inträffa i följande steg:

|Fas|Potentiella fel|Hanteras av|
|-|-|-|
|Byggnaden JavaScript request-objektet|Ogiltig URL|`try`/`catch` block|
|Skickar begäran|Nätverksfel, avbrutna anslutningar|`error` och `abort` händelsehanterare|
|Sökningen|Ogiltig förfrågan, ogiltigt JSON hastighetsbegränsningar|testar i `load` händelsehanterare|

Fel hanteras genom att anropa `renderErrorMessage()` med alla detaljer kända om felet. Om svaret klarat fullständig gauntlet fel tester, som vi kallar `renderSearchResults()` att visa sökresultat på sidan.

## <a name="displaying-search-results"></a>Visa sökresultat

Bing Web Sök API [kräver att du vill visa resultat i en angiven ordning](useanddisplayrequirements.md). Eftersom API: et kan returnera olika typer av svar, det räcker inte att gå igenom den översta `WebPages` samling i JSON-svar och visa resultat. (Om du vill bara en typ av resultat använder den `responseFilter` frågeparameter eller en annan slutpunkt för Bing Search.)

I stället, använder vi den `rankingResponse` i sökresultaten kan sortera resultaten för visning. Det här objektet refererar till objekt i den `WebPages` `News`, `Images`, och/eller `Videos` samlingar, eller i andra översta svaret samlingar i JSON-svar.

`rankingResponse` kan innehålla upp till tre samlingar av sökresultat avses `pole`, `mainline`, och `sidebar`. 

`pole`, om det finns är mest relevant sökresultatet och ska visas på en framträdande plats. `mainline` refererar till den största delen av sökresultatet. Likriktade resultaten ska visas omedelbart efter `pole` (eller först och om `pole` finns inte). 

Slutligen. `sidebar` refererar till extra sökresultat. De här resultaten returneras är ofta relaterad sökningar eller bilder. Om möjligt ska dessa resultat visas i en verklig sidopanelen. Om skärmen gränser gör en sidopanelen opraktiska (till exempel på en mobil enhet), de ska visas efter den `mainline` resultat.

Varje objekt i en `rankingResponse` samling refererar till de faktiska resultatet sökobjekt på två olika men motsvarande sätt.

| | |
|-|-|
|`id`|Den `id` ser ut som en URL, men ska inte användas för länkar. Den `id` typ av en rangordning resultat som matchar den `id` av antingen en sökning resultatobjekt i en samling i svaret, *eller* en samling för hela svaret (som `Images`).
|`answerType`, `resultIndex`|Den `answerType` refererar till den översta svar-samling som innehåller resultatet (till exempel `WebPages`). Den `resultIndex` refererar till det resultatet index i samlingen. Om `resultIndex` är utelämnas rangordning resultatet som refererar till hela samlingen.

> [!NOTE]
> Mer information om den här delen av search-svar finns [rang resultat](rank-results.md).

Du kan använda vilken metod för att hitta refererade Sök resultatobjekt passar ditt program. I våra självstudier koden som vi använder den `answerType` och `resultIndex` att hitta varje sökresultat.

Slutligen är det dags att titta på vår funktionen `renderSearchResults()`. Den här funktionen itererar över tre `rankingResponse` samlingar som representerar de tre avsnitten i sökresultaten. För varje avsnitt som vi kallar `renderResultsItems()` att återge resultaten för avsnittet.

```javascript
// render the search results given the parsed JSON response
function renderSearchResults(results) {

    // if spelling was corrected, update search field
    if (results.queryContext.alteredQuery) 
        document.forms.bing.query.value = results.queryContext.alteredQuery;

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);
    
    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

`renderResultsItems()` i sin tur itererar över objekt i varje `rankingResponse` avsnittet, mappas varje rangordning resultat till en sökning resultatet med hjälp av den `answerType` och `resultIndex` fält och anropar återgivningsfunktionen lämplig för att generera den resultatet HTML. 

Om `resultIndex` har inte angetts för ett givet rangordning objekt `renderResultsItems()` itererar över alla resultat av den typen och anropar återgivningsfunktionen för varje objekt. 

Oavsett hur den resulterande HTML infogas i rätt `<div>` element på sidan.

```javascript
// render search results from rankingResponse object in specified order
function renderResultsItems(section, results) {

    var items = results.rankingResponse[section].items;
    var html = [];
    for (var i = 0; i < items.length; i++) {
        var item = items[i];
        // collection name has lowercase first letter while answerType has uppercase
        // e.g. `WebPages` rankingResult type is in the `webPages` top-level collection
        var type = item.answerType[0].toLowerCase() + item.answerType.slice(1);
        // must have results of the given type AND a renderer for it
        if (type in results && type in searchItemRenderers) {
            var render = searchItemRenderers[type];
            // this ranking item refers to ONE result of the specified type
            if ("resultIndex" in item) {
                html.push(render(results[type].value[item.resultIndex], section));
            // this ranking item refers to ALL results of the specified type
            } else {
                var len = results[type].value.length;
                for (var j = 0; j < len; j++) {
                    html.push(render(results[type].value[j], section, j, len));
                }
            }
        }
    }
    return html.join("\n\n");
}
```

## <a name="rendering-result-items"></a>Återgivning resultatet objekt

I vårt JavaScript-kod är ett objekt, `searchItemRenderers`, som innehåller *återgivning:* funktioner som genererar HTML-kod för varje typ av sökresultatet.

```javascript
// render functions for various types of search results
searchItemRenderers = { 
    webPages: function(item) { ... },
    news: function(item) { ... },
    images: function(item, section, index, count) { ... },
    videos: function(item, section, index, count) { ... },
    relatedSearches: function(item, section, index, count) { ... }
}
```

> [!NOTE]
> Våra självstudier app har återgivning för webbsidor, artiklar, bilder, videor och relaterade sökningar. Ditt eget program behöver återgivning för alla typer av resultat som du kan få kan som kan omfatta beräkningar, stavningsförslag, enheter, tidszoner och definitioner.

Några av våra funktionerna som accepterar bara den `item` parameter: ett JavaScript-objekt som representerar ett enda sökresultat. Andra acceptera ytterligare parametrar som kan användas för att återge objekt på olika sätt i olika kontexter. (En renderare som inte använder den här informationen behöver inte godkänna dessa parametrar.)

Kontexten argumenten är:

| | |
|-|-|
|`section`|Resultatavsnittet (`pole`, `mainline`, eller `sidebar`) i objektet visas.
|`index`<br>`count`|Tillgängligt när den `rankingResponse` artikeln anger att alla resultat i en viss samling som ska visas; `undefined` annars. Dessa parametrar får indexet för objektet i dess samling och det totala antalet objekt i samlingen. Du kan den här informationen om du vill numrera resultat för att generera olika HTML för det första eller sista resultatet och så vidare.|

I våra självstudier app både den `images` och `relatedSearches` återgivning använda argumenten kontext för att anpassa de genererar HTML. Låt oss ta en närmare titt på den `images` renderare:

```javascript
searchItemRenderers = { 
    // render image result using thumbnail
    images: function(item, section, index, count) {
        var height = 60;
        var width = Math.round(height * item.thumbnail.width / item.thumbnail.height);
        var html = [];
        if (section === "sidebar") {
            if (index) html.push("<br>");
        } else {
            if (!index) html.push("<p class='images'>");
        }
        html.push("<a href='" + item.hostPageUrl + "'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + 
            "' height=" + height + " width=" + width + " title='" + title + "' alt='" + title + "'>");
        html.push("</a>");
        return html.join("");
    }, // other renderers omitted
}
```

Vår bilder renderare-funktionen:

> [!div class="checklist"]
> * Beräknar storlek på miniatyrbilderna bild (bredd varierar, medan höjd vara högst 60 bildpunkter).
> * Infogar HTML som föregår det bild resultatet beroende på kontext.
> * Skapar HTML `<a>` tagg som länkar till den sida som innehåller bilden.
> * Skapar HTML `<img>` tagg för att visa bilden i avbildningen. 

Bild-återgivningen använder den `section` och `index` variabler för att visa resultat på olika sätt beroende på var de förekommer. En radbrytning (`<br>` tagg) infogas mellan bildresultat i sidopanelen, så att sidopanelen visar en kolumn av avbildningar. I andra avsnitt leda till den första bilden `(index === 0)` föregås av en `<p>` tagg. Miniatyrer annars butt visa varandra och radbyte efter behov i webbläsarfönstret.

Storlek på miniatyrbilderna används i både den `<img>` tagg och `h` och `w` fält i URL: en på miniatyrbilden. Den [miniatyr Bing-tjänsten](resize-and-crop-thumbnails.md) levererar en miniatyr av exakt den storleken. Den `title` och `alt` attribut (en textbeskrivning av bilden) skapas från avbildningens namn och värdnamn i URL: en.

Bilder visas som visas här i likriktade sökresultatet.

![[Bing avbildningen resultat]](media/cognitive-services-bing-web-api/web-search-spa-images.png)

## <a name="persisting-client-id"></a>Spara klient-ID

Svar från Bing search API: er kan innehålla en `X-MSEdge-ClientID` rubrik som ska skickas tillbaka till API: et med efterföljande förfrågningar. Om flera Bing Search API: er används måste samma klient-ID användas med dem om möjligt.

Att tillhandahålla den `X-MSEdge-ClientID` sidhuvudet tillåter Bing-API: er att koppla alla sökningar för en användare, som har två viktiga fördelar.

Först och hjälper främst Bing sökmotor att tillämpa tidigare kontexten sökningar att hitta resultat som bättre uppfyller användaren. Om en användare har har sökt efter villkor som rör avseglingen, till exempel kan företrädesvis senare söka efter ”knutar” kan returnera information om knutar som används i avseglingen.

Andra väljer Bing slumpmässigt användarna får nya funktioner innan de blir allmänt tillgänglig. Att tillhandahålla samma klient-ID för varje begäran säkerställer att användare som har valts för att se en funktion alltid se den. Utan klient-ID kan användaren ser en funktion som visas och försvinner, till synes slumpmässigt, i sökresultaten.

Webbläsaren säkerhetsprinciper (CORS) kan hindra den `X-MSEdge-ClientID` huvudet från att vara tillgängliga för JavaScript. Den här begränsningen uppstår när search-svar har ett annat ursprung från sidan som begärt det. Du bör hantera den här principen genom att lägga upp ett skript på servern som innehåller API-anrop på samma domän som webbsidan i en produktionsmiljö. Eftersom skriptet har samma ursprung som webbsida, den `X-MSEdge-ClientID` huvudet är sedan tillgängliga för JavaScript.

> [!NOTE]
> I en produktionsmiljö webbprogram ska du utföra begäran serversidan ändå. Annars måste Bing Search API-nyckeln ingå i webbsidan där den är tillgänglig för alla som visar källa. Du debiteras för användning i alla under din API prenumeration nyckel även begäranden obehöriga personer så att det är viktigt att inte visa din nyckel.

Du kan göra sökningen Bing webb-API-begäran via en proxyserver för CORS för utveckling. Svaret från en sådan proxy har en `Access-Control-Expose-Headers` huvud som whitelists svarshuvuden och gör dem tillgängliga för JavaScript.

Det är lätt att installera en CORS-proxy för att tillåta huvud-ID för våra självstudier app åtkomst till klienten. Första, om du inte redan har det, [installera Node.js](https://nodejs.org/en/download/). Sedan kör du följande kommando i Kommandotolken:

    npm install -g cors-proxy-server

Ändra Bing webbsökning slutpunkten i HTML-filen:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Slutligen starta CORS-proxy med följande kommando:

    cors-proxy-server

Lämna Kommandotolken öppen när du använder appen självstudiekursen; stänger fönstret stoppar proxyn. I avsnittet nedan sökresultaten utbyggbara HTTP-huvuden, kan du nu se den `X-MSEdge-ClientID` huvud (bland andra) och kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Visual Sök mobilappen självstudiekursen](computer-vision-web-search-tutorial.md)

> [!div class="nextstepaction"]
> [Bing Web Sök API-referens](//docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)

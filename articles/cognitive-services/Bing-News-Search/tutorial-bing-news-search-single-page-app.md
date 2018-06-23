---
title: Bing News-appen sida Sök | Microsoft Docs
description: Förklarar hur du använder Bing News Sök API i en enda sida webbprogram.
services: cognitive-services
author: mikedodaro
manager: ronakshah
ms.service: cognitive-services
ms.component: bing-news-search
ms.topic: article
ms.date: 10/30/2017
ms.author: v-gedod
ms.openlocfilehash: fb8cd24dfdfb03500cc86ee1b1f0126ec044a873
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354906"
---
# <a name="tutorial-single-page-news-search-app"></a>Självstudier: Single-page nyheter söka app
Bing News Sök-API kan du söka på webben och visa resultat av typen nyheter som är relevanta för en sökfråga. I den här självstudiekursen skapar vi en enda sida webbprogram som använder Bing News Sök-API för att visa sökresultat på sidan. Programmet innehåller komponenter för HTML, CSS och JavaScript.

<!-- Remove until we can replace it with sanitized copy
![Single-page Bing News Search app](media/news-search-singlepage.png)
-->

> [!NOTE]
> JSON- och HTTP-rubriker längst ned på sidan när användaren klickar på Visa JSON-svar och information om HTTP-begäran. Dessa uppgifter kan vara användbart när utforska tjänsten.

Appen självstudiekurs visar hur du:
> [!div class="checklist"]
> * Utför ett Bing News Sök API-anrop i JavaScript
> * Skicka Sökalternativ till Bing News Sök-API
> * Visa nyheter sökresultat från fyra kategorier: alla typ, företag, hälsa eller politik från tidsramar 24 timmar, den senaste veckan, månad eller alla tillgängliga tid
> * Bläddra igenom sökresultat
> * Hantera Bing klient-ID och API-prenumeration nyckeln
> * Hantera fel som kan uppstå

Sidan självstudiekursen är helt självständigt; den använder inte någon extern ramverk, formatmallar eller bildfiler. Den använder endast brett stöd JavaScript språkfunktioner och fungerar med aktuella versioner av alla större webbläsare.

I den här självstudien diskuterar vi delar av källkoden. Det fullständiga [källkod](tutorial-bing-news-search-single-page-app-source.md) är tillgänglig. Om du vill köra exemplet, kopiera och klistra in källkoden i en textredigerare och spara den som `bing.html`.

## <a name="app-components"></a>Komponenter för appar
Som ett enda sida webbprogram innehåller den här självstudiekursen programmet tre delar:

> [!div class="checklist"]
> * HTML - definierar struktur och innehåll på sidan
> * CSS - definierar utseendet på sidan
> * JavaScript - definierar beteendet för sidan

De flesta HTML och CSS är vanliga, så kursen inte beskrivs den. HTML-koden innehåller formuläret som användaren anger en fråga och väljer sökalternativ. Formuläret är ansluten till JavaScript som faktiskt utför en sökning med hjälp av den `onsubmit` attribut för den `<form>` tagg:

```html
<form name="bing" onsubmit="return newBingNewsSearch(this)">
```
Den `onsubmit` hanteraren returnerar `false`, vilket håller formuläret från att skickas till en server. JavaScript-koden har arbetet med att samla in nödvändig information i formuläret och sökningen.

HTML-koden innehåller även avdelningar (HTML `<div>` taggar) där sökresultatet visas.

## <a name="managing-subscription-key"></a>Hantera prenumerationen nyckel

Om du vill undvika att behöva innehåller nyckel för Bing Search API-prenumeration i koden, använder vi webbläsarens beständig lagring för att lagra nyckeln. Innan nyckeln lagras ber vi den nyckeln. Om nyckeln senare avvisas av API: Ogiltig vi lagrade nyckeln så att användaren kommer att tillfrågas igen.

Vi definiera `storeValue` och `retrieveValue` funktioner som använder antingen det `localStorage` objekt (inte i alla webbläsare) eller en cookie. Den `getSubscriptionKey()` funktionen använder dessa funktioner för att lagra och hämta användarens nyckel.

``` javascript
// Cookie names for data we store
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";

// Bing Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/news";

// ... omitted definitions of storeValue() and retrieveValue()
// Browsers differ in their support for persistent storage by 
// local HTML files. See the source code for browser-specific
// options.

// Get stored API subscription key, or 
// prompt if it's not found.
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
HTML `<form>` taggen `onsubmit` anrop av `bingWebSearch` funktionen för att returnera sökresultat. `bingWebSearch` använder `getSubscriptionKey()` att autentisera varje fråga. Enligt tidigare definition `getSubscriptionKey` efterfrågar nyckeln om nyckeln inte har registrerats. Nyckeln lagras sedan för fortlöpande användning av programmet.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value, 
    bingSearchOptions(this), getSubscriptionKey())">
```
## <a name="selecting-search-options"></a>Att välja alternativ för sökning
Följande bild visar textrutan fråga och alternativ som definierar en sökning efter nyheter om skolan budget.

![Bing News Sökalternativ](media/news-search-categories.png)

HTML-formulär innehåller element med följande namn:

|Element|Beskrivning|
|-|-|
| `where` | En nedrullningsbar meny för att välja marknaden (plats och language) som används för sökningen. |
| `query` | Textfältet för att ange sökvillkor. |
| `category` | Kryssrutorna för att främja olika typer av resultaten. Uppgradera hälsa, ökar till exempel rangordning hälsa nyheter. |
| `when` | Listrutan för att du kan också begränsa sökningen till den senaste dag, vecka eller månad. |
| `safe` | En kryssruta som anger om du vill använda funktionen Bing säker sökning för att filtrera bort ”vuxna” resultat. |
| `count` | Dolda fält. Antal sökresultat ska returneras för varje begäran. Ändra om du vill visa färre eller fler resultat per sida. |
| `offset`|  Dolda fält. Förskjutning av första sökresultatet i begäran. används för sidindelning. Den återställs till `0` på en ny begäran. |

> [!NOTE]
> Bing webbsökning erbjuder andra Frågeparametrar. Vi använder bara några av dem.

``` javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);

    for (var i = 0; i < form.category.length; i++) {
        if (form.category[i].checked) {
            category = form.category[i].value;
            break;
        }
    }
    if (category.valueOf() != "all".valueOf()) { 
        options.push("category=" + category); 
        }
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

Till exempel den `SafeSearch` parameter i ett verkligt API-anrop kan vara `strict`, `moderate`, eller `off`, med `moderate` som standard. Vår formuläret, men använder en kryssruta som har bara två lägen. JavaScript-kod konverterar den här inställningen på antingen `strict` eller `off` (`moderate` inte används).

## <a name="performing-the-request"></a>Begäran utfördes
Baserat på frågan, alternativ strängen och API-nyckeln i `BingNewsSearch` använder funktionen ett `XMLHttpRequest` objekt att begära Bing News Search-slutpunkten.

```javascript
// perform a search given query, options string, and API key
function bingNewsSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

    var request = new XMLHttpRequest();
     if (category.valueOf() != "all".valueOf()) {
        var queryurl = BING_ENDPOINT + "/search?" + "?q=" + encodeURIComponent(query) + "&" + options;
    }
    else
    {
        if (query){
        var queryurl = BING_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;
        }
        else {
            var queryurl = BING_ENDPOINT + "?" + options;
        }
    }

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
Vid slutförande av HTTP-begäran JavaScript-anrop i `load` händelsehanterare, den `handleBingResponse()` funktionen sköta en lyckad HTTP GET-begäran-API: et. 

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
            if (jsobj._type === "News") {
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
> En lyckad HTTP-begäran har *inte* nödvändigtvis som själva lyckades sökningen. Om ett fel uppstår i search-åtgärd, returnerar en 200 HTTP - statuskod Bing News Sök-API och innehåller information om fel i JSON-svar. Dessutom returnerar begäran har begränsad hastighet, ett tomt svar på API: et.

Mycket av koden i båda av de här funktionerna är dedikerad till felhantering. Fel kan inträffa i följande steg:

|Fas|Potentiella fel|Hanteras av|
|-|-|-|
|Skapa JavaScript request-objektet|Ogiltig URL|`try`/`catch` block|
|Skickar begäran|Nätverksfel, avbrutna anslutningar|`error` och `abort` händelsehanterare|
|Sökningen|Ogiltig förfrågan, ogiltigt JSON hastighetsbegränsningar|testar i `load` händelsehanterare|

Fel hanteras genom att anropa `renderErrorMessage()` med alla detaljer kända om felet. Om svaret klarat fullständig gauntlet fel tester, som vi kallar `renderSearchResults()` att visa sökresultat på sidan.

## <a name="displaying-search-results"></a>Visa sökresultat
Den huvudsakliga funktionen för att visa sökresultaten är `renderSearchResults()`. Den här funktionen tar JSON som returnerats av Bing News Search-tjänsten och återger nyheter resultaten och relaterade sökningar eventuella.

```javascript
// render the search results given the parsed JSON response
    function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```
De huvudsakliga sökresultaten visas som den översta `value` objekt i JSON-svar. Vi skickar dem till vår funktionen `renderResults()`, som går igenom dem och anropar en separat funktion för att återge varje objekt i HTML-format. Den resulterande HTML returneras till `renderSearchResults()`, där den infogas i den `results` division på sidan.

```javascript
function renderResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.news(items[i], i, len));
    }
    return html.join("\n\n");
}
```
Sök-API Bing News returnerar upp till fyra olika typer av relaterade resultat i sin egen översta objektet. De är:

|Relation|Beskrivning|
|-|-|
|`pivotSuggestions`|Frågor som ersätter ett pivot ord i ursprungliga sökning med en annan. Till exempel om du söker efter ”röda blommor” en pivot-word kan vara ”red” och förslag på en pivot kan vara ”gula blommor”.|
|`queryExpansions`|Frågor som begränsar ursprungliga sökningen genom att lägga till flera villkor. Till exempel om du söker efter ”Microsoft Surface” en ökning av frågan kan vara ”Microsoft Surface Pro”.|
|`relatedSearches`|Frågor som också har registrerats av andra användare som har registrerats ursprungliga sökningen. Sökningen till exempel om du söker efter ”Mount Rainier” en relaterad kan gå ”om Mt. Saint Helens ”.|
|`similarTerms`|Frågor med liknande innebörd för den ursprungliga sökningen. Till exempel om du söker efter ”skolorna” kanske motsvarande period ”education”.|

Som tidigare setts i `renderSearchResults()`, vi återge endast den `relatedItems` förslag och placera den resulterande länkar i sidans sidopanelen.

## <a name="rendering-result-items"></a>Återgivning resultatet objekt

I JavaScript code objektet `searchItemRenderers`, innehåller *återgivning:* funktioner som genererar HTML-kod för varje typ av sökresultatet.

```javascript
searchItemRenderers = {
    news: function(item) { ... },
    webPages: function (item) { ... }, 
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```
En renderare funktion kan acceptera följande parametrar:

|Parameter|Beskrivning|
|-|-|
|`item`| JavaScript-objekt som innehåller objektets egenskaper, till exempel dess URL och dess beskrivning.|
|`index`| Index över resultatobjekt i dess samling.|
|`count`| Antal objekt i samlingen Sök resultatobjekt.|

Den `index` och `count` parametrar kan användas för att antalet resultat för att generera särskilda HTML för början eller slutet av en samling om du vill infoga radbrytningar efter ett visst antal objekt, och så vidare. Om en renderare inte behöver den här funktionen, behöver det inte godkänna dessa två parametrar.

Den `news` renderare visas i följande javascript utdrag:
```javascript
    // render news story
    news: function (item) {
        var html = [];
        html.push("<p class='news'>");
        if (item.image) {
            width = 60;
            height = Math.round(width * item.image.thumbnail.height / item.image.thumbnail.width);
            html.push("<img src='" + item.image.thumbnail.contentUrl +
                "&h=" + height + "&w=" + width + "' width=" + width + " height=" + height + ">");
        }
        html.push("<a href='" + item.url + "'>" + item.name + "</a>");
        if (item.category) html.push(" - " + item.category);
        if (item.contractualRules) {    // MUST display source attributions
            html.push(" (");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++)
                rules.push(item.contractualRules[i].text);
                html.push(rules.join(", "));
                html.push(")");
            }
        html.push(" (" + getHost(item.url) + ")");
        html.push("<br>" + item.description);
        return html.join("");
    },
```
Funktionen nyheter renderare:
> [!div class="checklist"]
> * Skapar en styckestagg och tilldelar den till den `news` klassen och skickar den till html-matrisen.
> * Beräknar storlek på miniatyrbilderna för avbildningen (bredd vara högst 60 bildpunkter höjd beräknas proportionellt).
> * Skapar HTML `<img>` tagg för att visa bilden i avbildningen. 
> * Skapar HTML `<a>` taggar som länkar till avbildningen och den sida som innehåller den.
> * Skapar beskrivning som visar information om bilden och den plats som den är aktiverad.

Storlek på miniatyrbilderna används i både den `<img>` tagg och `h` och `w` fält i URL: en på miniatyrbilden. Den [miniatyr Bing-tjänsten](resize-and-crop-thumbnails.md) levererar en miniatyr av exakt den storleken.

## <a name="persisting-client-id"></a>Spara klient-ID
Svar från Bing search API: er kan innehålla en `X-MSEdge-ClientID` rubrik som ska skickas tillbaka till API: et med efterföljande förfrågningar. Om flera Bing Search API: er används måste samma klient-ID användas med dem om möjligt.

Att tillhandahålla den `X-MSEdge-ClientID` sidhuvudet tillåter Bing-API: er att koppla alla sökningar för en användare, som har två viktiga fördelar.

Först och hjälper främst Bing sökmotor att tillämpa tidigare kontexten sökningar att hitta resultat som bättre uppfyller användaren. Om en användare har har sökt efter villkor som rör avseglingen, till exempel kan företrädesvis senare söka efter ”knutar” kan returnera information om knutar som används i avseglingen.

Andra väljer Bing slumpmässigt användarna får nya funktioner innan de blir allmänt tillgänglig. Att tillhandahålla samma klient-ID för varje begäran säkerställer att användare som finns i funktionen alltid se den. Utan klient-ID kan användaren ser en funktion som visas och försvinner, till synes slumpmässigt, i sökresultaten.

Webbläsaren säkerhetsprinciper (CORS) kan hindra den `X-MSEdge-ClientID` huvudet från att vara tillgängliga för JavaScript. Den här begränsningen uppstår när search-svar har ett annat ursprung från sidan som begärt det. Du bör hantera den här principen genom att lägga upp ett skript på servern som innehåller API-anrop på samma domän som webbsidan i en produktionsmiljö. Eftersom skriptet har samma ursprung som webbsida, den `X-MSEdge-ClientID` huvudet är sedan tillgängliga för JavaScript.

> [!NOTE]
> Du bör utföra begäran serversidan i produktion webbprogram. Annars måste Bing Search API-nyckeln ingå i webbsidan där den är tillgänglig för alla som visar källa. Du debiteras för användning i alla under din API prenumeration nyckel även begäranden obehöriga personer så att det är viktigt att inte visa din nyckel.

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
> [Bing News Sök API-referens](//docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)
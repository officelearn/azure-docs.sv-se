---
title: 'Självstudier: Skapa ensidig webbapp – API för bildsökning i Bing'
titleSuffix: Azure cognitive services
description: Med API för bildsökning i Bing kan du söka på webben efter relevanta bilder med hög kvalitet. Använd den här självstudien för att skapa ett enkelsidigt program som kan skicka sökfrågor till API:et och visa resultaten inom webbsidan.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: tutorial
ms.date: 03/05/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: a9dba9222816ef2cc7891ff7f803e6a7409802a4
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2020
ms.locfileid: "94593458"
---
# <a name="tutorial-create-a-single-page-app-using-the-bing-image-search-api"></a>Självstudier: Skapa en ensidesapp med hjälp av API för bildsökning i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Med API för bildsökning i Bing kan du söka på webben efter relevanta bilder med hög kvalitet. Använd den här självstudien för att skapa ett enkelsidigt program som kan skicka sökfrågor till API:et och visa resultaten inom webbsidan. Den här självstudiekursen liknar motsvarande [självstudiekurs](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md) för webbsökning i Bing.

I den här självstudieappen visas hur du:

> [!div class="checklist"]
> * Anropa API för bildsökning i Bing i JavaScript
> * Förbättra sökresultat med hjälp av alternativ för sökning
> * Visa och bläddra igenom sökresultat
> * Begära och hantera en API-prenumerationsnyckel och klient-ID för Bing.

Den fullständiga källkoden till det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/tree/master/Tutorials/Bing-Image-Search).

## <a name="prerequisites"></a>Förutsättningar

* Den senaste versionen av [Node.js](https://nodejs.org/).
* Ramverket [Express.js](https://expressjs.com/) för Node.js. Installationsinstruktioner för källkoden finns i GitHub-exemplets readme-fil.

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="manage-and-store-user-subscription-keys"></a>Hantera och lagra användarens prenumerationsnycklar

Det här programmet använder webbläsarens beständiga lagring för att lagra prenumerationsnycklar för API:et. Om ingen nyckel lagras frågar webbsidan användaren om nyckeln och lagrar den för senare användning. Om nyckeln senare avvisas av API appen tas den bort från lagringen. I det här exemplet används den globala slut punkten. Du kan också använda den [anpassade slut domänen](../../cognitive-services/cognitive-services-custom-subdomains.md) som visas i Azure Portal för din resurs.


Vi definierar funktionerna `storeValue` och `retrieveValue`, antingen med objektet `localStorage` (inte i alla webbläsare) eller en cookie.

```javascript
// Cookie names for data being stored
API_KEY_COOKIE   = "bing-search-api-key";
CLIENT_ID_COOKIE = "bing-search-client-id";
// The Bing Image Search API endpoint
BING_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/images/search";

try { //Try to use localStorage first
    localStorage.getItem;   

    window.retrieveValue = function (name) {
        return localStorage.getItem(name) || "";
    }
    window.storeValue = function(name, value) {
        localStorage.setItem(name, value);
    }
} catch (e) {
    //If the browser doesn't support localStorage, try a cookie
    window.retrieveValue = function (name) {
        var cookies = document.cookie.split(";");
        for (var i = 0; i < cookies.length; i++) {
            var keyvalue = cookies[i].split("=");
            if (keyvalue[0].trim() === name) return keyvalue[1];
        }
        return "";
    }
    window.storeValue = function (name, value) {
        var expiry = new Date();
        expiry.setFullYear(expiry.getFullYear() + 1);
        document.cookie = name + "=" + value.trim() + "; expires=" + expiry.toUTCString();
    }
}
```

Funktionen `getSubscriptionKey()` försöker hämta en tidigare lagrad nyckel med hjälp av `retrieveValue`. Det inte går att hitta en nyckel kommer användaren att uppmanas att ange en nyckel som lagras med `storeValue`.

```javascript

// Get the stored API subscription key, or prompt if it's not found
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

HTML-taggen `<form>``onsubmit` anropar `bingWebSearch`-funktionen för att returnera sökresultat. `bingWebSearch` använder `getSubscriptionKey` för att autentisera varje fråga. Som du ser i den föregående definitionen ber `getSubscriptionKey` användaren om nyckeln om nyckeln inte har registrerats. Nyckeln lagras sedan för fortlöpande användning av programmet.

```html
<form name="bing" onsubmit="this.offset.value = 0; return bingWebSearch(this.query.value,
bingSearchOptions(this), getSubscriptionKey())">
```

## <a name="send-search-requests"></a>Skicka sökförfrågningar

Det här programmet använder ett HTML `<form>` för att inledningsvis skicka användarsökförfrågningar med hjälp av attributet `onsubmit` för att anropa `newBingImageSearch()`.

```html
<form name="bing" onsubmit="return newBingImageSearch(this)">
```

`onsubmit`-hanteraren returnerar `false`, som ser till att formuläret inte skickas.

## <a name="select-search-options"></a>Välj sökalternativ

![[Bildsökformulär i Bing]](media/cognitive-services-bing-images-api/image-search-spa-form.png)

Sökning i Bing tillhandahåller flera [filtrerfrågeparametrar](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#filter-query-parameters) för att begränsa och filtrera sökresultaten. HTML-formulär i det här programmet använder och visar följande parameteralternativ:

| Alternativ | Beskrivning |
|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `where`      | En listruta för att välja marknad (plats och språk) som används för sökningen.                                                                                             |
| `query`      | Textfältet för att ange sökvillkor.                                                                                                                                 |
| `aspect`     | Alternativknapparna för att välja proportioner för den hittade bilden: ungefär kvadratisk, bred eller hög.                                                                                     |
| `color`      |                                                                                                                                                                                    |
| `when`       | Listruta för att valfritt begränsa sökningen till den senaste dagen, veckan eller månaden.                                                                                          |
| `safe`       | En kryssruta som anger om du vill använda Bing SafeSearch-funktionen för att filtrera bort ”vuxeninnehåll”.                                                                                      |
| `count`      | Dolt fält. Antal sökresultat som returneras för varje begäran. Ändra om du vill visa färre eller fler resultat per sida.                                                            |
| `offset`     | Dolt fält. Förskjutningen av det första sökresultatet i begäran. Används för växling. Den återställs till `0` vid en ny begäran.                                                           |
| `nextoffset` | Dolt fält. Vid mottagning av ett sökresultat anges fältet som värdet för `nextOffset` i svaret. Med hjälp av det här fältet undviks överlappande resultat på efterföljande sidor. |
| `stack`      | Dolt fält. En JSON-kodad lista med förskjutningar i de föregående sidorna med sökresultat för att gå tillbaka till föregående sidor.                                                      |

Funktionen `bingSearchOptions()` formaterar dessa alternativ till en partiell frågesträng som kan användas i appens API-begäranden.  

```javascript
// Build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.when.value.length) options.push("freshness=" + form.when.value);
    var aspect = "all";
    for (var i = 0; i < form.aspect.length; i++) {
        if (form.aspect[i].checked) {
            aspect = form.aspect[i].value;
            break;
        }
    }
    options.push("aspect=" + aspect);
    if (form.color.value) options.push("color=" + form.color.value);
    options.push("count=" + form.count.value);
    options.push("offset=" + form.offset.value);
    return options.join("&");
}
```

## <a name="performing-the-request"></a>Utföra förfrågan

Beroende på sökfrågan, alternativsträngen och API-nyckeln använder funktionen `BingImageSearch()` ett XMLHttpRequest-objekt för att göra begäran till Bing-bildsökningsslutpunkten.


```javascript
// perform a search given query, options string, and API key
function bingImageSearch(query, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("results", "related", "_json", "_http", "paging1", "paging2", "error");

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

När HTTP-begäran har slutförts anropar JavaScript `handleBingResponse()`-belastningshändelsehanteraren, för att hantera en lyckad HTTP GET-begäran.

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
            if (jsobj._type === "Images") {
                if (jsobj.nextOffset) document.forms.bing.nextoffset.value = jsobj.nextOffset;
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
> Lyckade HTTP-begäranden kan innehålla information om misslyckade sökningar. Om ett fel uppstår i sökåtgärden returnerar API för bildsökning i Bing en icke-200-HTTP-statuskod och felinformation i JSON-svaret. Om begäran var begränsad returnerar API:et ett tomt svar.

## <a name="display-the-search-results"></a>Visa sökresultat

Sökresultaten visas som funktionen `renderSearchResults()`, vilket tar den JSON som returneras av tjänsten för bildsökning i Bing och anropar en lämplig återgivningsfunktion på returnerade bilder och relaterade sökningar.

```javascript
function renderSearchResults(results) {

    // add Prev / Next links with result count
    var pagingLinks = renderPagingLinks(results);
    showDiv("paging1", pagingLinks);
    showDiv("paging2", pagingLinks);

    showDiv("results", renderImageResults(results.value));
    if (results.relatedSearches)
        showDiv("sidebar", renderRelatedItems(results.relatedSearches));
}
```

Sökresultaten returneras som `value`-objekt på den översta nivån i JSON-svaret. Dessa skickas till `renderImageResults()`, som går igenom resultaten och konverterar varje objekt till HTML-format.

```javascript
function renderImageResults(items) {
    var len = items.length;
    var html = [];
    if (!len) {
        showDiv("noresults", "No results.");
        hideDivs("paging1", "paging2");
        return "";
    }
    for (var i = 0; i < len; i++) {
        html.push(searchItemRenderers.images(items[i], i, len));
    }
    return html.join("\n\n");
}
```

API för bildsökning i Bing kan returnera fyra typer av sökförslag för att vägleda användarnas sökmiljöer, var och en i den översta objektnivån:

| Förslag         | Beskrivning                                                                                                                                                                                                         |
|--------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `pivotSuggestions` | Frågor som ersätter ett pivotord i den ursprungliga sökningen med ett annat. Om du till exempel söker efter ”röda blommor” kan ett pivotord vara ”röda”, och ett pivotförslag kan vara ”gula blommor”. |
| `queryExpansions`  | Frågor som begränsar den ursprungliga sökningen genom att lägga till fler termer. Om du exempelvis söker efter ”Microsoft Surface” kan en frågeexpansion vara ”Microsoft Surface Pro”.                                   |
| `relatedSearches`  | Frågor som också har angetts av andra användare som registrerade den ursprungliga sökningen. Om du till exempel söker efter ”Mount Rainier” kan en relaterad sökning vara ”Mt. Saint Helens.”                       |
| `similarTerms`     | Frågor vars innebörd liknar den ursprungliga sökningen. Om du exempelvis söker efter ”kattungar” kan en liknande term vara ”gulliga”.                                                                   |

Med detta program återges endast `relatedItems`-förslag och de resulterande länkarna placeras i sidans sidopanel.

## <a name="rendering-search-results"></a>Återgivning av sökresultat

I programmet innehåller `searchItemRenderers`-objektet återgivningsfunktioner som genererar HTML för varje typ av sökresultat.

```javascript
searchItemRenderers = {
    images: function(item, index, count) { ... },
    relatedSearches: function(item) { ... }
}
```

En funktion för återgivning kan acceptera följande parametrar:

| Parameter         | Beskrivning                                                                                              |
|---------|----------------------------------------------------------------------------------------------|
| `item`  | JavaScript-objekt som innehåller objektets egenskaper, som dess webbadress och en beskrivning. |
| `index` | Index för resultatobjektet i en samling.                                          |
| `count` | Antal objekt i sökresultatets objektsamling.                                  |

Parametrarna `index` och `count` används för att numrera resultat, generera HTML-kod för samlingar och ordna innehåll. Mer specifikt:

* Beräknar storleken på miniatyrbilderna (bredd varierar med minst 120 bildpunkter medan höjden högst får vara 90 bildpunkter).
* Skapar HTML `<img>`-taggen för att visa miniatyrbilden.
* Skapar HTML `<a>`-taggar som länkar till bilden och den sida som innehåller den.
* Skapar beskrivning som visar information om bilden och den plats som den finns på.

```javascript
    images: function (item, index, count) {
        var height = 120;
        var width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
        var html = [];
        if (index === 0) html.push("<p class='images'>");
        var title = escape(item.name) + "\n" + getHost(item.hostPageDisplayUrl);
        html.push("<p class='images' style='max-width: " + width + "px'>");
        html.push("<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width +
            "' height=" + height + " width=" + width + "'>");
        html.push("<br>");
        html.push("<nobr><a href='" + item.contentUrl + "'>Image</a> - ");
        html.push("<a href='" + item.hostPageUrl + "'>Page</a></nobr><br>");
        html.push(title.replace("\n", " (").replace(/([a-z0-9])\.([a-z0-9])/g, "$1.<wbr>$2") + ")</p>");
        return html.join("");
    }, // relatedSearches renderer omitted
```

Miniatyrbildernas `height` och `width` används i både `<img>`-taggen och fälten `h` och `w` i miniatyrbildens webbadress. På så sätt kan Bing returnera [en miniatyrbild](../bing-web-search/resize-and-crop-thumbnails.md) med den exakta storleken.

## <a name="persisting-client-id"></a>Bestående klient-ID

Svar från API:er för Bing Search kan innehålla ett `X-MSEdge-ClientID`-huvud som ska skickas tillbaka till API:et med efterföljande förfrågningar. Om flera API:er för Bing-sökning används ska samma klient-ID användas för dem om möjligt.

När `X-MSEdge-ClientID`-huvudet tillhandahålls kan Bing-API:er associera alla sökningar för en användare, vilket är användbart i

Först hjälper Bing-sökmotorn till med att tillämpa tidigare kontexter på sökningarna för att hitta resultat som bättre tillfredsställer användaren. Om en användare tidigare har sökt efter termer som exempelvis relaterar till segling kan en senare sökning efter ”knopar” returnera information om knopar som används vid segling.

Därefter väljer Bing slumpmässigt ut användare som ska prova nya funktioner innan de blir allmänt tillgängliga. Genom att tillhandahålla samma klient-ID med varje begäran säkerställs att användare som har valts för att se en funktion alltid ser den. Utan klient-ID kan användaren se en funktion som sedan försvinner, till synes slumpmässigt, i sökresultatet.

Säkerhetsprinciper för webbläsaren (CORS) kan hindra att `X-MSEdge-ClientID`-huvudet visas för JavaScript. Den här begränsningen uppstår när söksvaret har ett annat ursprung än sidan som begärt det. I en produktionsmiljö bör du hantera den här principen genom att lägga upp ett serverskript som gör API-anrop på samma domän som webbsidan. Eftersom skriptet har samma ursprung som webbsidan är sedan `X-MSEdge-ClientID`-huvudet tillgängligt för JavaScript.

> [!NOTE]
> Du bör utföra begäran på serversidan i ett produktionsklart webbprogram ändå. I annat fall måste API-nyckeln för Bing-sökning inkluderas i webbsidan där den är tillgänglig för alla som visar källan. Du debiteras för all användning under din API-prenumerationsnyckel, även begäranden som görs av obehöriga personer, så det är viktigt att inte exponera nyckeln.

I utvecklingssyfte kan du begära API för webbsökning i Bing via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers` huvud som tillåter svarshuvuden och gör dem tillgängliga för Java Script.

Det är enkelt att installera en CORS-proxy för att tillåta att självstudien får åtkomst till klientens ID-huvud. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Sedan kör du följande kommando i ett kommandofönster:

```console
npm install -g cors-proxy-server
```

Ändra sedan Webbsökning i Bing-slutpunkten i HTML-filen till: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Slutligen startar du CORS-proxyn med följande kommando:

```console
cors-proxy-server
```

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Extrahera avbildningsinformation med hjälp av API för bildsökning i Bing](tutorial-image-post.md)

## <a name="see-also"></a>Se även

* [API-referens för bildsökning i Bing](//docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)
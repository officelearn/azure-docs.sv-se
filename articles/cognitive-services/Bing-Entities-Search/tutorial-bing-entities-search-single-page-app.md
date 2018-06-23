---
title: Webbprogram för Bing enheten Sök sida | Microsoft Docs
description: Visar hur du använder Bing enheten Sök API i en enda sida webbprogram.
services: cognitive-services
author: v-jerkin
manager: ehansen
ms.service: cognitive-services
ms.component: bing-entity-search
ms.topic: article
ms.date: 12/08/2017
ms.author: v-jerkin
ms.openlocfilehash: 91c60913cd806baf100e5511cbf59299bf9a84f0
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354921"
---
# <a name="tutorial-single-page-web-app"></a>Självstudier: Single-page-webbprogram

Bing enheten Sök-API kan du söka efter information på webben om *entiteter* och *placeras.* Du kan begära typ av resultat, eller både och, i en given fråga. Definitionerna av platser och enheter som anges nedan.

|||
|-|-|
|Entiteter|Välkända personer, platser och saker som du Sök efter namn|
|Platser|Hotell, hotell och andra lokala företag som du hittar namnet *eller* efter typ (italienska hotell)|

I den här självstudiekursen kommer vi skapa en enda sida webbprogram som använder Bing enheten Sök-API för att visa sökresultat höger på sidan. Programmet innehåller komponenter för HTML, CSS och JavaScript.

API: et kan du prioritera resultaten efter plats. Du kan be enheten för en egen plats i en mobil app. I en webbapp som du kan använda den `getPosition()` funktion. Det här anropet fungerar endast i säkra kontexter men ger inte en exakt plats. Användaren kan också söka efter entiteter nära en annan plats än sina egna.

Vår app uppmanas därför Bing Maps-tjänsten för att erhålla latitud och longitud från en plats som anges av användaren. Användaren kan ange namnet på en Landmärke (”utrymme nålen”) eller en fullständig eller partiell adress (”New York City”) och Bing Maps API innehåller koordinaterna.

<!-- Remove until we can replace with a sanitized version.
![[Single-page Bing Entity Search app]](media/entity-search-spa-demo.png)
-->

> [!NOTE]
> JSON- och HTTP-rubriker längst ned på sidan avslöja JSON-svar och HTTP-begäraninformation när du klickar på. Dessa uppgifter är användbara när utforska tjänsten.

Appen självstudiekurs visar hur du:

> [!div class="checklist"]
> * Utför ett Bing enheten Sök API-anrop i JavaScript
> * Utföra en Bing Maps `locationQuery` API-anrop i JavaScript
> * Skicka Sökalternativ till API-anrop
> * Visa sökresultat
> * Hantera Bing klient-ID och API-prenumeration nycklarna
> * Åtgärda eventuella fel som kan uppstå

Sidan självstudiekursen är helt självständigt; den använder inte någon extern ramverk, formatmallar eller även bildfiler. Den använder endast brett stöd JavaScript språkfunktioner och fungerar med aktuella versioner av alla större webbläsare.

I den här självstudien diskuterar vi endast delar av källkoden. Fullständig källkoden finns [på en separat sida](tutorial-bing-entities-search-single-page-app-source.md). Kopiera och klistra in den här koden i en textredigerare och spara den som `bing.html`.

> [!NOTE]
> Den här självstudiekursen liknar avsevärt den [sida Bing webbsökning app kursen](../Bing-Web-Search/tutorial-bing-web-search-single-page-app.md), men endast behandlar sökresultat för entiteten.

## <a name="app-components"></a>Komponenter för appar

Som ett enda sida webbprogram innehåller självstudiekursen programmet tre delar:

> [!div class="checklist"]
> * HTML - definierar struktur och innehåll på sidan
> * CSS - definierar utseendet på sidan
> * JavaScript - definierar beteendet för sidan

Den här kursen täcker inte de flesta av HTML- eller CSS i detalj, som de är enkla.

HTML-koden innehåller formuläret som användaren anger en fråga och väljer sökalternativ. Formuläret är ansluten till JavaScript som faktiskt utför sökning efter den `<form>` taggens `onsubmit` attribut:

```html
<form name="bing" onsubmit="return newBingEntitySearch(this)">
```

Den `onsubmit` hanteraren returnerar `false`, vilket håller formuläret från att skickas till en server. JavaScript-koden i själva verket har arbetet med att samla in nödvändig information i formuläret och sökningen.

Sökningen görs i två faser. Om användaren har angett en plats-begränsning, görs först en Bing Maps-fråga för att konvertera den till koordinater. Återanropet för den här frågan sedan av systemtillstånd aktiveras Bing enheten sökfråga.

HTML-koden innehåller även avdelningar (HTML `<div>` taggar) där sökresultatet visas.

## <a name="managing-subscription-keys"></a>Hantera prenumerationen nycklar

> [!NOTE]
> Den här appen kräver prenumeration nycklar för Bing Search-API och Bing Maps API. Du kan använda en [utvärderingsversion Bing söknyckeln](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) och en [grundläggande Bing Maps nyckeln](https://www.microsoft.com/maps/create-a-bing-maps-key).

Vi använder webbläsarens beständig lagring för att lagra dem om du vill undvika att inkludera tangenterna Bing Search och Bing Maps API-prenumeration i koden. Om antingen nyckeln inte har lagrats, vi efterfrågar det och spara den för senare användning. Om nyckeln senare avvisas av API: Ogiltig vi lagrade nyckeln så uppmanas användaren för den vid nästa sökningen.

Vi definiera `storeValue` och `retrieveValue` funktioner som använder antingen det `localStorage` objekt (om webbläsaren stöder den) eller en cookie. Vår `getSubscriptionKey()` funktionen använder dessa funktioner för att lagra och hämta användarens nyckel.

```javascript
// cookie names for data we store
SEARCH_API_KEY_COOKIE = "bing-search-api-key";
MAPS_API_KEY_COOKIE   = "bing-maps-api-key";
CLIENT_ID_COOKIE      = "bing-search-client-id";

// API endpoints
SEARCH_ENDPOINT = "https://api.cognitive.microsoft.com/bing/v7.0/entities";
MAPS_ENDPOINT   = "http://dev.virtualearth.net/REST/v1/Locations";

// ... omitted definitions of storeValue() and retrieveValue()

// get stored API subscription key, or prompt if it's not found
function getSubscriptionKey(cookie_name, key_length, api_name) {
    var key = retrieveValue(cookie_name);
    while (key.length !== key_length) {
        key = prompt("Enter " + api_name + " API subscription key:", "").trim();
    }
    // always set the cookie in order to update the expiration date
    storeValue(cookie_name, key);
    return key;
}

function getMapsSubscriptionKey() {
    return getSubscriptionKey(MAPS_API_KEY_COOKIE, 64, "Bing Maps");
}

function getSearchSubscriptionKey() {
    return getSubscriptionKey(SEARCH_API_KEY_COOKIE, 32, "Bing Search");
}
```

HTML `<body>` taggen innehåller ett `onload` attribut som anropar `getSearchSubscriptionKey()` och `getMapsSubscriptionKey()` när sidan har lästs in. Dessa anrop fungera att uppmana användaren att ange sina nycklar omedelbart om de inte har angett dem ännu.

```html
<body onload="document.forms.bing.query.focus(); getSearchSubscriptionKey(); getMapsSubscriptionKey();">
```

## <a name="selecting-search-options"></a>Att välja alternativ för sökning

![[Bing enheten formulär]](media/entity-search-spa-form.png)

HTML-formulär innehåller följande kontroller:

| | |
|-|-|
|`where`|En nedrullningsbar meny för att välja marknaden (plats och language) som används för sökningen.|
|`query`|Fältet att ange sökvillkor.|
|`safe`|En kryssruta som anger om säker sökning är aktiverad (begränsar ”vuxna” resultaten)|
|`what`|En meny för att välja att söka efter personer, platser eller båda.|
|`mapquery`|Fältet som användaren kan ange en fullständig eller partiell adress, ett Landmärke osv att Bing enheten returnerade mer relevant sökresultat.|

> [!NOTE]
> Det finns för närvarande platser resultat endast i USA. Den `where` och `what` menyer har kod för att tillämpa den här begränsningen. Om du väljer en-US marknad när platser har valts i den `what` menyn `what` ändras till något. Om du väljer platser när en-US marknad har valts i den `where` menyn `where` ändringar i USA.

Vårt JavaScript-funktionen `bingSearchOptions()` konverterar dessa fält till en partiell frågesträng för Bing Search-API.

```javascript
// build query options from the HTML form
function bingSearchOptions(form) {

    var options = [];
    options.push("mkt=" + form.where.value);
    options.push("SafeSearch=" + (form.safe.checked ? "strict" : "off"));
    if (form.what.selectedIndex) options.push("responseFilter=" + form.what.value);
    return options.join("&");
}
```

Funktionen Säker sökning kan till exempel vara `strict`, `moderate`, eller `off`, med `moderate` som standard. Men formuläret använder en kryssruta som har bara två lägen. JavaScript-kod konverterar den här inställningen på antingen `strict` eller `off` (används inte `moderate`).

Den `mapquery` fält som inte hanteras i `bingSearchOptions()` eftersom den används för Bing Maps plats frågan, inte för Bing enheten sökning.

## <a name="obtaining-a-location"></a>Hämta en plats

Bing Maps API erbjuder en [ `locationQuery` metoden](//msdn.microsoft.com/library/ff701711.aspx), som vi använder för att hitta latitud och longitud för platsen användaren anger. Dessa koordinater skickas till Bing enheten Sök-API med användarens begäran. Sökresultaten prioritera entiteter och platser som närmar sig den angivna platsen.

Det går inte att komma åt Bing Maps-API med hjälp av en vanlig `XMLHttpRequest` fråga i en webbapp eftersom tjänsten inte har stöd för cross-origin-frågor. Det stöder Lyckligtvis hanteras JSONP (”P” är för ”utfyllnad”). Ett hanteras JSONP-svar är en vanlig JSON-svar kapslas in i ett funktionsanrop. Begäran gjordes genom att använda en `<script>` tagg i dokumentet. (Läser in skript omfattas inte webbläsaren säkerhetsprinciper.)

Den `bingMapsLocate()` funktionen skapas och infogas i `<script>` tagg för frågan. Den `jsonp=bingMapsCallback` segmentet i frågesträngen anger namnet på funktionen som ska anropas med svaret.

```javascript
function bingMapsLocate(where) {

    where = where.trim();
    var url = MAPS_ENDPOINT + "?q=" + encodeURIComponent(where) + 
                "&jsonp=bingMapsCallback&maxResults=1&key=" + getMapsSubscriptionKey();

    var script = document.getElementById("bingMapsResult")
    if (script) script.parentElement.removeChild(script);

    // global variable holds reference to timer that will complete the search if the maps query fails
    timer = setTimeout(function() {
        timer = null;
        var form = document.forms.bing;
        bingEntitySearch(form.query.value, "", bingSearchOptions(form), getSearchSubscriptionKey());
    }, 5000);

    script = document.createElement("script");
    script.setAttribute("type", "text/javascript");
    script.setAttribute("id", "bingMapsResult");
    script.setAttribute("src", url);
    script.setAttribute("onerror", "BingMapsCallback(null)");
    document.body.appendChild(script);

    return false;
}
```

> [!NOTE]
> Om Bing Maps API inte svarar på `bingMapsCallBack()` -funktionen anropas aldrig. Normalt som skulle innebära att `bingEntitySearch()` inte anropas och sökresultaten enheten visas inte. Undvik det här scenariot `bingMapsLocate()` anger också en timer att anropa `bingEntitySearch()` efter fem sekunder. Det finns logik i Återanropsfunktionen att undvika sökningen entitet två gånger.

När frågan har slutförts på `bingMapsCallback()` funktionen anropas, enligt begäran.

```javascript
function bingMapsCallback(response) {

    if (timer) {    // we beat the timer; stop it from firing
        clearTimeout(timer);
        timer = null;
    } else {        // the timer beat us; don't do anything
        return; 
    }

    var location = "";
    var name = "";
    var radius = 1000;

    if (response) {
        try {
            if (response.statusCode === 401) {
                invalidateMapsKey();
            } else if (response.statusCode === 200) {
                var resource = response.resourceSets[0].resources[0];
                var coords   = resource.point.coordinates;
                name         = resource.name;

                // the radius is the largest of the distances between the location and the corners
                // of its bounding box (in case it's not in the center) with a minimum of 1 km
                try {
                    var bbox    = resource.bbox;
                    radius  = Math.max(haversineDistance(bbox[0], bbox[1], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[1]),
                                       haversineDistance(bbox[0], bbox[3], coords[0], coords[1]),
                                       haversineDistance(coords[0], coords[1], bbox[2], bbox[3]), 1000);
                } catch(e) {  }
                var location = "lat:" + coords[0] + ";long:" + coords[1] + ";re:" + Math.round(radius);
            }
        }
        catch (e) { }   // response is unexpected. this isn't fatal, so just don't provide location
    }

    var form = document.forms.bing;
    if (name) form.mapquery.value = name;
    bingEntitySearch(form.query.value, location, bingSearchOptions(form), getSearchSubscriptionKey());

}
```

Tillsammans med latitud och longitud Bing enheten sökfråga kräver en *radius* som anger precisionen för platsinformationen. Vi beräkna radius med hjälp av *angränsande ruta* i Bing Maps-svaret. Markeringsrutan är en rektangel som omger hela platsen. Till exempel om användaren anger `NYC`, innehåller ungefär centrala koordinater i New York City och en avgränsningsram som omfattar staden. 

Vi först beräkna avstånd från primära koordinaterna till var och en av de fyra hörn rutan med hjälp av funktionen `haversineDistance()` (visas inte). Vi använder den största av dessa fyra avstånd som radien. Minsta radius är en kilometer. Det här värdet används också som standard om det finns inga avgränsningsram i svaret.

Efter att ha beviljats koordinaterna och radien, vi sedan anropa `bingEntitySearch()` att utföra den faktiska sökningen.

## <a name="performing-the-search"></a>Sökningen

Baserat på frågan, en plats, en sträng med alternativ och API-nyckeln i `BingEntitySearch()` funktion gör Bing enheten sökbegäran.

```javascript
// perform a search given query, location, options string, and API keys
function bingEntitySearch(query, latlong, options, key) {

    // scroll to top of window
    window.scrollTo(0, 0);
    if (!query.trim().length) return false;     // empty query, do nothing

    showDiv("noresults", "Working. Please wait.");
    hideDivs("pole", "mainline", "sidebar", "_json", "_http", "error");

    var request = new XMLHttpRequest();
    var queryurl = SEARCH_ENDPOINT + "?q=" + encodeURIComponent(query) + "&" + options;

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

    if (latlong) request.setRequestHeader("X-Search-Location", latlong);

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
            if (jsobj._type === "SearchResponse") {
                renderSearchResults(jsobj);
            } else {
                renderErrorMessage("No search results in JSON response");
            }
        } else {
            renderErrorMessage("Empty response (are you sending too many requests too quickly?)");
        }
    if (divHidden("pole") && divHidden("mainline") && divHidden("sidebar")) 
        showDiv("noresults", "No results.<p><small>Looking for restaurants or other local businesses? Those currently areen't supported outside the US.</small>");
    }

    // Any other HTTP status is an error
    else {
        // 401 is unauthorized; force re-prompt for API key for next request
        if (this.status === 401) invalidateSearchKey();

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
> En lyckad HTTP-begäran har *inte* nödvändigtvis som själva lyckades sökningen. Om ett fel uppstår i search-åtgärd, returnerar en 200 HTTP - statuskod Bing enheten Sök-API och innehåller information om fel i JSON-svar. Dessutom returnerar begäran har begränsad hastighet, ett tomt svar på API: et.

Mycket av koden i båda av de här funktionerna är dedikerad till felhantering. Fel kan inträffa i följande steg:

|Fas|Potentiella fel|Hanteras av|
|-|-|-|
|Byggnaden JavaScript request-objektet|Ogiltig URL|`try`/`catch` block|
|Skickar begäran|Nätverksfel, avbrutna anslutningar|`error` och `abort` händelsehanterare|
|Sökningen|Ogiltig förfrågan, ogiltigt JSON hastighetsbegränsningar|testar i `load` händelsehanterare|

Fel hanteras genom att anropa `renderErrorMessage()` med alla detaljer kända om felet. Om svaret klarat fullständig gauntlet fel tester, som vi kallar `renderSearchResults()` att visa sökresultat på sidan.

## <a name="displaying-search-results"></a>Visa sökresultat

Sök-API Bing enheten [kräver att du vill visa resultat i en angiven ordning](use-display-requirements.md). Eftersom API: et kan returnera två olika typer av svar, det räcker inte att gå igenom den översta `Entities` eller `Places` samling i JSON-svar och visa resultat. (Om du vill bara en typ av resultat av använder den `responseFilter` Frågeparametern.)

I stället, använder vi den `rankingResponse` samling i sökresultaten kan sortera resultaten för visning. Det här objektet refererar till objekt i den `Entitiess` och/eller `Places` samlingar.

`rankingResponse` kan innehålla upp till tre samlingar av sökresultat avses `pole`, `mainline`, och `sidebar`. 

`pole`, om det finns är mest relevant sökresultatet och ska visas på en framträdande plats. `mainline` refererar till den största delen av sökresultatet. Likriktade resultaten ska visas omedelbart efter `pole` (eller först och om `pole` finns inte). 

Slutligen. `sidebar` refererar till extra sökresultat. De kan visas i en verklig sidopanelen eller bara efter likriktade resultaten. Vi har valt den senare för våra självstudier app.

Varje objekt i en `rankingResponse` samling refererar till de faktiska resultatet sökobjekt på två olika men motsvarande sätt.

| | |
|-|-|
|`id`|Den `id` ser ut som en URL, men ska inte användas för länkar. Den `id` typ av en rangordning resultat som matchar den `id` av antingen en sökning resultatobjekt i en samling i svaret, *eller* en samling för hela svaret (som `Entities`).
|`answerType`<br>`resultIndex`|Den `answerType` refererar till den översta svar-samling som innehåller resultatet (till exempel `Entities`). Den `resultIndex` refererar till det resultatet index i samlingen. Om `resultIndex` är utelämnas rangordning resultatet som refererar till hela samlingen.

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

    // for each possible section, render the resuts from that section
    for (section in {pole: 0, mainline: 0, sidebar: 0}) {
        if (results.rankingResponse[section])
            showDiv(section, renderResultsItems(section, results));
    }
}
```

## <a name="rendering-result-items"></a>Återgivning resultatet objekt

I vårt JavaScript-kod är ett objekt, `searchItemRenderers`, som innehåller *återgivning:* funktioner som genererar HTML-kod för varje typ av sökresultatet.

```javascript
searchItemRenderers = { 
    entities: function(item) { ... },
    places: function(item) { ... }
}
```

En renderare funktion kan acceptera följande parametrar:

| | |
|-|-|
|`item`|JavaScript-objekt som innehåller objektets egenskaper, till exempel dess URL och dess beskrivning.|
|`index`|Index över resultatobjekt i dess samling.|
|`count`|Antal objekt i samlingen Sök resultatobjekt.|

Den `index` och `count` parametrar kan användas för att antalet resultat för att generera särskilda HTML för början eller slutet av en samling om du vill infoga radbrytningar efter ett visst antal objekt, och så vidare. Om en renderare inte behöver den här funktionen, behöver det inte godkänna dessa två parametrar. Faktum är använder vi inte dem i återgivning för våra självstudier app.

Låt oss ta en närmare titt på den `entities` renderare:

```javascript
    entities: function(item) {
        var html = [];
        html.push("<p class='entity'>");
        if (item.image) {
            var img = item.image;
            if (img.hostPageUrl) html.push("<a href='" + img.hostPageUrl + "'>");
            html.push("<img src='" + img.thumbnailUrl +  "' title='" + img.name + "' height=" + img.height + " width= " + img.width + ">");
            if (img.hostPageUrl) html.push("</a>");
            if (img.provider) {
                var provider = img.provider[0];
                html.push("<small>Image from ");
                if (provider.url) html.push("<a href='" + provider.url + "'>");
                html.push(provider.name ? provider.name : getHost(provider.url));
                if (provider.url) html.push("</a>");
                html.push("</small>");
            }
        }
        html.push("<p>");
        if (item.entityPresentationInfo) {
            var pi = item.entityPresentationInfo;
            if (pi.entityTypeHints || pi.entityTypeDisplayHint) {
                html.push("<i>");
                if (pi.entityTypeDisplayHint) html.push(pi.entityTypeDisplayHint);
                else if (pi.entityTypeHints) html.push(pi.entityTypeHints.join("/"));
                html.push("</i> - ");
            }
        }
        html.push(item.description);
        if (item.webSearchUrl) html.push("&nbsp;<a href='" + item.webSearchUrl + "'>More</a>")
        if (item.contractualRules) {
            html.push("<p><small>");
            var rules = [];
            for (var i = 0; i < item.contractualRules.length; i++) {
                var rule = item.contractualRules[i];
                var link = [];
                if (rule.license) rule = rule.license;
                if (rule.url) link.push("<a href='" + rule.url + "'>");
                link.push(rule.name || rule.text || rule.targetPropertyName + " source");
                if (rule.url) link.push("</a>");
                rules.push(link.join(""));
            }
            html.push("License: " + rules.join(" - "));
            html.push("</small>");
        }
        return html.join("");
    }, // places renderer omitted
```

Vår entitet renderare funktionen:

> [!div class="checklist"]
> * Skapar HTML `<img>` tagg för att visa bildens miniatyr, om sådana finns. 
> * Skapar HTML `<a>` tagg som länkar till den sida som innehåller bilden.
> * Skapar beskrivning som visar information om bilden och den plats som den är aktiverad.
> * Inkluderar entitetens klassificering med Visa-tips om alla.
> * Innehåller en länk till en Bing-sökning för att få mer information om entiteten.
> * Visar alla licens- eller tillskrivningar information som krävs av datakällor.

## <a name="persisting-client-id"></a>Spara klient-ID

Svar från Bing search API: er kan innehålla en `X-MSEdge-ClientID` rubrik som ska skickas tillbaka till API: et med efterföljande förfrågningar. Om flera Bing Search API: er används måste samma klient-ID användas med dem om möjligt.

Att tillhandahålla den `X-MSEdge-ClientID` sidhuvudet tillåter Bing-API: er att koppla alla sökningar för en användare, som har två viktiga fördelar.

Först och hjälper främst Bing sökmotor att tillämpa tidigare kontexten sökningar att hitta resultat som bättre uppfyller användaren. Om en användare har har sökt efter villkor som rör avseglingen, till exempel kan företrädesvis senare söka efter ”dockningsstationer” kan returnera information om platser att docka en segelbåt.

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
> [Bing enheten Sök API-referens](//docs.microsoft.com/rest/api/cognitiveservices/bing-entities-api-v7-reference)

> [!div class="nextstepaction"]
> [Bing Maps API-dokumentation](//msdn.microsoft.com/library/dd877180.aspx)

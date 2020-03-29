---
title: Api-svarsstruktur och svarstyper för webbsökning
titleSuffix: Azure Cognitive Services
description: När du skickar en sökbegäran för Bing Web Search returneras ett `SearchResponse` objekt i svarstexten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 95ebfaef863a1fa05e8a5d3b46fca9659c61f6b7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74110611"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>Api-svarsstruktur och svarstyper för webbsökning  

När du skickar en sökbegäran för Bing Web Search returneras ett [`SearchResponse`](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objekt i svarstexten. Objektet innehåller ett fält för varje svar som Bing fastställt var relevant för frågan. Det här exemplet illustrerar ett svarsobjekt om Bing returnerade alla svar:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Vanligtvis returnerar Bing Web Search en delmängd av svaren. Om frågetermen till exempel *seglade jollar*kan `webPages` `images`svaret `rankingResponse`innehålla , och . Om du inte har använt [responseFilter](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) för att filtrera `webpages` bort `rankingResponse` webbsidor innehåller svaret alltid svaren och svaren.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Svar på webbsidor

[WebPages-svaret](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) innehåller en lista med länkar till webbsidor som Bing Web Search fastställt var relevanta för frågan. Varje [webbsida](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) i listan kommer att innehålla: sidans namn, url, visnings-URL, en kort beskrivning av innehållet och det datum Bing hittade innehållet.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Använd `name` `url` och skapa en hyperlänk som tar användaren till webbsidan.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Bilder svar

[Bilderna](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) svaret innehåller en lista över bilder som Bing trodde var relevanta för frågan. Varje [bild](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) i listan innehåller webbadressen till bilden, dess storlek, dess dimensioner och dess kodningsformat. Bildobjektet inkluderar även URL för en miniatyrbild av bilden och miniatyrbildens mått.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Beroende på användarens enhet visar du vanligtvis en delmängd av miniatyrerna, med ett alternativ för användaren att [bläddra igenom](paging-webpages.md) de återstående bilderna.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Du kan även expandera miniatyrbilden när användaren hovrar markören över den. Se till att tillskriva bildkällan om du expanderar bilden. Till exempel genom att extrahera `hostPageDisplayUrl` värden från och visa den under bilden. Information om att ändra storlek på miniatyrbilden finns i [Resizing and Cropping Thumbnails](./resize-and-crop-thumbnails.md) (Ändra storlek på och beskär miniatyrbilder).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Om användaren klickar på miniatyren använder du `webSearchUrl` för att ta användaren till Bings sökresultatsida för bilder, som innehåller ett collage av bilderna.

Mer information om bildsvaret och bilderna finns i [API för bildsökning](../bing-image-search/search-the-web.md).

## <a name="related-searches-answer"></a>Svar på relaterade sökningar

[RelatedSearches-svaret](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) innehåller en lista över de mest populära relaterade frågor som gjorts av andra användare. Varje [fråga](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) i listan innehåller`text`en frågesträng ( ),`displayText`en frågesträng`webSearchUrl`med träffmarkeringstecken ( ) och en URL ( ) till Bings sökresultatsida för den frågan.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Använd `displayText` frågesträngen `webSearchUrl` och URL:en för att skapa en hyperlänk som tar användaren till sökresultatsidan Bing för den relaterade frågan. Du kan också `text` använda frågesträngen i din egen API-fråga för webbsökning och visa resultaten själv.

Information om hur du hanterar markeringsmarkörerna i finns i `displayText` [Tryckmarkering](../bing-web-search/hit-highlighting.md).

Följande visar ett exempel på den relaterade frågor användning i Bing.com.

![Exempel på relaterade sökningar på Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Video svar

[Videosvaret](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) innehåller en lista med videor som Bing trodde var relevanta för frågan. Varje [video](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) i listan innehåller videons URL, dess varaktighet, dess dimensioner och kodningsformat. Videoobjektet innehåller även webbadressen till en miniatyrbild av videon och miniatyrbildens mått.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Beroende på användarens enhet visar du vanligtvis en delmängd av videorna med ett alternativ för användaren att visa de återstående videorna. Du skulle visa en miniatyrbild av videon med videons längd, beskrivning (namn) och attribution (utgivare).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

När användaren hovrar över miniatyren `motionThumbnailUrl` kan du använda för att spela upp en miniatyrversion av videon. Se till att du tillskriver ursprunget när du visar en video.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Om användaren klickar på miniatyrbilden finns följande visningsalternativ för videon:

- Används `hostPageUrl` för att visa videon på värdwebbplatsen (till exempel YouTube)
- Används `webSearchUrl` för att visa videon i Bing-videobläddraren
- Används `embedHtml` för att bädda in videon i din egen upplevelse

Mer information om videosvaret och videorna finns i [API för videosökning](../bing-video-search/search-the-web.md).

## <a name="news-answer"></a>Nyhetssvar

[Nyhetssvaret](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) innehåller en lista med nyhetsartiklar som Bing trodde var relevanta för frågan. Varje [nyhetsartikel](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) i listan innehåller artikels namn, beskrivning och webbadressen till artikeln på värdens webbplats. Om artikeln innehåller en bild innehåller objektet även en miniatyr för bilden.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Beroende på användarens enhet visar du en delmängd av nyhetsartiklarna med ett alternativ för användaren att visa de återstående artiklarna. Använd `name` och `url` till att skapa en hyperlänk som tar användaren till nyhetsartikeln på värdens webbplats. Om artikeln innehåller en bild gör du `url`bilden klickbar med . Se till att du tillskriver artikeln med `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Mer information om nyhetssvar och nyhetsartiklar finns i [API för nyhetssökning](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Svar på beräkning

Om användaren anger ett matematiskt uttryck eller en enhetskonverteringsfråga kan svaret innehålla ett [beräkningssvar.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) Svaret `computation` innehåller det normaliserade uttrycket och dess resultat.

En enhetskonverteringsfråga är en fråga som konverterar en enhet till en annan. Till exempel, *Hur många meter i 10 meter?* eller Hur många *matskedar i en 1/4 kopp?*

Följande visar `computation` svaret för *Hur många meter i 10 meter?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Följande visar exempel på matematiska `computation` frågor och deras motsvarande svar.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Ett matematiskt uttryck kan innehålla följande symboler:

|Symbol|Beskrivning|
|------------|-----------------|
|+|Addition|
|-|Subtraktion|
|/|Division|
|*|Multiplikation|
|^|Power|
|!|Fakultet|
|.|Decimal|
|()|Gruppering av prioritet|
|[]|Funktion|

Ett matematiskt uttryck kan innehålla följande konstanter:

|Symbol|Beskrivning|
|------------|-----------------|
|Pi|3.14159...|
|Grad|Grad|
|i|Imaginära tal|
|e|e, 2,71828...|
|GoldenRatio (0)|Guld- förhållande, 1.61803...|

Ett matematiskt uttryck kan innehålla följande funktioner:

|Symbol|Beskrivning|
|------------|-----------------|
|Sortera|Kvadratroten|
|Sin[x], Cos [x], Tan [x]<br />Csc[x], sek[x], cot[x]|Trigonometriska funktioner (med argument i radianer)|
|ArcSin[x], ArcCos[x], ArcTan[x]<br />ArcCsc[x], ArcSec[x], ArcCot[x]|Omvänd trigonometriska funktioner (ger resultat i radianer)|
|Exp[x], E^x|Exponentiell funktion|
|Logga[x]|Naturlig logaritm|
|Sinh[x], Cosh[x], Tanh[x]<br />Csch[x], Sech[x], Coth[x]|Hyperboliska funktioner|
|ArcSinh[x], ArcCosh[x], ArcTanh[x]<br />ArcCsch[x], ArcSech[x], ArcCoth[x]|Omvända hyperboliska funktioner|

Matematiska uttryck som innehåller variabler (till exempel 4x+6=18, där x är variabeln) stöds inte.

## <a name="timezone-answer"></a>TimeZone-svar

Om användaren anger en tids- eller datumfråga kan svaret innehålla ett [TimeZone-svar.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) Det här svaret stöder implicita eller explicita frågor. En implicit fråga, till exempel *Vilken tid är det?* En explicit fråga, till exempel *Vilken tid är det i Seattle?*, returnerar den lokala tiden för Seattle, WA.

Svaret `timeZone` innehåller namnet på platsen, aktuellt UTC-datum och tid på den angivna platsen och UTC-förskjutningen. Om gränsen för platsen ligger inom flera tidszoner innehåller svaret aktuellt UTC-datum och -tid för alla tidszoner inom gränsen. Eftersom Florida State till exempel ligger inom två tidszoner innehåller svaret det lokala datumet och tiden för båda tidszonerna.  

Om frågan begär tiden för ett land/en region bestämmer Bing den primära staden inom platsens `primaryCityTime` geografiska gräns och returnerar den i fältet . Om gränsen innehåller flera tidszoner returneras de återstående tidszonerna i fältet `otherCityTimes` .

Följande visar exempelfrågor som `timeZone` returnerar svaret.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion svar

Om Bing fastställer att användaren kan ha avsett att söka efter något annat, innehåller svaret ett [SpellSuggestions-objekt.](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) Om användaren till exempel söker efter *Carlos-pennan*kan Bing fastställa att användaren sannolikt är avsedd att söka efter Carlos Pena i stället (baserat på tidigare sökningar av andra av *Carlos Pen*). Följande visar ett exempel stava svar.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Svarshuvuden

Svar från API:et för webbsökning på Bing kan innehålla följande rubriker:

|||
|-|-|
|`X-MSEdge-ClientID`|Det unika ID som Bing har tilldelat användaren|
|`BingAPIs-Market`|Den marknad som användes för att uppfylla begäran|
|`BingAPIs-TraceId`|Loggposten på Bing API-servern för den här begäran (för support)|

Det är särskilt viktigt att behålla klient-ID:n och returnera det med efterföljande begäranden. När du gör detta kommer sökningen att använda tidigare kontext i rankningsresultaten och ger också en konsekvent användarupplevelse.

Men när du anropar API:et för webbsökning på Bing från JavaScript kan webbläsarens inbyggda säkerhetsfunktioner (CORS) hindra dig från att komma åt värdena för dessa rubriker.

För att få tillgång till rubrikerna kan du göra API-begäran om Bing-webbsökning via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers`-huvud som vitlistar svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en CORS-proxy så att vår [handledningsapp](tutorial-bing-web-search-single-page-app.md) kan komma åt de valfria klienthuvudena. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

    npm install -g cors-proxy-server

Ändra sedan slutpunkten för API-koden för webbsökning på Bing web sökning i HTML-filen till:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search

Slutligen startar du CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="response-headers-in-production"></a>Svarshuvuden i produktion

Cors proxy-metoden som beskrivs i föregående svar är lämplig för utveckling, testning och inlärning.

I en produktionsmiljö bör du vara värd för ett serverskript på samma domän som webbsidan som använder API:et för webbsökning på Bing. Det här skriptet bör ringa API-anrop på begäran från webbsidan JavaScript och skicka alla resultat, inklusive rubriker, tillbaka till klienten. Eftersom de två resurserna (sidan och skriptet) delar ett ursprung används inte CORS och specialrubrikerna är tillgängliga för JavaScript på webbsidan.

Den här metoden skyddar också din API-nyckel från exponering för allmänheten, eftersom endast serverskriptet behöver det. Skriptet kan använda en annan metod för att kontrollera att begäran är auktoriserad.

Följande visar hur Bing använder stavningsförslaget.

![Exempel på Bing-stavningsförslag](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Nästa steg  

* Granska dokumentation [för begränsning av begäranden.](throttling-requests.md)  

## <a name="see-also"></a>Se även  

* [API-referens för webbsökning](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

---
title: Skicka begäranden till API:et för automatiska förslag på Bing
titleSuffix: Azure Cognitive Services
description: API för automatiska förslag för Bing returnerar en lista med föreslagna frågor som baseras på den partiella frågesträngen i sökrutan. Läs mer om att skicka förfrågningar.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: scottwhi
ms.openlocfilehash: d479548e682e814345e13d9416d08ec453f90304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74072851"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Skicka begäranden till API:et för automatiska förslag på Bing.

Om programmet skickar frågor till något av API:erna för Bing-sökning, kan du använda API för automatiska förslag för Bing till att förbättra användarnas sökupplevelse. API för automatiska förslag för Bing returnerar en lista med föreslagna frågor som baseras på den partiella frågesträngen i sökrutan. När tecken skrivs in i en sökruta i programmet kan du visa förslag i en listruta. Använd den här artikeln om du vill veta mer om hur du skickar begäranden till det här API:et. 

## <a name="bing-autosuggest-api-endpoint"></a>Bing-slutpunkt för api-slutpunkt för automatiskt förslag

**Api:et för automatisk uggsuggest för Bing** innehåller en slutpunkt som returnerar en lista med föreslagna frågor från en partiell sökterm.

Om du vill få förslag på frågor `GET` med Bing-API:et skickar du en begäran till följande slutpunkt. Använd rubrikerna och URL-parametrarna för att definiera ytterligare specifikationer.

**Slutpunkt:** Returnerar sökförslag som JSON-resultat som är relevanta `?q=""`för användarens indata som definieras av .

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Mer information om rubriker, parametrar, marknadskoder, svarsobjekt, fel osv., se [Bing Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference) v7-referensen.

Bing-API:erna stöder sökåtgärder som returnerar resultat efter typ. **Bing**Alla sökslutpunkter returnerar resultat som JSON-svarsobjekt.
Alla slutpunkter stöder frågor som returnerar ett visst språk och/eller en viss plats med longitud, latitud och sökradie.

Fullständig information om de parametrar som stöds av varje slutpunkt finns i referenssidorna för varje typ.
Exempel på grundläggande begäranden med autosuggest-API finns i [Snabbstartar för Autosuggest](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Api-begäranden för Bing-korts förslag

> [!NOTE]
> * Begäranden till API:et för automatiska förslag på Bing måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Distribuera nyckeln som en del av ett klientprogram ger mer möjlighet skadlig åtkomst från tredje part. Att ringa samtal från en server ger dessutom en enda uppgraderingspunkt för framtida uppdateringar.

Begäran måste ange frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), som innehåller användarens partiella sökterm. Även om det är valfritt bör begäran även innehålla frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), som identifierar marknaden som du vill att resultatet ska komma från. En lista över valfria frågeparametrar finns i [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Alla frågeparametervärden måste vara URL-kodade.

Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Sidhuvuden för klientens IP-adress och platsen är viktiga för att returnera platsmedvetet innehåll.

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> När du anropar API:et för automatiska förslag på Bing från JavaScript kan webbläsarens inbyggda säkerhetsfunktioner hindra dig från att komma åt värdena för dessa rubriker.

Lös detta kan du göra API-begäran om automatiska förslag på Bing-åtkomst via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers`-huvud som vitlistar svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en CORS-proxy så att vår [handledningsapp](../tutorials/autosuggest.md) kan komma åt de valfria klienthuvudena. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

    npm install -g cors-proxy-server

Ändra sedan API-slutpunkten för automatiska förslag på Bing i HTML-filen till:

    http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/Suggestions

Slutligen startar du CORS-proxyn med följande kommando:

    cors-proxy-server

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

Begäranden bör innehålla alla föreslagna frågeparametrar och rubriker. 

I följande exempel visas en begäran som returnerar de föreslagna frågesträngarna för *sail* (segla).

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klientens ID-huvud om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

Följande webbförslagsgrupp är ett svar på ovanstående begäran. Gruppen innehåller en lista med sökfrågeförslag, `displayText`med `query`varje `url` förslag, inklusive ett , och fält.

Fältet `displayText` innehåller den föreslagna frågan som du använder för att fylla i sökrutans listruta. Du måste visa alla förslag som svaret innehåller, och i den angivna ordningen.  

Om användaren väljer en fråga i listrutan kan du använda den för att anropa [api:erna för Bing-sökning](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) och visa resultaten själv, `url` eller skicka användaren till resultatsidan Bing med det returnerade fältet.

[!INCLUDE [cognitive-services-bing-url-note](../../../../includes/cognitive-services-bing-url-note.md)]

```json
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{
    "_type" : "Suggestions",
    "queryContext" : {
        "originalQuery" : "sail"
    },
    "suggestionGroups" : [{
        "name" : "Web",
        "searchSuggestions" : [{
            "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
            "displayText" : "sailing lessons seattle",
            "query" : "sailing lessons seattle",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+moon+news&FORM=USBAPI",
            "displayText" : "sailor moon news",
            "query" : "sailor moon news",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailor+jack%27s+lincoln+city&FORM=USBAPI",
            "displayText" : "sailor jack's lincoln city",
            "query" : "sailor jack's lincoln city",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailing+anarchy&FORM=USBAPI",
            "displayText" : "sailing anarchy",
            "query" : "sailing anarchy",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale&FORM=USBAPI",
            "displayText" : "sailboats for sale",
            "query" : "sailboats for sale",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailstn.mylabsplus.com&FORM=USBAPI",
            "displayText" : "sailstn.mylabsplus.com",
            "query" : "sailstn.mylabsplus.com",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailusfood&FORM=USBAPI",
            "displayText" : "sailusfood",
            "query" : "sailusfood",
            "searchKind" : "WebSearch"
        },
        {
            "url" : "https:\/\/www.bing.com\/search?q=sailboats+for+sale+seattle&FORM=USBAPI",
            "displayText" : "sailboats for sale seattle",
            "query" : "sailboats for sale seattle",
            "searchKind" : "WebSearch"
        }]
    }]
}
```

## <a name="next-steps"></a>Nästa steg

- [Vad är Automatiska förslag i Bing?](../get-suggested-search-terms.md)
- [Referens för API v7 för automatiska förslag i Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
- [Hämta föreslagna söktermer från API:et för automatiska förslag på Bing](get-suggestions.md)

---
title: Skicka förfrågningar till automatiska förslag i Bing
titlesuffix: Azure Cognitive Services
description: Lär dig mer om att skicka begäranden till automatiska förslag i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: scottwhi
ms.openlocfilehash: 597ef48fd7499a9d33b214b182d6dd1354756cdf
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57011588"
---
# <a name="sending-requests-to-the-bing-autosuggest-api"></a>Skicka förfrågningar till automatiska förslag i Bing.

Om programmet skickar frågor till något av API:erna för Bing-sökning, kan du använda API för automatiska förslag för Bing till att förbättra användarnas sökupplevelse. API för automatiska förslag för Bing returnerar en lista med föreslagna frågor som baseras på den partiella frågesträngen i sökrutan. Som tecken anges i en sökruta i ditt program, kan du visa förslag i en nedrullningsbar listruta. Använd den här artikeln om du vill veta mer om att skicka förfrågningar till detta API.

## <a name="bing-autosuggest-api-endpoint"></a>Automatiska förslag i Bing API-slutpunkt

Den **automatiska förslag i Bing** innehåller en slutpunkt som returnerar en lista över föreslagna frågor från en partiell sökterm.

För att få föreslagna frågor med hjälp av Bing-API kan skicka en `GET` begäran till följande slutpunkt. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

**Slutpunkt:** Returnerar sökförslag som JSON-resultat som är relevanta för användarens indata som definierats av `?q=""`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/Suggestions 
```

Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [automatiska förslag i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference) referens.

Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.
Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med hjälp av API för automatiska förslag i finns [automatiska förslag i Snabbstarter](https://docs.microsoft.com/azure/cognitive-services/Bing-Autosuggest).

## <a name="bing-autosuggest-api-requests"></a>Begäranden för automatiska förslag i Bing

> [!NOTE]
> Begäranden till API för automatiska förslag i Bing måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Distribuera nyckeln som en del av ett klientprogram ger mer möjlighet skadliga från tredje part-åtkomst. Dessutom tillhandahåller samtal från en server en enkel uppgradering för framtida uppdateringar.

Begäran måste ange frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query), som innehåller användarens partiella sökterm. Även om det är valfritt bör begäran även innehålla frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt), som identifierar marknaden som du vill att resultatet ska komma från. En lista över valfria frågeparametrar finns i [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Alla frågeparametervärden måste vara URL-kodade.

Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:

- [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Sidhuvuden för klientens IP-adress och platsen är viktiga för att returnera platsmedvetet innehåll.

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

> [!NOTE]
> När du anropar den automatiska förslag i Bing från JavaScript hindrar i webbläsaren inbyggda säkerhetsfunktioner dig från att komma åt värdena för dessa rubriker.

Du kan göra automatiska förslag i Bing-begäran via en CORS-proxy för att lösa problemet. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers`-huvud som vitlistar svarshuvuden och gör dem tillgängliga för JavaScript.

Det är enkelt att installera en proxy för CORS så att våra [självstudieappen](../tutorials/autosuggest.md) att komma åt valfria klientcertifikat-huvuden. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

    npm install -g cors-proxy-server

Sedan ändra den automatiska förslag i Bing-slutpunkten i HTML-filen:

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

Följande visar svaret på den föregående begäran. Svaret innehåller en webbförslagsgrupp som innehåller en lista över sökfrågeförslag. Varje förslag innehåller fälten `displayText`, `query` och `url`.

Fältet `displayText` innehåller den föreslagna frågan som du använder för att fylla i sökrutans listruta. Du måste visa alla förslag som svaret innehåller, och i den angivna ordningen.  

Om användaren väljer en fråga från den nedrullningsbara listan, kan du använda den för att anropa en av de [API: er för Bing-sökresultat](https://docs.microsoft.com/azure/cognitive-services/bing-web-search/bing-api-comparison?toc=%2Fen-us%2Fazure%2Fcognitive-services%2Fbing-autosuggest%2Ftoc.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json) och visa resultaten själv, eller skicka användaren till Bing resultatsidan med hjälp av den returnerade `url` fält. I följande exempel används Bing Web Search API.

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

- [Vad är automatiska förslag i Bing?](../get-suggested-search-terms.md)
- [Referens för API v7 för automatiska förslag i Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference)
- [Hämta föreslagna söktermer från automatiska förslag i Bing](get-suggestions.md)

---
title: 'Snabbstart: API för stavningskontroll i Bing'
titlesuffix: Azure Cognitive Services
description: Visar hur du kommer igång med API för stavningskontroll i Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: quickstart
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: 4af90c0e1ffd6dbadcb31eb6141bc24f746d01f9
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316420"
---
# <a name="quickstart-your-first-spell-check-request"></a>Snabbstart: Din första stavningskontrollsförfrågan

Hämta en [Cognitive Services-åtkomstnyckel](https://azure.microsoft.com/try/cognitive-services/) under **Sök**.  Se även [Priser för Cognitive Services – API för Bing-sökning](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

Om du vill kontrollera stavning och grammatikfel i en textsträng skickar du en GET-begäran till följande slutpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> Slutpunkt för V7-förhandsversion:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla förfrågningar kommer från en server. Om nyckeln distribueras som en del av ett klientprogram ökar risken för att en illvillig tredje part kan komma åt den. Anrop från en server innebär dessutom att det bara finns en enda uppgraderingspunkt för framtida versioner av API:et.

Begäran måste innehålla frågeparametern [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text), som innehåller textsträngens som ska kontrolleras. Även om det är valfritt bör begäran även innehålla frågeparametern [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt), som identifierar marknaden som du vill att resultatet ska komma från. En lista över valfria frågeparametrar som `mode` finns i [Frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Alla frågeparametervärden måste vara URL-kodade.  
  
Begäran måste innehålla huvudet [Ocp-Apim-Subscription-Key](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey). Även om det är valfritt rekommenderar vi även att följande huvuden finns med:  
  
-   [User-Agent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Search-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X-Search-Location](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

En lista över alla sidhuvuden för begäranden och svar finns i [Sidhuvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en begäran som innehåller alla föreslagna frågeparametrar och sidhuvuden. Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen. 
  
```  
GET https://api.cognitive.microsoft.com/bing/v5.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-Search-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```  

> [!NOTE]
> Begäran om V7-förhandsversion:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Nedan visas svaret på den tidigare begäran. Exemplet visar också Bing-specifika svarshuvuden.

```
BingAPIs-TraceId: 76DD2C2549B94F9FB55B4BD6FEB6AC
X-MSEdge-ClientID: 1C3352B306E669780D58D607B96869
BingAPIs-Market: en-US

{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  


## <a name="next-steps"></a>Nästa steg

Testa API:et. Gå till [testningskonsolen för stavningskontrolls-API](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

Mer information om hur du använder svarsobjekten finns i [Spell check text strings](./proof-text.md) (Stavningskolla textsträngar).


---
title: Snabbstart för Bing stavningskontroll kontrollera API | Microsoft Docs
description: Visar hur du kommer igång med Bing stavningskontroll kontrollera API.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: AF8EB1F0-386D-4555-9354-735611435F04
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: article
ms.date: 06/21/2016
ms.author: scottwhi
ms.openlocfilehash: cae8353e5be6e70eca90e5995b29b6774fc7d6a9
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351501"
---
# <a name="your-first-spell-check-request"></a>Din första stavningskontroll begäran

Du måste hämta en nyckel för prenumerationen kognitiva Services innan du kan göra din första anropet. Om du vill få en nyckel finns [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api).

Om du vill kontrollera en textsträng för stavningen och grammatikfel skickar du en GET-begäran till följande slutpunkt:  
  
```
https://api.cognitive.microsoft.com/bing/v5.0/spellcheck
```

> [!NOTE]
> V7 förhandsgranskningsslutpunkten:
> 
> ```
> https://api.cognitive.microsoft.com/bing/v7.0/spellcheck
> ```  
  
Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla begäranden som kommer från en server. Distribuera nyckeln som en del av ett klientprogram ger mer möjlighet för skadliga tredjeparts att komma åt den. Dessutom ger samtal från en server en enkel uppgradering för framtida versioner av API: et.

Begäran måste ange den [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#text) frågeparameter som innehåller en textsträng att bevis. Även om det är valfritt, begäran måste också ange den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#mkt) frågeparameter som identifierar marknaden där du vill att resultaten från. En lista över valfria fråga parametrar som `mode`, se [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#query-parameters). Alla parametervärden för frågan måste vara URL-kodade.  
  
Begäran måste ange den [Ocp-Apim-prenumeration-nyckeln](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#subscriptionkey) huvud. Även om det är valfritt, uppmanas du att ange följande huvuden:  
  
-   [Användaragent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#useragent)  
-   [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientid)  
-   [X-Sök-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#clientip)  
-   [X sökplats](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#location)  

En lista över alla begärande- och svarshuvuden, finns i [huvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v5-reference#headers).

## <a name="the-request"></a>Begäran

Nedan visas en förfrågan som innehåller alla föreslagna frågeparametrar och rubriker. Om det är första gången du anropar någon av API: er för Bing inte med klient-ID-huvudet. Ta bara med klient-ID om du har tidigare påbörjat en Bing-API och Bing returnerade ett klient-ID för användare och enhet kombination. 
  
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
> V7 Preview begäran:

> ```  
> GET https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?text=when+its+your+turn+turn,+john,+come+runing&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE  
> X-MSEdge-ClientIP: 999.999.999.999  
> X-Search-Location: lat:47.60357;long:-122.3295;re:100  
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
> Host: api.cognitive.microsoft.com  
> ```  

Nedan visas svaret på den tidigare begäranden. Exemplet visar också Bing-specifika svarshuvuden.

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

Prova att använda API: et. Gå till [Stavningskontrolls Kontrollera API Testing-konsolen](https://dev.cognitive.microsoft.com/docs/services/56e73033cf5ff80c2008c679/operations/57855119bca1df1c647bc358). 

Mer information om förbrukar svar-objekt finns [stavningskontroll kontrollera textsträngar](./proof-text.md).


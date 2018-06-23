---
title: Autosuggest API quickstart | Microsoft Docs
description: Visar hur du kommer igång med Bing Autosuggest API.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1482E781-7352-4A3F-B1D5-B896381348C4
ms.service: cognitive-services
ms.component: bing-autosuggest
ms.topic: article
ms.date: 04/15/2017
ms.author: scottwhi
ms.openlocfilehash: a7b54a1fb0b7c76eb72097357a6b51aa02e6e2fd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354579"
---
# <a name="making-your-first-autosuggest-query"></a>Att göra sökningen första automatiska förslag

Du måste hämta en nyckel för prenumerationen kognitiva Services innan du kan göra din första anropet. Om du vill få en nyckel finns [försök kognitiva Services](https://azure.microsoft.com/try/cognitive-services/?api=autosuggest-api).

För att få sökresultaten kan skickar du en GET-begäran till följande slutpunkt:

```http
https://api.cognitive.microsoft.com/bing/v5.0/Suggestions
```

> [!NOTE]
> V7 förhandsgranskningsslutpunkten:
>
> ```http
> https://api.cognitive.microsoft.com/bing/v7.0/Suggestions
> ```

Begäran måste använda HTTPS-protokollet.

Vi rekommenderar att alla begäranden som kommer från en server. Distribuera nyckeln som en del av ett klientprogram ger mer möjlighet för skadliga tredjeparts att komma åt den. Dessutom ger samtal från en server en enkel uppgradering för framtida versioner av API: et.

Begäran måste ange den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query) frågeparameter som innehåller användarens partiella sökord. Men det är valfritt, begäran måste också ange den [mkt](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#mkt) frågeparameter som identifierar marknaden där du vill att resultaten från. En lista med valfria parametrar finns [frågeparametrar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#query-parameters). Alla parametervärden för frågan måste vara URL-kodade.

Begäran måste ange den [Ocp-Apim-prenumeration-nyckeln](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#subscriptionkey) huvud. Även om det är valfritt, uppmanas du att ange följande huvuden:

- [Användaragent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#useragent)
- [X-MSEdge-ClientID](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientid)
- [X-Sök-ClientIP](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#clientip)
- [X sökplats](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#location)

Klientens IP- och plats huvuden är viktiga för att returnera var medveten om innehållet.

En lista över alla begärande- och svarshuvuden, finns i [huvuden](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v5-reference#headers).

## <a name="the-request"></a>Begäran

Begäran bör innehålla alla föreslagna frågeparametrar och rubriker. Du kan anropa denna API varje gång användaren anger ett nytt tecken i sökrutan. Returnerar av frågan sträng påverkan för föreslagna frågan termer som API: et. Ju fler slutföra frågesträngen mer relevant att listan över föreslagna sökord är. Till exempel förslagen API: et kan returnera för *s* sannolikhet att vara mindre relevanta än de frågor som returnerar för *avseglingen dinghies*. 

I följande exempel visas en begäran som returnerar de föreslagna frågesträngarna för *seglar*.

```http
GET https://api.cognitive.microsoft.com/bing/v5.0/suggestions?q=sail&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

> [!NOTE]
> V7 Preview begäran:

> ```http
> GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
> Ocp-Apim-Subscription-Key: 123456789ABCDE
> X-MSEdge-ClientIP: 999.999.999.999
> X-Search-Location: lat:47.60357;long:-122.3295;re:100
> X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
> Host: api.cognitive.microsoft.com
> ```

Om det är första gången du anropar någon av API: er för Bing inte med klient-ID-huvudet. Ta bara med rubriken klient-ID om du har tidigare påbörjat en Bing-API och Bing returnerade ett klient-ID för användare och enhet kombination.

Nedan visas svaret på den föregående begäranden. Svaret innehåller en web förslag grupp som innehåller en lista över sökförslag för frågan. Varje förslag innehåller en `displayText`, `query`, och `url` fält.

Den `displayText` fältet innehåller föreslagna frågan som du vill använda för att fylla i sökrutan i listrutan. Du måste visa alla förslag som svaret innehåller och i angiven ordning.  

Om användaren väljer en fråga från den nedrullningsbara listrutan, kan du använda frågesträngen i den `query` fält att anropa den [Bing Search API](../bing-web-search/search-the-web.md) och visa resultatet själv. Eller så kan du använda URL-Adressen i det `url` fältet att skicka användaren till Bing-sökningen resultatsida.

```  
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

Prova att använda API: et. Gå till [Autosuggest API testning konsolen](https://dev.cognitive.microsoft.com/docs/services/56c7694ecf5ff801a090fbd1/operations/56c769a2cf5ff801a090fbd2).

Mer information om förbrukar svar-objekt finns [komma förslag söktermer](./get-suggested-search-terms.md).

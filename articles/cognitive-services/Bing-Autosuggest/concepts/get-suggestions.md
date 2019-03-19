---
title: Föreslå söktermer med API:et för automatiska förslag i Bing
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder API för automatiska förslag i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 293dcaadfc20116455983b3fc0069f9e9df3f843
ms.sourcegitcommit: 15e9613e9e32288e174241efdb365fa0b12ec2ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "57010556"
---
# <a name="suggesting-query-terms"></a>Föreslå frågeord

Vanligtvis anropar du API:et för automatiska förslag i Bing varje gång en användare matar in ett nytt tecken i programmets sökruta. Frågesträngens fullständighet påverkar relevansen för de föreslagna frågetermer som API:et returnerar. Ju mer fullständig frågesträngen är, desto mer relevant blir listan över föreslagna frågetermer. Till exempel är de förslag som API:et kan returnera för `s` förmodligen mindre relevanta än de frågor det returnerar för `sailing dinghies`.

## <a name="example-request"></a>Exempelbegäran

I följande exempel visas en begäran som returnerar de föreslagna frågesträngarna för *sail* (segla). Kom ihåg att URL-koda användarens partiella frågeterm när du anger frågeparametern [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#query). Om användaren till exempel anger *sailing les* ställer du in `q` till `sailing+les` eller `sailing%20les`.

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/suggestions?q=sail&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Följande svar innehåller en lista över [SearchAction](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference#searchaction)-objekt som innehåller de föreslagna frågetermerna.

```json
{
    "url" : "https:\/\/www.bing.com\/search?q=sailing+lessons+seattle&FORM=USBAPI",
    "displayText" : "sailing lessons seattle",
    "query" : "sailing lessons seattle",
    "searchKind" : "WebSearch"
}, ...
```

## <a name="using-suggested-query-terms"></a>Använda föreslagna söktermer

Varje förslag innehåller fälten `displayText`, `query` och `url`. Fältet `displayText` innehåller den föreslagna fråga som du använder för att fylla i sökrutans listruta. Du måste visa alla förslag som svaret innehåller, och i den angivna ordningen.

I följande exempel visas en sökruta för listrutan med föreslagna söktermer från API:et för automatiska förslag i Bing.

![Lista med automatiska förslag i sökrutans listruta](../media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Om användaren väljer en föreslagen fråga från listrutan använder du frågetermen i fältet `query` för att anropa [API för webbsökning i Bing](../../bing-web-search/search-the-web.md) och visa resultaten själv. Alternativt kan du använda URL:en i fältet `url` för att dirigera användaren till sidan för Bing-sökresultat i stället.

## <a name="next-steps"></a>Nästa steg

* [Vad är API:et för automatiska förslag i Bing?](../get-suggested-search-terms.md)
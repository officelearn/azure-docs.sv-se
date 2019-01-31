---
title: Vad är Automatiska förslag i Bing?
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder API för automatiska förslag i Bing.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: overview
ms.date: 09/12/2017
ms.author: scottwhi
ms.openlocfilehash: e1e1ec2a9f5329f5d7331b7ce3ced4924d001a49
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55174184"
---
# <a name="what-is-bing-autosuggest"></a>Vad är Automatiska förslag i Bing?

Om du skickar frågor till något av API:erna för Bing-sökning kan du använda API för automatiska förslag i Bing för att förbättra din upplevelse för sökrutan. API för automatiska förslag i Bing returnerar en lista över föreslagna frågor baserat på den partiella frågesträng som användaren anger i sökrutan. Visa förslagen i sökrutans listruta. De föreslagna villkoren baseras på föreslagna frågor som andra användare har sökt på samt användarens avsikt.

Normal anropar du detta API varje gång användaren skriver ett nytt tecken i sökrutan. Frågesträngens fullständighet påverkar relevansen för de föreslagna frågetermer som API:et returnerar. Ju mer fullständig frågesträngen är, desto mer relevant blir listan över föreslagna frågetermer. Till exempel är de förslag som API:et kan returnera för *s* förmodligen mindre relevanta än de frågor det returnerar för *sailing dinghies* (segla jollar).

## <a name="getting-suggested-search-terms"></a>Hämta föreslagna söktermer

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

Varje förslag innehåller fälten `displayText`, `query` och `url`. Fältet `displayText` innehåller den föreslagna fråga som du använder för att fylla i sökrutans listruta. Du måste visa alla förslag som svaret innehåller, och i den angivna ordningen.

Nedan visas ett exempel på sökrutans listruta med föreslagna frågetermer.

![Lista med automatiska förslag i sökrutans listruta](./media/cognitive-services-bing-autosuggest-api/bing-autosuggest-drop-down-list.PNG)

Om användaren väljer en föreslagen fråga från listrutan använder du frågetermen i fältet `query` för att anropa [API för webbsökning i Bing](../bing-web-search/search-the-web.md) och visa resultaten själv. Alternativt kan du använda URL:en i fältet `url` för att dirigera användaren till sidan för Bing-sökresultat i stället.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt med din första begäran genom att läsa avsnittet om att [Skapa din första fråga](quickstarts/csharp.md).

Bekanta dig med referensen för [API för automatiska förslag i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-autosuggest-api-v7-reference). Referensen innehåller en lista över de slutpunkter, rubriker och frågeparametrar som du använder för att begära föreslagna frågetermer samt definitionerna av svarsobjekten.

Lär dig hur du söker på webben med hjälp av [API för webbsökning i Bing](../bing-web-search/search-the-web.md).

Se till att läsa [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.

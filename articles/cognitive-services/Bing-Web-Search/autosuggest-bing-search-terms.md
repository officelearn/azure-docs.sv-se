---
title: Automatiska förslag i Bing söktermer
titleSuffix: Microsoft Cognitive Services
description: Koppla Bing Web Search API med automatiska förslag i Bing att ge användare en förbättrad sökupplevelse.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-web-search
ms.topic: article
ms.date: 8/13/2018
ms.author: erhopf
ms.openlocfilehash: df8a57b3136abfacce971f4d01ccb2296dfa784c
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42889464"
---
# <a name="autosuggest-bing-search-terms"></a>Automatiska förslag i Bing söktermer

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren anger en sökterm, det måste vara URL-kodad innan den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) frågeparameter har angetts. Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

Om frågetermen innehåller en felstavning, Sök svaret innehåller en [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) objekt. Objektet visas den ursprungliga stavningen och korrigerade stavningen som Bing används för sökningen.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Du kan använda den här informationen till att de vet att du ändrat deras frågesträngen när du visar sökresultatet.

![Exempel på sökfråga kontext UX](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Nästa steg  

* Granska exempel [Bing Web Search API-svar](search-responses.md).  

## <a name="see-also"></a>Se också  

* [Bing Web Search API-referens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference)

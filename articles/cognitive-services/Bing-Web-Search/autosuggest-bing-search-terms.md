---
title: Automatiska förslag i söktermer - API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Koppla Bing Web Search API med automatiska förslag i Bing att ge användare en förbättrad sökupplevelse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: bf15ed704de305353d1ec141df6deb9d29b47f75
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2019
ms.locfileid: "57339342"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Automatiska förslag i Bing söktermer i ditt program

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren anger en sökterm, det måste vara URL-kodad innan den [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#query) frågeparameter har angetts. Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

Om frågetermen innehåller en felstavning, Sök svaret innehåller en [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference#querycontext) objekt. Objektet visar den ursprungliga stavningen och den korrigerade stavningen som Bing använde för sökningen.

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

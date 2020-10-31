---
title: Automatiska förslags Sök termer – API för webbsökning i Bing
titleSuffix: Azure Cognitive Services
description: Para ihop API för webbsökning i Bing med API för automatiska förslag i Bing för att ge användarna en förbättrad Sök upplevelse.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/03/2019
ms.author: aahi
ms.openlocfilehash: 15a2d3b433236d74f3fa3d09e38071bacae02508
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078840"
---
# <a name="autosuggest-bing-search-terms-in-your-application"></a>Autoföreslå Bing-sökord i ditt program

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett en sökterm måste den vara URL-kodad innan [q](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query) -Frågeparametern anges. Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

Om frågetermen innehåller en stavnings fel innehåller Sök svaret ett [QueryContext](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#querycontext) -objekt. Objektet visar den ursprungliga stavningen och den korrigerade stavningen som Bing använde för sökningen.

```json
"queryContext": {
    "originalQuery": "sialing dingy for sale",
    "alteredQuery": "sailing dinghy for sale",
    "alterationOverrideQuery": "+sialing +dingy for sale"
}
```

Du kan använda den här informationen för att låta användaren veta att du har ändrat deras frågesträng när du visar Sök resultaten.

![GRÄNSSNITTs exempel för frågeuttryck](./media/cognitive-services-bing-web-api/bing-query-context.PNG)  

## <a name="next-steps"></a>Nästa steg  

* Granska exempel [API för webbsökning i Bing svar](search-responses.md).  

## <a name="see-also"></a>Se även  

* [API för webbsökning i Bing referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)

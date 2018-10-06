---
title: Slutpunkt för Anpassad sökning i Bing
titlesuffix: Azure Cognitive Services
description: Sammanfattning av Bing Custom Search API-slutpunkten.
services: cognitive-services
author: mikedodaro
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: v-gedod
ms.openlocfilehash: da448cdeaf6fcbe10cba8e5e2613214f8e0cee18
ms.sourcegitcommit: 6f59cdc679924e7bfa53c25f820d33be242cea28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2018
ms.locfileid: "48815196"
---
# <a name="custom-search"></a>Anpassad sökning
Bing Custom Search kan du skapa skräddarsydda sökmöjligheter för ämnen som intresserar dig. Användarna ser sökresultat som skräddarsys för det innehåll som de bryr dig om slipper att bläddra igenom sökresultat som har inte relevant innehåll.

## <a name="custom-search-endpoint"></a>Anpassad sökning
För att få resultat med hjälp av API: et för Bing Custom Search kan skicka en `GET` begäran till följande slutpunkt. Använda rubriker och URL-parametrar för att definiera ytterligare specifikationer.

Slutpunkt: Returnerar sökförslag som JSON-resultat som är relevanta för användarens indata som definierats av `?q=""`.
```  
 GET https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search  
```

Exempel som beskriver hur du ställer in anpassad sökning i källor, finns det [självstudien](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page). Mer information om huvuden, parametrar, marknaden koder, svarsobjekt, fel, o.s.v., se den [anpassad sökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference) referens.

## <a name="custom-search-response-json"></a>Anpassad sökning i svaret JSON
En anpassad sökning i begäran returnerar resultat som JSON-objekt, se [svarsobjekten](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference#response-objects). 

## <a name="custom-autosuggest"></a>Anpassade automatiska förslag
Anpassade automatiska förslag i API: et kan du skicka en fråga för partiella sökterm till Bing och få tillbaka en lista över föreslagna frågor som du kan konfigurera. Med anpassade automatiska förslag att lägga till förslag som returneras av API: et och du kan också ange om du vill inkludera förslag som genererats av Bing.

## <a name="custom-autosuggest-endpoint"></a>Anpassade automatiska förslag slutpunkt
Skicka en GET-begäran för att begära anpassade frågeförslag:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/Suggestions
```  

Information om hur du definierar anpassade förslag finns i [definiera anpassade sökförslag](define-custom-suggestions.md).

## <a name="custom-image-search"></a>Anpassade bildsökning
Sök-API för anpassad bild kan du skicka en sökfråga till Bing och hämta en lista över relevanta avbildningar från din anpassade Sökinstans.

## <a name="custom-image-search-endpoint"></a>Anpassad avbildning Search slutpunkt
Om du vill begära avbildningar från din anpassade Sökinstans, skicka en GET-begäran till följande URL:

```
https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/images/search
```

Information om hur du konfigurerar en anpassad Sökinstans finns i [konfigurera din anpassade sökupplevelse](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/define-your-custom-view).

## <a name="next-steps"></a>Nästa steg
Den **Bing** API: er stöder sökåtgärder som returnerar resultat enligt typ. Alla Sök slutpunkter returnerar resultat som JSON-svar-objekt.  Alla slutpunkter stöd för frågor som returnerar ett visst språk och/eller plats genom att longitud, latitud och Sök radius.

Fullständig information om de parametrar som stöds av varje slutpunkt finns referenssidor för varje typ av.
Exempel på grundläggande begäranden med hjälp av API för anpassad sökning finns [anpassad sökning i Snabbstarter för lösningar](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/)
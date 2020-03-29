---
title: Så här bläddrar du till sökresultat - API:er för bing-sökning
titleSuffix: Azure Cognitive Services
description: Läs om hur du bläddrar igenom sökresultaten från API:erna för Bing-sökning.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: 26CA595B-0866-43E8-93A2-F2B5E09D1F3B
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: aahi
ms.openlocfilehash: ea883bb294a8769b3c9be1e0eafc2e3e7c811b48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73481741"
---
# <a name="how-to-page-through-results-from-the-bing-search-apis"></a>Så här bläddrar du igenom resultaten från API:erna för Bing-sökning

När du skickar ett samtal till API:erna för Bing-, custom-, image-, nyhets- eller videosökning returnerar Bing en delmängd av det totala antalet resultat som kan vara relevanta för frågan. Om du vill hämta det uppskattade totala antalet tillgängliga `totalEstimatedMatches` resultat öppnar du svarsobjektets fält. 

Ett exempel: 

```json
{
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=3A43CA...",
        "totalEstimatedMatches" : 262000,
        "value" : [...]
    }
}  
```

## <a name="paging-through-search-results"></a>Söka via sökresultat

Om du vill bläddra igenom `count` `offset` de tillgängliga resultaten använder du parametrarna och frågar när du skickar din begäran.  

> [!NOTE]
>
> * Växling med API:erna för Bing-video, bild`/video/search`och Nyheter`/news/search`gäller endast`/image/search`allmänna videosökningar ( ), nyheter ( ) och bild ( ) sökningar. Det går inte att söka igenom populära ämnen och kategorier.  
> * Fältet `TotalEstimatedMatches` är en uppskattning av det totala antalet sökresultat för den aktuella frågan. När du `count` ställer `offset` in parametrarna och kan den här uppskattningen ändras.

| Parameter | Beskrivning                                                                                                                                                                |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `count`   | Anger antalet resultat som ska returneras i svaret. Observera att standardvärdet `count`för och det maximala antalet resultat som du kan begära varierar beroende på API. Du hittar dessa värden i referensdokumentationen under [Nästa steg](#next-steps). |
| `offset`  | Anger antalet resultat som ska hoppa övers. Den `offset` är noll-baserad och bör`totalEstimatedMatches` - `count`vara mindre än ( ).                                           |

Om du till exempel vill visa 15 resultat per `count` sida anger `offset` du 15 och 0 för att få den första resultatsidan. För varje efterföljande API-anrop ökar du med `offset` 15. I följande exempel begärs 15 webbsidor som börjar vid offset 45.

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&count=15&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

Om du använder `count` standardvärdet behöver du `offset` bara ange frågeparametern i DINA API-anrop.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/search?q=sailing+dinghies&offset=45&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
Host: api.cognitive.microsoft.com  
```

När du använder API:erna för Bing-bild och video kan du använda `nextOffset` värdet för att undvika dubbla sökresultat. Hämta värdet från `Images` `Videos` objekten eller svarsobjekten och `offset` använd det i dina begäranden med parametern.  

> [!NOTE]
> Api:et för webbsökning på Bing returnerar sökresultat som kan innehålla webbsidor, bilder, videor och nyheter. När du bläddrar igenom sökresultaten från API:et för webbsökning på Bing söker du bara webbsidor och inte andra [svarstyper](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage)som bilder eller nyheter. Sökresultat i `WebPage` objekt kan innehålla resultat som även visas i andra svarstyper.
>
> Om du `responseFilter` använder frågeparametern utan att ange några `count` `offset` filtervärden ska du inte använda parametrarna och. 

## <a name="next-steps"></a>Nästa steg

* [Vilka api:er för webbsökning på Bing?](bing-api-comparison.md)
* [Referens för API för webbsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)
* [Bing Anpassad sökning API v7 referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
* [Bing Nyheter Sök API v7 referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)
* [Bing Video Search API v7 referens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference)
* [Api för Bing-bildsökning v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)

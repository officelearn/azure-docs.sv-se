---
title: Hämta avbildningar från webben med sökning i Bing | Microsoft Docs
description: Använd den bildsökning i Bing för att söka efter och få relevanta avbildningar från webben.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: AB1B9898-C94A-4B59-91A1-8623C94BA3D4
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: c379cc2dfbe53f83e4d79500cd947879407c8d55
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082615"
---
# <a name="getting-images-from-the-web-with-the-bing-image-search-api"></a>Hämta avbildningar från webben med sökning i Bing

Med Search REST API för Bing-avbildning kan hämta du bilder från webben som är relaterade till användarens sökterm genom att skicka följande GET-begäran:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

> [!IMPORTANT]
> * Alla begäranden måste göras från en server och inte från en klient.
> * Om det är första gången du anropar någon av Bing search API: er omfattar inte rubrik för klient-ID. Inkludera endast klient-ID om du har tidigare kallade ett Bing-API som returnerade ett klient-ID för användaren och enheten kombination.
> * Avbildningar måste visas i den ordning som anges i svaret.

## <a name="getting-images-from-a-specific-web-domain"></a>Hämta bilder från en specifik domän

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

> [!NOTE]
> Svar på frågor med hjälp av den `site:` operatör kan innehålla vuxet innehåll oavsett den [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) inställningen. Använd bara `site:` om du är medveten om innehållet på domänen.

I följande exempel visas hur du hämtar små bilder från ContosoSailing.com som Bing har identifierat den senaste veckan.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="filtering-images"></a>Filtrera bilder

 Som standard returnerar avbildningen Search API: et alla avbildningar som är relevanta för frågan. Om du vill filtrera bilder Bing returnerar (t.ex, för att returnera endast avbildningar med en transparant bakgrund eller en viss storlek) kan du använda följande frågeparametrar:

* [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) – filtrerar bilder efter bredd–höjd-förhållande (till exempel bilder i vanligt eller i brett format)
* [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) – filtrerar bilder efter dominant färg eller svartvitt
* [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) – filtrerar bilder efter ålder (till exempel bilder som upptäckts av Bing den senaste veckan)
* [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) – filtrerar bilder efter bredd och höjd
* [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) – filtrerar bilder efter innehåll (till exempel bilder som endast visar ansikten)
* [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) – filtrerar bilder efter typ (till exempel ClipArt, animerade GIF-bilder eller genomskinliga bakgrunder)
* [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) – filtrerar bilder efter den typ av licens som associeras med webbplatsen
* [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) – filtrerar bilder efter storlek, till exempel små bilder upp till 200 x 200 pixlar

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](http://msdn.microsoft.com/library/ff795613.aspx).

    > [!NOTE]
    > Responses to queries using the `site:` operator may include adult content regardless of the [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch) setting. Only use `site:` if you are aware of the content on the domain.

I följande exempel visas hur du hämtar små bilder från ContosoSailing.com som Bing har identifierat den senaste veckan.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="bing-image-search-response-format"></a>Bildsökning i Bing svarsformat

Svarsmeddelandet från Bing innehåller en [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) svar som innehåller en lista med avbildningar som Azure cognitive services är relevant för frågan. Varje [bild](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image) objekt i listan innehåller följande information om avbildningen: URL: en, dess storlek, dess dimensioner, dess kodningsformat, en URL till en miniatyrbild för avbildningen och den miniatyrbilden dimensioner.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=73118C8B4E3...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP.GNarK7m...",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=73118C8B4E3D4C3687...",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "en.contoso.org\/wiki\/File:Rich_Passage...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "imageInsightsToken": "ccid_GNarK7ma*mid_CCF85447ADA6...",
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    },
    "imageId": "CCF85447ADA6FFF9E96E7DF0B796F7A86E34593",
    "accentColor": "376094"
},
```

När du anropar API för bildsökning i Bing returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. Svarets fält `totalEstimatedMatches` innehåller en uppskattning av det antal bilder som är tillgängliga för visning. Mer information om hur du bläddrar bland återstående bilder finns i [Paging Images](../paging-images.md) (Bläddra bland bilder).

## <a name="next-steps"></a>Nästa steg

Om du inte har testat den bildsökning i Bing innan du prova en [snabbstarten](../quickstarts/csharp.md). Om du letar efter ett mer komplext prova guiden för att skapa en [enkelsidiga webbapp](../tutorial-bing-image-search-single-page-app.md).

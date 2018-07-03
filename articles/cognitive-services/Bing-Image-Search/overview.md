---
title: Vad är bildsökning i Bing? | Microsoft Docs
description: Lär dig hur du använder API för bildsökning i Bing för att söka efter bilder på webben.
services: cognitive-services
author: swhite-msft
manager: ehansen
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: scottwhi
ms.openlocfilehash: 457707065059b5cb83c9d2b81f5d073cf1c89b84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36336527"
---
# <a name="what-is-bing-image-search"></a>Vad är bildsökning i Bing?

API för bildsökning i Bing ger funktioner som liknar [Bing Images](https://www.bing.com/images) genom att du kan skicka en användarsökfråga till Bing och få tillbaka en lista över relevanta bilder.

Om du utvecklar en sökresultatsida endast för bilder för att hitta bilder som är relevanta för användarens sökfråga anropar du detta API i stället för att anropa det mer generella [API för webbsökning](../bing-web-search/search-the-web.md). Om du vill hitta bilder och andra typer av innehåll såsom webbplatser, nyheter och videor anropar du API för webbsökning.

## <a name="suggesting--using-search-terms"></a>Föreslå och använda söktermer

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett sin sökterm kodar URL:en termen innan den ställer in [q](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) frågeparametern. Om användaren till exempel anger *segeljollar* ställer du in `q` till `sailing+dinghies` eller `sailing%20dinghies`.

## <a name="getting-images"></a>Hämta bilder

För att hämta bilder relaterade till användarens söktermer från webben skickar du följande GET-begäran:

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies&mkt=en-us HTTP/1.1
Ocp-Apim-Subscription-Key: 123456789ABCDE
X-MSEdge-ClientIP: 999.999.999.999
X-Search-Location: lat:47.60357;long:-122.3295;re:100
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>
Host: api.cognitive.microsoft.com
```

Alla begäranden måste ske från en server. Du kan inte göra anrop från en klient.

Om det är den första gången du anropar ett Bing-API inkluderar du inte klientens ID-huvud. Inkludera endast klient-ID om du har anropat ett Bing-API förut och om Bing returnerade ett klient-ID för användar- och enhetskombinationen.

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](http://msdn.microsoft.com/library/ff795613.aspx).

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&mkt=en-us HTTP/1.1
```

Svaret innehåller ett [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images)-svar (Bilder) som innehåller en lista över bilder som Bing ansåg vara relevanta för frågan. Varje [Image](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image)-objekt (Bild) i listan innefattar bildens URL, storlek, mått och kodningsformat. Bildobjektet inkluderar även URL för en miniatyrbild av bilden och miniatyrbildens mått.

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

Du kan visa ett collage av alla miniatyrbilderna eller en delmängd av miniatyrbilderna. Om du visar en delmängd bör du ge användaren alternativet att visa resten av bilderna. Du måste visa bilderna i den ordning som anges i svaret.

Du kan även expandera miniatyrbilden när användaren hovrar markören över den. Se till att tillskriva bildkällan om du expanderar bilden. Till exempel kan du extrahera värden från [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-hostpagedisplayurl) och visa den nedanför bilden. Information om att ändra storlek på miniatyrbilden finns i [Resizing and Cropping Thumbnails](./resize-and-crop-thumbnails.md) (Ändra storlek på och beskär miniatyrbilder).

<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Om användaren klickar på miniatyrbilden kan du använda [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-contenturl) för att visa bilden i fullstorlek för användaren. Se till att tillskriva bildkällan.

Om `shoppingSourcesCount` eller `recipeSourcesCount` är större än noll bör du lägga till ikoner som visar att det finns shopping eller recept för objektet i bilden.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

För att få insikter om bilden, till exempel webbplatser som inkluderar bilden eller personer som kändes igen i bilden, använder du [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#image-imageinsightstoken). Information finns i [Image Insights](./image-insights.md) (Bildinsikter).

## <a name="filtering-images"></a>Filtrera bilder

 Som standard returnerar Images Search API alla bilder som är relevanta för frågan. Om du däremot endast vill få bilder med en genomskinlig bakgrund eller bilder med en specifik storlek använder du följande frågeparametrar för att filtrera de bilder som Bing returnerar.  

- [aspect](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#aspect) – filtrerar bilder efter bredd–höjd-förhållande (till exempel bilder i vanligt eller i brett format)
- [color](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#color) – filtrerar bilder efter dominant färg eller svartvitt
- [freshness](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#freshness) – filtrerar bilder efter ålder (till exempel bilder som upptäckts av Bing den senaste veckan)
- [height](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#height), [width](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#width) – filtrerar bilder efter bredd och höjd
- [imageContent](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagecontent) – filtrerar bilder efter innehåll (till exempel bilder som endast visar ansikten)
- [imageType](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#imagetype) – filtrerar bilder efter typ (till exempel ClipArt, animerade GIF-bilder eller genomskinliga bakgrunder)
- [license](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#license) – filtrerar bilder efter den typ av licens som associeras med webbplatsen
- [size](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#size) – filtrerar bilder efter storlek, till exempel små bilder upp till 200 x 200 pixlar

Om du vill hämta bilder från en specifik domän använder du frågeoperatorn [site:](http://msdn.microsoft.com/library/ff795613.aspx).

> [!NOTE]
> Beroende på frågan finns det en risk om du använder operatorn `site:` att svaret har innehåll som är olämpligt för barn oberoende av inställningen för [safeSearch](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#safesearch). Du bör endast använda `site:` om du är medveten om innehållet på webbplatsen och ditt scenario tillåter möjligheten att det förekommer innehåll som är olämpligt för barn.

I följande exempel visas hur du hämtar små bilder från ContosoSailing.com som Bing har identifierat den senaste veckan.  

```http
GET https://api.cognitive.microsoft.com/bing/v7.0/images/search?q=sailing+dinghies+site:contososailing.com&size=small&freshness=week&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
```

## <a name="pivoting-the-query"></a>Pivotera frågan

Om Bing kan segmentera den ursprungliga sökfrågan innehåller objektet [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) (Bilder) fältet `pivotSuggestions`. Om den ursprungliga frågan till exempel var *Microsoft Surface* kan Bing segmentera frågan till *Microsoft* och *Surface*.  

Följande exempel visar pivotförslagen för *Microsoft Surface*.  

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface&FORM=OIIARP",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions": [...],
    "pivotSuggestions": [{
        "pivot": "microsoft",
        "suggestions": [{
            "text": "Contoso Surface",
            "displayText": "Contoso",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=OtterBox+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Contoso...",
                    "searchLink": "https:\/\/api.cognitive.microsoft.com\/api...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Contoso+Surface..."
            }
        },
        {
            "text": "Adatum Surface",
            "displayText": "Adatum",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Adatum+Surface&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Adatum+Surface&pid=Ap..."
            }
        },
        ...
        ]
    },
    {
        "pivot": "surface",
        "suggestions": [{
            "text": "Microsoft Surface4",
            "displayText": "Surface4",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface...",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft..."
            }
        },
        {
            "text": "Microsoft Tablet",
            "displayText": "Tablet",
            "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Tablet&FORM=IRQBPS",
            "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?...",
            "thumbnail": {
                "thumbnailUrl": "https:\/\/tse3.mm.bing.net\/th?q=Microsoft+Tablet..."
            }
        },
        ...
    ],
    "nextOffsetAddCount": 0
}
```

Du kan sedan visa användaren valfria frågetermer om de är av intresse för användaren.

Fältet `pivotSuggestions` innehåller listan över segment (pivoter) som den ursprungliga frågan delades in i. För varje pivot innehåller svaret en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj)-objekt som innehåller föreslagna frågor. Fältet `text` innehåller den föreslagna frågan, och fältet `displayText` innehåller den term som ersätter pivoten i den ursprungliga frågan. Ett exempel kan vara Release Date of Surface (Lanseringsdatum för Surface).

Du kan använda fälten `text` och `thumbnail` för att visa användaren pivotfrågesträngarna om pivotfrågesträngen är det användaren letar efter. Gör miniatyrbilden och texten klickbara genom att använda URL:en `webSearchUrl` eller URL:en `searchLink`. Använd `webSearchUrl` för att skicka användaren till Bing-sökresultat eller `searchLink` om du tillhandahåller din egen resultatsida.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expanding-the-query"></a>Expandera frågan

Om Bing kan expandera frågan för att begränsa den ursprungliga sökningen innehåller objektet [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) (Bilder) fältet `queryExpansions`. Om frågan till exempel var *Microsoft Surface* kan de utökade frågorna bli: Microsoft Surface **Pro 3**, Microsoft Surface **RT**, Microsoft Surface **Phone** samt Microsoft Surface **Hub**.

Följande exempel visar de expanderade frågorna för *Microsoft Surface*.

```json
{
    "_type": "Images",
    "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=microsoft%20surface...",
    "totalEstimatedMatches": 1000,
    "value": [...],
    "queryExpansions":  [{
        "text": "Microsoft Surface Pro 3",
        "displayText": "Pro 3",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Pro+3...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=Microsoft...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Pro+3..."
        }
    },
    {
        "text": "Microsoft Surface RT",
        "displayText": "RT",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+RT...",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+RT..."
        }
    },
    {
        "text": "Microsoft Surface Phone",
        "displayText": "Phone",
        "webSearchUrl": "https:\/\/www.bing.com\/images\/search?q=Microsoft+Surface+Phone",
        "searchLink": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images\/search?q=...",
        "thumbnail": {
            "thumbnailUrl": "https:\/\/tse4.mm.bing.net\/th?q=Microsoft+Surface+Phone..."
        }
    }],
    "pivotSuggestions": [...],
    "nextOffsetAddCount": 0
}
```

Fältet `queryExpansions` innehåller en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj)-objekt. Fältet `text` innehåller den expanderade frågan, och fältet `displayText` innehåller expansionstermen. Du kan använda fälten `text` och `thumbnail` för att visa användaren de expanderade frågesträngarna om den expanderade frågesträngen är det användaren letar efter. Gör miniatyrbilden och texten klickbara genom att använda URL:en `webSearchUrl` eller URL:en `searchLink`. Använd `webSearchUrl` för att skicka användaren till Bing-sökresultat eller `searchLink` om du tillhandahåller din egen resultatsida.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Kom igång snabbt med din första begäran genom att läsa [snabbstarten för C#](quickstarts/csharp.md).

Bekanta dig med referensen för [API för bildsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference). Referensen innehåller listan över slutpunkter, rubriker och frågeparametrar som du använder för att begära sökresultat. Den omfattar även definitioner av svarsobjekten.

För att förbättra användarfunktionen för sökrutan läser du [API för automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). När användaren anger sina frågetermer kan du anropa det här API:t för att få relevanta frågetermer som har använts av andra användare.

Se till att läsa [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Krav för användning och visning i Bing) så att du inte bryter mot någon av reglerna om användning av sökresultat.

När du anropar API för bildsökning i Bing returnerar Bing en lista med resultat. Listan är en delmängd av det totala antalet resultat som är relevanta för frågan. Svarets fält `totalEstimatedMatches` innehåller en uppskattning av det antal bilder som är tillgängliga för visning. Mer information om hur du bläddrar bland återstående bilder finns i [Paging Images](./paging-images.md) (Bläddra bland bilder).
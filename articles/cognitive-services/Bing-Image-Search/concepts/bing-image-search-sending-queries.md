---
title: Anpassa och föreslå bildsökningsfrågor - API för bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Läs mer om hur du anpassar de sökfrågor du skickar till API:et för bildsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: aahi
ms.openlocfilehash: d833b017004365e9dad7241e360f42ff41a55883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67542749"
---
# <a name="customize-and-suggest-image-search-queries"></a>Anpassa och föreslå bildsökfrågor

Använd den här artikeln om du vill lära dig hur du anpassar frågor och föreslår söktermer att skicka till API:et för bildsökning i Bing.

## <a name="suggest-search-terms"></a>Föreslå söktermer

Om din app har en sökruta där söktermer anges kan du använda API:et [för automatiska förslag på Bing](../../bing-autosuggest/get-suggested-search-terms.md) för att förbättra upplevelsen. API:et kan visa föreslagna söktermer i realtid. API:et returnerar föreslagna frågesträngar baserat på partiella söktermer och Cognitive Services.

## <a name="pivot-the-query"></a>Pivotera frågan

Om Bing kan segmentera den ursprungliga sökfrågan `pivotSuggestions`innehåller det returnerade [images-objektet](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) . Pivotförslag kan visas som valfria söktermer för användaren. Om den ursprungliga frågan till exempel var *Microsoft Surface*kan Bing segmentera frågan till *Microsoft* och *Surface* och ange föreslagna pivoter för varje. Dessa förslag kan visas som valfria frågetermer för användaren.

I följande exempel visas pivotförslag för *Microsoft Surface:*  

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

Fältet `pivotSuggestions` innehåller listan över segment (pivoter) som den ursprungliga frågan delades in i. För varje pivot innehåller svaret en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objekt som innehåller föreslagna frågor. Fältet `text` innehåller den föreslagna frågan. Fältet `displayText` innehåller termen som ersätter pivoten i den ursprungliga frågan. Ett exempel är Utgivningsdatum för Surface.

Om pivotfrågesträngen är vad användaren `text` letar `thumbnail` efter använder du fälten och för att visa pivotfrågesträngarna. Gör miniatyrbilden och texten klickbar med hjälp av WEBBADRESSEN `webSearchUrl` eller WEBBADRESSEN. `searchLink` Används `webSearchUrl` för att skicka användaren till Bing-sökresultaten. Om du anger din egen `searchLink`resultatsida använder du .

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Expandera frågan

Om Bing kan expandera frågan för att begränsa den ursprungliga sökningen innehåller objektet [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) (Bilder) fältet `queryExpansions`. Om frågan till exempel var *Microsoft Surface*kan de utökade frågorna vara:
- Microsoft Surface **Pro 3**.
- Microsoft Surface **RT**.
- Microsoft Surface **Phone**.
- Microsoft Surface **Hub**.

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

Fältet `queryExpansions` innehåller en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#query_obj)-objekt. Fältet `text` innehåller den expanderade frågan. Fältet `displayText` innehåller expansionstermen. Om den expanderade frågesträngen är vad användaren `text` `thumbnail` letar efter använder du fälten och för att visa de expanderade frågesträngarna. Gör miniatyrbilden och texten klickbar med hjälp av WEBBADRESSEN `webSearchUrl` eller WEBBADRESSEN. `searchLink` Används `webSearchUrl` för att skicka användaren till Bing-sökresultaten. Om du anger din egen `searchLink`resultatsida använder du .

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Om du inte har provat API:et för bing-bildsökning tidigare provar du en [snabbstart](../quickstarts/csharp.md). Om du letar efter något mer komplext kan du prova självstudien för att skapa en [webbsida](../tutorial-bing-image-search-single-page-app.md).

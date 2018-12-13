---
title: Skicka avbildningen frågor – bildsökning i Bing
titleSuffix: Azure Cognitive Services
description: Lär dig mer om hur du anpassar sökfrågor som du skickar till sökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: 5d850b7b95c15137f70a0d343f97171ec2d2f753
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256986"
---
# <a name="send-queries-to-the-bing-image-search-api"></a>Skicka frågor till sökning i Bing

Sökning i Bing ger en upplevelse som liknar Bing.com/Images. Du kan använda den för att skicka en sökfråga till Bing och få tillbaka en lista över relevanta avbildningar.

## <a name="use-and-suggest-search-terms"></a>Använd och föreslå söktermer

När ett sökvillkor har angetts, URL-koda termen innan du anger den [ **q** ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) frågeparameter. Exempel: Om du anger *avseglingen dinghies*anger `q` till `sailing+dinghies` eller `sailing%20dinghies`.

Om din app har en sökruta där sökvillkor har angetts, kan du använda den [automatiska förslag i Bing](../../bing-autosuggest/get-suggested-search-terms.md) att förbättra upplevelsen. API: et kan visa föreslagna sökvillkor i realtid. API: et returnerar föreslagna frågesträngar utifrån partiella söktermer och Cognitive Services.

## <a name="pivot-the-query"></a>Pivotera frågan

Om Bing kan segmentera ursprungliga sökfrågan, den returnerade [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objektet innehåller `pivotSuggestions`. Pivot förslag kan visas som valfria sökvillkor för användaren. Exempel: om den ursprungliga frågan var *Microsoft Surface*, Bing kan segmentera frågan i *Microsoft* och *Surface* och ange föreslagna pivoteringsmöjligheter för varje. Förslagen kan visas som valfria sökord för användaren.

I följande exempel visas pivot-förslag på *Microsoft Surface*:  

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

Fältet `pivotSuggestions` innehåller listan över segment (pivoter) som den ursprungliga frågan delades in i. För varje pivot innehåller svaret en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj)-objekt som innehåller föreslagna frågor. Den `text` fältet innehåller föreslagna frågan. Den `displayText` fältet innehåller termen som ersätter pivot i den ursprungliga frågan. Ett exempel är datum för versionen av angrepp.

Om frågesträngen pivot är vad användaren är ute efter använder den `text` och `thumbnail` fält som ska visas i pivot fråga strängar. Gör miniatyr och text klickbara med hjälp av den `webSearchUrl` URL eller `searchLink` URL: en. Använd `webSearchUrl` att skicka användaren till Bing-sökresultat. Om du anger din egen resultatsidan kan använda `searchLink`.

<!-- Need a sanitized version of the image
The following shows an example of the pivot queries.

![Pivot suggestions](./media/cognitive-services-bing-images-api/bing-image-pivotsuggestion.GIF)
-->

## <a name="expand-the-query"></a>Expandera frågan

Om Bing kan expandera frågan för att begränsa den ursprungliga sökningen innehåller objektet [Images](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) (Bilder) fältet `queryExpansions`. Exempel: Om frågan var *Microsoft Surface*, utökad frågorna kan vara:
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

Fältet `queryExpansions` innehåller en lista över [Query](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query_obj)-objekt. Den `text` fältet innehåller utökade frågan. Den `displayText` fältet innehåller expansion termen. Om den utökade frågesträngen är vad användaren är ute efter använder den `text` och `thumbnail` fält att visa de utökade frågesträngarna. Gör miniatyr och text klickbara med hjälp av den `webSearchUrl` URL eller `searchLink` URL: en. Använd `webSearchUrl` att skicka användaren till Bing-sökresultat. Om du anger din egen resultatsidan kan använda `searchLink`.

<!-- Removing until we can replace with a sanitized image.
The following shows an example Bing implementation that uses expanded queries. If the user clicks the Microsoft Surface Pro 3 link, they're taken to the Bing search results page, which shows them images of the Pro 3.

![Query expansion suggestions](./media/cognitive-services-bing-images-api/bing-image-queryexpansion.GIF)
-->


## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Om du inte har testat den bildsökning i Bing innan du prova en [snabbstarten](../quickstarts/csharp.md). Om du letar efter ett mer komplext prova guiden att skapa en [enkelsidiga webbapp](../tutorial-bing-image-search-single-page-app.md).

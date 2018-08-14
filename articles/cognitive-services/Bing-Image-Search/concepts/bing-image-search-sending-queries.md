---
title: Skicka frågor till sökning i Bing | Microsoft Docs
description: Läs mer om att skicka och anpassa sökfrågor som skickas till den bildsökning i Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: C2862E98-8BCC-423B-9C4A-AC79A287BE38
ms.service: cognitive-services
ms.component: bing-image-search
ms.topic: conceptual
ms.date: 8/8/2018
ms.author: aahi
ms.openlocfilehash: d74f59ffcf095e639686a3ada3b09dac988fc544
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2018
ms.locfileid: "40082618"
---
# <a name="sending-queries-to-the-bing-image-search-api"></a>Skicka frågor till sökning i Bing

Sökning i Bing ger en upplevelse som liknar Bing.com/Images genom att låta dig skicka en sökfråga för användaren till Bing och få tillbaka en lista över relevanta avbildningar.

## <a name="using-and-suggesting-search-terms"></a>Med hjälp av och föreslår söktermer

När ett sökvillkor har angetts, URL-koda termen innan du anger den [ **q** ](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#query) frågeparameter. Exempel: om en användare anger *avseglingen dinghies*anger `q` till `sailing+dinghies` eller `sailing%20dinghies`.

Om din app har en sökruta där sökvillkor har angetts, kan du använda den [automatiska förslag i Bing](../../bing-autosuggest/get-suggested-search-terms.md) att förbättra upplevelsen genom att visa föreslagna sökvillkor i realtid. API: et returnerar föreslagna fråga strängar baserat på partiella söktermer och Azure cognitive services.

## <a name="pivoting-the-query"></a>Pivotera frågan

Om Bing kan segmentera ursprungliga sökfrågan, den returnerade [avbildningar](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference#images) objekt innehåller `pivotSuggestions` som kan visas som valfria sökvillkor för användaren. Exempel: om den ursprungliga frågan var *Microsoft Surface*, Bing kan segmentera frågan i *Microsoft* och *Surface* och ange föreslagna pivoteringsmöjligheter för varje. Dessa kan visas som valfria sökord för användaren.

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

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

Om du inte har testat den bildsökning i Bing innan du prova en [snabbstarten](../quickstarts/csharp.md). Om du letar efter ett mer komplext prova guiden för att skapa en [enkelsidiga webbapp](../tutorial-bing-image-search-single-page-app.md).

---
title: Vad är API:et för videosökning i Bing?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du söker efter videor på webben med hjälp av API för videosökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.openlocfilehash: d5a4c3110186329516f10465c5e80a10b0ceb7b7
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2020
ms.locfileid: "94379828"
---
# <a name="what-is-the-bing-video-search-api"></a>Vad är API:et för videosökning i Bing?

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

API för videosökning i Bing gör det enkelt att lägga till videosökfunktioner i dina tjänster och program. Genom att skicka användarsökfrågor med API:et kan du hämta och visa relevanta och högkvalitativa videor på ett sätt som liknar [Bing Video](https://www.bing.com/video). Använd detta API för sökresultat som endast innehåller videor. [API för webbsökning i Bing](../bing-web-search/overview.md) kan returnera andra typer av webbinnehåll såsom webbplatser, videor, nyheter och bilder.

## <a name="bing-video-search-api-features"></a>Funktioner i API för videosökning i Bing

| Funktion                                                                                                                                                                                 | Beskrivning                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Föreslå söktermer i realtid](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Förbättra programupplevelsen med [automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). Föreslagna sökord visas medan användaren skriver. |
| [Filtrera och begränsa videoresultat](concepts/get-videos.md#filtering-videos)                      | Filtrera videor som returneras genom att redigera frågeparametrar.                                                                                                       |
| [Beskär, ändra storlek och visa miniatyrer](../bing-web-search/resize-and-crop-thumbnails.md)                                                | Redigera och visa miniatyrförhandsversioner för de videor som returneras av API för videosökning i Bing.                                                                                      |
| [Hämta populära videor](trending-videos.md) | Sök efter trendande videor från hela världen.                                                                                                          |
| [Hämta information om video](video-insights.md) | Anpassa en sökning efter populära videor från hela världen.                                                                                                          |

## <a name="workflow"></a>Arbetsflöde

API för videosökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. Du kan använda tjänsten med hjälp av antingen [REST API](./quickstarts/csharp.md)eller [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

1. Skapa ett [Cognitive Services-API-konto](../cognitive-services-apis-create-account.md) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/cognitive-services/) kostnads fritt.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.


## <a name="next-steps"></a>Nästa steg

[Interaktiva demon](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) för API för videosökning i Bing visar hur du kan anpassa en sökfråga och sök efter videor på webben.

Använd [snabbstarten](./quickstarts/csharp.md) för att snabbt komma igång med din första API-begäran.

## <a name="see-also"></a>Se även

* Referenssidan för [API för videosökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference) innehåller listan över slutpunkter, rubriker och frågeparametrar som används för att begära sökresultat.

* I [användnings- och visningskraven för Bing](../bing-web-search/use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.

* Gå till [sidan Bing-sökning API Hub](../bing-web-search/overview.md) och utforska de andra tillgängliga API: erna.
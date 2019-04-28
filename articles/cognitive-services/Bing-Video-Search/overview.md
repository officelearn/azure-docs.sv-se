---
title: Vad är API:et för videosökning i Bing?
titlesuffix: Azure Cognitive Services
description: Lär dig hur du söker efter videor på webben med hjälp av API för videosökning i Bing.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-video-search
ms.topic: overview
ms.date: 01/31/2019
ms.author: scottwhi
ms.openlocfilehash: f56893f830720c57c66eb4c17bb2771efbb73f6f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62124653"
---
# <a name="what-is-the-bing-video-search-api"></a>Vad är API:et för videosökning i Bing?

API för videosökning i Bing gör det enkelt att lägga till videosökfunktioner i dina tjänster och program. Genom att skicka användarsökfrågor med API:et kan du hämta och visa relevanta och högkvalitativa videor på ett sätt som liknar [Bing Video](https://www.bing.com/video). Använd detta API för sökresultat som endast innehåller videor. [API för webbsökning i Bing](../bing-web-search/search-the-web.md) kan returnera andra typer av webbinnehåll såsom webbplatser, videor, nyheter och bilder.

## <a name="bing-video-search-api-features"></a>Funktioner i API för videosökning i Bing

| Funktion                                                                                                                                                                                 | Beskrivning                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Föreslå söktermer i realtid](concepts/sending-requests.md#suggest-search-terms-with-the-bing-autosuggest-api) | Förbättra programupplevelsen med [automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). Föreslagna sökord visas medan användaren skriver. |
| [Filtrera och begränsa videoresultat](concepts/get-videos.md#filtering-videos)                      | Filtrera videor som returneras genom att redigera frågeparametrar.                                                                                                       |
| [Beskär, ändra storlek och visa miniatyrer](resize-and-crop-thumbnails.md)                                                | Redigera och visa miniatyrförhandsversioner för de videor som returneras av API för videosökning i Bing.                                                                                      |
| [Hämta trendande videor](trending-videos.md) | Sök efter trendande videor från hela världen.                                                                                                          |
| [Hämta insikter om videor](video-insights.md) | Anpassa en sökning efter populära videor från hela världen.                                                                                                          |

## <a name="workflow"></a>Arbetsflöde

API för videosökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON. Du kan använda tjänsten med hjälp av antingen [REST API](csharp.md)eller [SDK](video-search-sdk-quickstart.md).

1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnadsfritt.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.


## <a name="next-steps"></a>Nästa steg

[Interaktiva demon](https://azure.microsoft.com/services/cognitive-services/bing-video-search-api/) för API för videosökning i Bing visar hur du kan anpassa en sökfråga och sök efter videor på webben.

När du är redo att anropa API:et skapar du ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnadsfritt.

Använd [snabbstarten](csharp.md) för att snabbt komma igång med din första API-begäran.

## <a name="see-also"></a>Se också

* Referenssidan för [API för videosökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-video-api-v7-reference) innehåller listan över slutpunkter, rubriker och frågeparametrar som används för att begära sökresultat.

* I [användnings- och visningskraven för Bing](./useanddisplayrequirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.
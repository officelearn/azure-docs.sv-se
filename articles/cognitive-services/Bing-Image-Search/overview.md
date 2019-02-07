---
title: Vad är API:et för bildsökning i Bing?
titleSuffix: Azure Cognitive Services
description: Med API:et för bildsökning i Bing kan du använda Bings kognitiva bildsökningsfunktioner i programmet. Genom att skicka användarsökfrågor med API:et kan du hämta och visa relevanta och högkvalitativa avbildningar som liknar Bing-avbildningar.
services: cognitive-services
author: aahill
manager: cgronlun
ms.assetid: 1446AD8B-A685-4F5F-B4AA-74C8E9A40BE9
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: overview
ms.date: 10/11/2017
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 4ae9c0c1fb6cd341e812f9e8b35eaff789637561
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55765654"
---
# <a name="what-is-the-bing-image-search-api"></a>Vad är API:et för bildsökning i Bing?

Med API:et för bildsökning i Bing kan du använda Bings kognitiva bildsökningsfunktioner i programmet. Genom att skicka användarsökfrågor med API:et kan du hämta och visa relevanta och högkvalitativa avbildningar som liknar [Bing-avbildningar](https://www.bing.com/images).

Observera att API:et för bildsökning i Bing endast returnerar bildresultat. Använd [API:et för webbsökning i Bing](../bing-web-search/search-the-web.md), [API:et för videosökning](https://docs.microsoft.com/azure/cognitive-services/Bing-Video-Search) och [API:et för nyhetssökning](https://review.docs.microsoft.com/azure/cognitive-services/bing-news-search) för andra typer av webbinnehåll.

## <a name="bing-image-search-features"></a>API för bildsökning i Bing – funktioner

Bildsökning i Bing hittar och returnerar främst relevanta bilder från en sökfråga, men tjänsten tillhandahåller också flera ytterligare funktioner för att på ett smart och fokuserat sätt hämta bilder på webben.


| Funktion                                                                                                                                                                                 | Beskrivning                                                                                                                                                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Föreslå söktermer i realtid](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) | Förbättra programupplevelsen med [automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). Föreslagna sökord visas medan användaren skriver. |
| [Filtrera och begränsa bildresultat](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images)                       | Filtrera de bilder Bing returnerar genom att redigera frågeparametrarna.                                                                                                       |
| [Beskär, ändra storlek och visa miniatyrer](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/resize-and-crop-thumbnails)                                                | Redigera och visa miniatyrer för de bilder som returneras med bildsökningen i Bing.                                                                                      |
| [Pivotera och expandera användarsökfrågor](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries)               | Utöka sökfunktionen genom att inkludera och visa Bing-föreslagna sökord för frågor.                                                                    |
| [Hämta populära bilder](https://review.docs.microsoft.com/azure/cognitive-services/bing-image-search/trending-images)                                                                     | Anpassa en sökning efter populära bilder från hela världen.                                                                                                          |

## <a name="workflow"></a>Arbetsflöde

API:et för bildsökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda tjänsten med hjälp av antingen [REST API](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp?)eller [SDK](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart).

1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnadsfritt.
2. Skicka en begäran till API:et med en giltig [sökfråga](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries).
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Prova först med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/) av API:et för bildsökning i Bing.
Den här demonstrationen visar hur du snabbt kan anpassa en sökfråga och finkamma webben i jakt på bilder.

När du är redo att anropa API:et skapar du ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account). Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) kostnadsfritt.

För att komma igång snabbt med din första API-begäran kan du lära dig att:

* [Skicka sökfrågor till Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/quickstarts/csharp) med hjälp av REST API eller
* [Begära och filtrera](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/image-search-sdk-quickstart) bilderna som Bing returnerar med hjälp av SDK:et.

## <a name="see-also"></a>Se även

* [API:et för bildsökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference)-referensavsnittet innehåller definitioner och information om slutpunkterna, rubrikerna, API-svaren och frågeparametrarna som du kan använda till att begära bildsökresultat.

* I [användnings- och visningskraven för Bing](./useanddisplayrequirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.

* I ämnet om att [hämta bilder från webben med API:et för bildsökning i Bing](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images) beskriver vi hur du söker efter och hämtar bilder från webben.

* I avsnittet om att [skicka och arbeta med sökfrågor](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-sending-queries) beskriver vi hur du skapar, anpassar och pivoterar sökfrågor.

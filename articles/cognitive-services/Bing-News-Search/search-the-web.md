---
title: Vad är API för nyhetssökning i Bing?
titlesuffix: Azure Cognitive Services
description: Lär dig hur du använder API för nyhetssökning i Bing för att söka efter aktuella nyheter i flera kategorier på webben, inklusive rubriker och trendande ämnen.
services: cognitive-services
author: swhite-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 01/10/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 1409839718b7ea2f685952a396ea2f52bd34fae1
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55148820"
---
# <a name="what-is-the-bing-news-search-api"></a>Vad är API för nyhetssökning i Bing?

Med API för nyhetssökning i Bing är det enkelt att integrera Bings funktioner för kognitiv nyhetssökning i dina program. API:et fungerar ungefär som [Nyhetssökning i Bing](https://www.bing.com/news), där du kan söka efter och få relevanta nyhetsartiklar.

Observera att API:et för nyhetssökning i Bing endast returnerar nya sökresultat. Använd [API:et för webbsökning i Bing](../bing-web-search/search-the-web.md), [API:et för videosökning](../bing-video-search/search-the-web.md) och [API:et för bildsökning](../bing-image-search/overview.md) för andra typer av webbinnehåll.

## <a name="bing-news-search-api-features"></a>Funktioner i Bings API för nyhetssökning

API:et för nyhetssökning i Bing hittar och returnerar i första hand relevanta nyhetsartiklar, men har även flera funktioner för intelligent och fokuserad nyhetssökning på webben.

|Funktion  |Beskrivning  |
|---------|---------|
|[Föreslå och använda söktermer](concepts/search-for-news.md#suggest-and-use-search-terms)     | Förbättra sökupplevelsen med [API:et för automatiska förslag i Bing](../bing-autosuggest/get-suggested-search-terms.md). Då visas föreslagna sökord medan du skriver.         |
|[Få allmänna nyheter](concepts/search-for-news.md#get-general-news)     | Hitta nyheter med API:et för nyhetssökning i Bing genom att skicka sökfrågor till Bing och få tillbaka en lista med relevanta nyhetsartiklar.           |
|[Dagens viktigaste nyheter](concepts/search-for-news.md#get-todays-top-news)      | Få dagens viktigaste nyheter i samtliga kategorier.       |
|[Nyheter efter kategori](concepts/search-for-news.md)     | Sök efter nyheter i kategorier.        | 
|[Toppnyheter](concepts/search-for-news.md)     | Sök efter toppnyheter i samtliga kategorier.         |

## <a name="workflow"></a>Arbetsflöde

API:et för nyhetssökning i Bing är en RESTful-webbtjänst, vilket innebär att den är enkel att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON. Du kan använda tjänsten med hjälp av antingen REST API eller SDK.

1. Skapa ett Cognitive Services-API-konto med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto kostnadsfritt](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-news-api).

2. Skicka en begäran till API:et med en giltig sökfråga.

3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Prova först med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) av API:et för nyhetssökning i Bing. Den här demonstrationen visar hur du snabbt kan anpassa en sökfråga och hitta nyheter på webben.

Om du vill komma igång snabbt med en API-begäran kan du prova snabbstarten för [REST API](quickstart.md) eller någon [SDK](sdk.md).

## <a name="see-also"></a>Se även

* [API för nyhetssökning i Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices/bing-news-api-v7-reference)-referensavsnittet innehåller definitioner och information om slutpunkterna, rubrikerna, API-svaren och frågeparametrarna som du kan använda till att skicka begäran.

* I [användnings- och visningskraven för Bing](./useanddisplayrequirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.

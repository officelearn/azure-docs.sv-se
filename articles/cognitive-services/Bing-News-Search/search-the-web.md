---
title: Vad är API för nyhetssökning i Bing?
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder API för nyhetssökning i Bing för att söka på webben efter aktuella rubriker över kategorier, inklusive rubriker och trend ämnen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: overview
ms.date: 12/18/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 2f793c675b045f995d3f8b208e11b60bc2140ec9
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367156"
---
# <a name="what-is-the-bing-news-search-api"></a>Vad är API för nyhetssökning i Bing?

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](https://aka.ms/cogsvcs/bingmove).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](https://aka.ms/cogsvcs/bingmigration).

Med API för nyhetssökning i Bing är det enkelt att integrera Bings funktioner för kognitiv nyhetssökning i dina program. API:et fungerar ungefär som [Nyhetssökning i Bing](https://www.bing.com/news), där du kan söka efter och få relevanta nyhetsartiklar.

Observera att API:et för nyhetssökning i Bing endast returnerar nya sökresultat. Använd [API:et för webbsökning i Bing](../bing-web-search/overview.md), [API:et för videosökning](../bing-video-search/overview.md) och [API:et för bildsökning](../bing-image-search/overview.md) för andra typer av webbinnehåll.

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

1. Skapa ett [Cognitive Services-API-konto](../cognitive-services-apis-create-account.md) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du [skapa ett konto](https://azure.microsoft.com/free/cognitive-services/) kostnads fritt.
2. Skicka en begäran till API:et med en giltig sökfråga.
3. Bearbeta API-svaret genom att tolka det returnerade JSON-meddelandet.

## <a name="next-steps"></a>Nästa steg

Prova först med den [interaktiva demonstrationen](https://azure.microsoft.com/services/cognitive-services/bing-news-search-api/) av API:et för nyhetssökning i Bing. Den här demonstrationen visar hur du snabbt kan anpassa en sökfråga och hitta nyheter på webben.

Om du vill komma igång snabbt med en API-begäran kan du prova snabbstarten för [REST API](./csharp.md) eller någon [SDK](./quickstarts/client-libraries.md?pivots=programming-language-csharp).

## <a name="see-also"></a>Se även

* [API för nyhetssökning i Bing v7](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference)-referensavsnittet innehåller definitioner och information om slutpunkterna, rubrikerna, API-svaren och frågeparametrarna som du kan använda till att skicka begäran.
* I [användnings- och visningskraven för Bing](../bing-web-search/use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.
* Gå till [sidan Bing-sökning API Hub](../bing-web-search/overview.md) och utforska de andra tillgängliga API: erna.
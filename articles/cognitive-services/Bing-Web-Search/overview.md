---
title: Vad är API för webbsökning i Bing?
titleSuffix: Azure Cognitive Services
description: API:et för webbsökning i Bing är en RESTful-tjänst som ger svar direkt på användarförfrågningar. Det är enkelt att konfigurera sökresultaten så att de omfattar webbsidor, bilder, video, nyheter, översättningar med mera. Resultaten tillhandahålls som JSON och baserat på sökrelevans och prenumerationer för webbsökning i Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: overview
ms.date: 03/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: b6e546130efd52312fca62ab4e6a22626778a19e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60648164"
---
# <a name="what-is-the-bing-web-search-api"></a>Vad är API för webbsökning i Bing?

API:et för webbsökning i Bing är en RESTful-tjänst som ger svar direkt på användarförfrågningar. Det är enkelt att konfigurera sökresultaten så att de omfattar webbsidor, bilder, video, nyheter, översättningar med mera. Webbsökning i Bing tillhandahåller resultaten som JSON-baserade på sökrelevans och dina prenumerationer för webbsökning i Bing.

Det här API:et är optimalt för program som behöver åtkomst till allt innehåll som är relevant för en användares sökfråga. Om du skapar ett program som endast kräver en viss typ av resultat kan du överväga att använda [API för bildsökning i Bing](../Bing-Image-Search/overview.md), [API för videosökning i Bing](../Bing-Video-Search/search-the-web.md) eller [API för nyhetssökning i Bing](../Bing-News-Search/search-the-web.md). I listan med [API:er för Cognitive Services](https://docs.microsoft.com/azure/cognitive-services) ser du alla API: er för Bing-sökresultat.

Vill du se hur det fungerar? Prova vår demo med [API för webbsökning i Bing](https://azure.microsoft.com/services/cognitive-services/bing-web-search-api/).

## <a name="features"></a>Funktioner  

Webbsökning i Bing, inte bara att ge dig åtkomst till svar direkt. Den innehåller också ytterligare egenskaper och funktioner som gör att du kan anpassa sökresultaten för dina användare.

| Funktion | Beskrivning |
|---------|-------------|
| [Föreslå söktermer i realtid](../bing-autosuggest/get-suggested-search-terms.md) | Förbättra programupplevelsen API för automatiska förslag i Bing. Föreslagna sökord visas medan användaren skriver. |
| [Filtrera och begränsa resultat efter innehållstyp](filter-answers.md) | Anpassa och förfina sökresultaten med filter och frågeparametrar för webbsidor, bilder, video, säker sökning med mera. |
| [Träffmarkering för unicode-tecken](hit-highlighting.md) | Identifiera och ta bort oönskade unicode-tecken från sökresultaten innan de visas för användaren. |
| [Lokalisera sökresultat efter land, region och marknad](supported-countries-markets.md) | Webbsökningen i Bing stöder fler än tre dussin länder eller regioner. Med den här funktionen kan du förfina sökresultaten för varje land, region eller marknad. |
| [Analysera mätdata med Bing-statistik](bing-web-stats.md) | Bing-statistik är en betald prenumeration som tillhandahåller analys av samtalsvolym, toppfrågesträngar, geografisk fördelning med mera. |

## <a name="workflow"></a>Arbetsflöde

API:et för webbsökning i Bing är enkelt att anropa från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON-svaren. Tjänsten är tillgänglig med hjälp av [REST-API:et](quickstarts/python.md) och [SDK:erna för Bing-webbsökning](web-sdk-python-quickstart.md).

1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).  
2. Skicka en [begäran till API:et för webbsökning i Bing](quickstarts/python.md).
3. Tolka JSON-svar.

## <a name="next-steps"></a>Nästa steg

* Använd vår [Python-snabbstart](quickstarts/python.md) och gör ditt första anrop till API:et för webbsökning i Bing.  
* [Skapa en enkelsidig webbapp](tutorial-bing-web-search-single-page-app.md).
* Granska dokumentationen om [v7-referensen för API:et för webbsökning](https://docs.microsoft.com/rest/api/cognitiveservices/bing-web-api-v7-reference).  
* Läs mer om [kraven för användning och visning](UseAndDisplayRequirements.md) för webbsökning i Bing.  

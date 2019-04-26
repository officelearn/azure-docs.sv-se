---
title: Vad är API:et för sökning efter lokala företag i Bing? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: API:et för sökning efter lokala företag i Bing är en RESTful-tjänst som gör det möjligt för dina program att hitta information om lokala platser och företag utifrån sökkriterier.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: fb3b375a66ac9b05ad26287b7c39ce8e748ad699
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60336719"
---
# <a name="what-is-bing-local-business-search"></a>Vad är lokala företag sökning i Bing?
Den lokala företag i Bing är en RESTful-tjänst som gör att dina program att hitta information om lokala företag baserat på sökfrågor. Till exempel `q=<business-name> in Redmond, Washington`, eller `q=Italian restaurants near me`. 

## <a name="features"></a>Funktioner
| Funktion | Beskrivning |  
| -- | -- | 
| [Hitta lokala företag och platser](quickstarts/local-quickstart.md) | Den lokala företag i Bing hämtar lokaliserade resultat från en fråga. Resultatet innehåller en URL för företagets webbplats och visa text, telefonnummer och geografisk plats, inklusive: GPS-koordinater, stad, gatuadress |  
| [Lokala filterresultat med geografiska gränser](specify-geographic-search.md) | Lägg till koordinaterna som sökparametrar för att begränsa resultaten till ett visst geografiskt område, anges av en cirkulär området eller kvadrat angränsande ruta. | 
| [Filtrera lokala affärsresultat efter kategori](local-categories.md) | Sök efter lokal affärsresultat efter kategori. Det här alternativet använder omvänd IP-plats eller GPS-koordinater för anroparen för att returnera lokaliserade resultat i olika kategorier av verksamhet.|

## <a name="workflow"></a>Arbetsflöde
Anropa den lokala företag i Bing från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON-svaren. Den här tjänsten är tillgänglig med hjälp av REST-API.
 
1. Skapa en [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API: er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. URL: en koda sökorden för de `q=""` frågeparameter. Exempel: `q=nearby+restaurant` eller `q=nearby%20restaurant`. Ange sidbrytning också, om det behövs. 
3. Skicka en [begäran till den lokala företag i Bing](quickstarts/local-quickstart.md) 
4. Tolka JSON-svaret 

> [!NOTE]
> Lokala företag Sök stöder för närvarande endast den `en-US` marknaden. 
> [!NOTE]
> För närvarande stöder inte lokala företag Sök automatiska förslag. 

## <a name="next-steps"></a>Nästa steg
- [Frågor och svar](local-search-query-response.md)
- [Lokala företag Search-Snabbstart](quickstarts/local-quickstart.md)
- [Lokala företag Search API-referens](local-search-reference.md)
- [Användnings- och visningskrav](use-display-requirements.md)

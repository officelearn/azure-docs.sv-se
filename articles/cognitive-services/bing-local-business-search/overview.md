---
title: Vad är API:et för sökning efter lokala företag i Bing?
titleSuffix: Azure Cognitive Services
description: API:et för sökning efter lokala företag i Bing är en RESTful-tjänst som gör det möjligt för dina program att hitta information om lokala platser och företag utifrån sökkriterier.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 0006761126eb1d561da7eeff97e8a9928d62ddb0
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478931"
---
# <a name="what-is-bing-local-business-search"></a>Vad är lokal sökning i Bing Business?
API: t för lokal sökning i Bing är en RESTful-tjänst som gör det möjligt för dina program att hitta information om lokala företag baserat på Sök frågor. Till exempel `q=<business-name> in Redmond, Washington`, eller `q=Italian restaurants near me`. 

## <a name="features"></a>Funktioner
| Funktion | Beskrivning |  
| -- | -- | 
| [Hitta lokala företag och platser](quickstarts/local-quickstart.md) | API: t för lokal sökning i Bing får lokaliserade resultat från en fråga. Resultatet innehåller en URL för företagets webbplats och visnings text, telefonnummer och geografisk plats, inklusive: GPS-koordinater, stad, gatuadress |  
| [Filtrera lokala resultat med geografiska gränser](specify-geographic-search.md) | Lägg till koordinater som Sök parametrar för att begränsa resultaten till ett specifikt geografiskt område, angivet av antingen ett cirkulärt område eller kvadratisk begränsnings ruta. | 
| [Filtrera lokala affärs resultat efter kategori](local-categories.md) | Sök efter lokala affärs resultat efter kategori. Det här alternativet använder omvänd IP-plats eller GPS-koordinater för anroparen för att returnera lokaliserade resultat i olika verksamhets kategorier.|

## <a name="workflow"></a>Arbetsflöde
Anropa Bing-API: et för lokal sökning från alla programmeringsspråk som kan göra HTTP-begäranden och parsa JSON-svar. Den här tjänsten kan nås med hjälp av REST API.
 
1. Skapa ett [Cognitive Services-API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:er för Bing-sökresultat. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. URL koda dina Sök villkor för `q=""` Frågeparametern. Exempel: `q=nearby+restaurant` eller `q=nearby%20restaurant`. Ange även sid brytning, om det behövs. 
3. Skicka en [begäran till Bing-API för lokal affärs sökning](quickstarts/local-quickstart.md) 
4. Tolka JSON-svaret 

> [!NOTE]
> För närvarande kan du söka i lokalt företag: 
> * Stöder endast `en-US` marknaden. 
> * Stöder inte Automatiska förslag i Bing. 

## <a name="next-steps"></a>Nästa steg
- [Fråga och svar](local-search-query-response.md)
- [Snabb start för lokal affärs sökning](quickstarts/local-quickstart.md)
- [API-referens för sökning efter lokala företag](local-search-reference.md)
- [Använda och visa krav](use-display-requirements.md)

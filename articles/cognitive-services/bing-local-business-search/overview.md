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
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478931"
---
# <a name="what-is-bing-local-business-search"></a>Vad är Bing Local Business Search?
Api:et för lokala företag i Bing är en RESTful-tjänst som gör att dina program kan hitta information om lokala företag baserat på sökfrågor. Till exempel `q=<business-name> in Redmond, Washington`, `q=Italian restaurants near me`eller . 

## <a name="features"></a>Funktioner
| Funktion | Beskrivning |  
| -- | -- | 
| [Hitta lokala företag och platser](quickstarts/local-quickstart.md) | Api:et för lokala företagssökning i Bing får lokaliserade resultat från en fråga. Resultaten inkluderar en webbadress för företagets webbplats och visa text, telefonnummer och geografisk plats, inklusive: GPS-koordinater, stad, gatuadress |  
| [Filtrera lokala resultat med geografiska gränser](specify-geographic-search.md) | Lägg till koordinater som sökparametrar för att begränsa resultaten till ett visst geografiskt område, som anges av antingen ett cirkulärt område eller en fyrkantig begränsningsram. | 
| [Filtrera lokala affärsresultat efter kategori](local-categories.md) | Sök efter lokala företagsresultat efter kategori. Det här alternativet använder omvänd IP-plats eller GPS-koordinater för den som ringer för att returnera lokaliserade resultat i olika kategorier av företag.|

## <a name="workflow"></a>Arbetsflöde
Anropa API:et för lokal företagssökning på Bing från alla programmeringsspråk som kan göra HTTP-begäranden och tolka JSON-svar. Den här tjänsten är tillgänglig med REST API.
 
1. Skapa ett [Cognitive Services API-konto](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) med åtkomst till API:erna för Bing-sökning. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. URL-koda söktermerna `q=""` för frågeparametern. Exempel: `q=nearby+restaurant` eller `q=nearby%20restaurant`. Ställ sidnumrering också, om det behövs. 
3. Skicka en [begäran till API:et](quickstarts/local-quickstart.md) för sökning i Bing lokalt företag 
4. Tolka JSON-svaret 

> [!NOTE]
> För närvarande, Local Business Search: 
> * Stöder endast `en-US` marknaden. 
> * Stöder inte Automatiska Förslag på Bing. 

## <a name="next-steps"></a>Nästa steg
- [Fråga och svar](local-search-query-response.md)
- [Snabbstart för lokal företagssökning](quickstarts/local-quickstart.md)
- [API-referens för sökning efter lokala företag](local-search-reference.md)
- [Använda och visa krav](use-display-requirements.md)

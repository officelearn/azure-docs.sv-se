---
title: Vilka är Bing Search API:er?
titleSuffix: Azure Cognitive Services
description: Använd den här artikeln om du vill veta mer om API:erna för Bing-sökning och hur du kan aktivera kognitiva internetsökningar i dina appar och tjänster.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: aahi
ms.openlocfilehash: 82b1f13562a49284059c25bcbd39a33daf949dcc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74775548"
---
# <a name="what-are-the-bing-search-apis"></a>Vilka är Bing Search API:er?

Med API:er för Bing-sökning kan du skapa webbanslutna appar och tjänster som söker efter webbsidor, bilder, nyheter, platser med mera, utan annonser. Genom att skicka sökförfrågningar med hjälp av API:er för Bing Search REST eller SDK:er kan du få relevant information och innehåll för webbsökningar. Använd den här artikeln om du vill veta mer om de olika API:erna för Bing-sökning och hur du kan integrera kognitiva sökningar i dina program och tjänster. Pris- och prisgränser kan variera mellan API:er.

## <a name="the-bing-web-search-api"></a>API:et för webbsökning i Bing

[Api:et för webbsökning](../Bing-Web-Search/overview.md) på Bing returnerar webbsidor, bilder, video, nyheter med mera. Du kan filtrera sökfrågorna som skickas till det här API:et för att inkludera eller utesluta vissa innehållstyper.

Överväg att använda API:et för webbsökning på Bing i program som kan behöva söka efter alla typer av relevant webbinnehåll. Om ditt program söker efter en viss typ av onlineinnehåll bör du tänka på något av sök-API:erna nedan:

## <a name="content-specific-bing-search-apis"></a>Innehållsspecifika API:er för Bing-sökning

Följande Bing-sök-API:er returnerar specifikt innehåll från webben som bilder, nyheter, lokala företag och videor.

| Bing-API | Beskrivning |
| -- | -- |
| [Entitetssökning](../Bing-Entities-Search/overview.md) | API:et för sökning av Bing-enhet returnerar sökresultat som innehåller entiteter, som kan vara personer, platser eller saker. Beroende på frågan returnerar API:et en eller flera entiteter som uppfyller sökfrågan. Sökfrågan kan innehålla anmärkningsvärda personer, lokala företag, landmärken, destinationer med mera. |
| [Bildsökning](../Bing-Image-Search/overview.md) | Med API:et för bildsökning på Bing kan du söka efter och hitta statiska och animerade bilder av hög kvalitet som [liknar Bing.com/images](https://www.Bing.com/images). Du kan förfina sökningar för att inkludera eller utesluta bilder efter attribut, inklusive storlek, färg, licens och färskhet. Du kan också söka efter populära bilder, ladda upp bilder för att få insikter om dem och visa miniatyrförhandsvisningar. |
| [Nyhetssökning](../Bing-News-Search/search-the-web.md) | Med API:et för nyhetssökning på Bing kan du hitta nyhetsartiklar som liknar [Bing.com/news](https://www.Bing.com/news). API:et returnerar nyhetsartiklar från antingen flera källor eller specifika domäner. Du kan söka i olika kategorier för att få populära artiklar, toppartiklar och rubriker. |
| [Videosökning](../Bing-Video-Search/overview.md) | Med API:et för videosökning på Bing kan du hitta videor på webben. Få populära videor, relaterat innehåll och miniatyrförhandsgranskningar. |
| [Visuell sökning](../Bing-visual-search/overview.md) | Ladda upp en bild eller använd en webbadress för att få insiktsfull information om den, till exempel visuellt liknande produkter, bilder och relaterade sökningar. |
 [Sökning i lokalt företag](../bing-local-business-search/overview.md) | Med API:et för lokal företagssökning i Bing kan dina program hitta kontakt- och platsinformation om lokala företag baserat på sökfrågor. |

## <a name="the-bing-custom-search-api"></a>Api:et för anpassad sökning i Bing

Genom att skapa en anpassad sökinstans med API:et för anpassad sökning på [Bing](../Bing-Custom-Search/overview.md) kan du skapa en sökupplevelse som bara fokuserar på innehåll och ämnen som du bryr dig om. När du till exempel har angett de domäner, webbplatser och specifika webbsidor som Bing ska söka på, anpassar Bing Custom Search resultaten till det specifika innehållet. Du kan infoga API:erna för anpassade autosuggest, bild och videosökning för att ytterligare anpassa sökupplevelsen.

## <a name="additional-bing-search-apis"></a>Ytterligare API:er för Bing-sökning

Med följande Bing Search API:er kan du förbättra din sökupplevelse genom att kombinera dem med andra Bing-sök-API:er.

| API | Beskrivning |
| -- | -- |
| [Automatiska förslag i Bing](../Bing-Autosuggest/get-suggested-search-terms.md) | Förbättra programmets sökupplevelse med API:et för automatiska förslag på Bing genom att returnera föreslagna sökningar i realtid.  |
| [Bing Statistik](bing-web-stats.md) | Bing Statistics tillhandahåller analyser för Bing Search API:er som ditt program använder. Några av de tillgängliga analyserna inkluderar samtalsvolym, de vanligaste frågesträngarna och geografisk distribution. |

## <a name="next-steps"></a>Nästa steg

* [Prisinformation för](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) Bing Search API
* I [användnings- och visningskraven för Bing](./use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.

---
title: 'Vad är API: er för Bing Search?'
titleSuffix: Azure Cognitive Services
description: 'Använd den här artikeln om du vill veta mer om API: er för Bing Search och hur du kan aktivera kognitiva internet-sökningar i dina appar och tjänster.'
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: article
ms.date: 01/31/2019
ms.author: aahi
ms.openlocfilehash: b2277f540b076307fe74c57068ff288860f82796
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2019
ms.locfileid: "55513119"
---
# <a name="what-are-the-bing-search-apis"></a>Vad är API: er för Bing Search?

API: er för Bing Search kan du skapa webb-anslutna appar och tjänster som hittar webbsidor, bilder, nyheter, platser och mycket annat utan annonser. Genom att skicka sökförfrågningar med Bing Search REST API: er eller SDK: er, kan du få relevant information och innehåll för webbsökningar. Använd den här artikeln om du vill veta mer om de olika Bing search API: er och hur du kan integrera cognitive sökningar i dina program och tjänster. Gränser för frekvensen och priser kan variera mellan API: er.

## <a name="the-bing-web-search-api"></a>API för Bing-webbsökning

Den [webbsökning i Bing](../Bing-Web-Search/index.yml) Returnerar webbsidor, bilder, video, nyheter och mer. Sökfrågor som skickas till detta API kan filtreras för att inkludera eller exkludera vissa typer av innehåll.

Överväg att använda Bing Web Search API i program som kan behöva söka efter alla typer av relevanta webbinnehåll. Om programmet söker efter en viss typ av innehåll online, bör du något av Sök-API: erna nedan: 

## <a name="content-specific-bing-search-apis"></a>Innehållsspecifika Bing search API: er

I följande Bing search API: er returnera specifikt innehåll från webben, till exempel bilder, nyheter, lokala företag och videor.

| API för Bing | Beskrivning |
| -- | -- | 
| [Entitetssökning](../Bing-Entities-Search/index.yml) | Bing-Entitetssökning returnerar sökresultat som innehåller enheter som kan vara personer, platser eller saker. Beroende på frågan returnerar API: N en eller flera enheter som uppfyller sökfråga, vilket kan innefatta anmärkningsvärda enskilda användare, lokala företag, landmärken, mål och mycket mer. |
| [Bildsökning](../Bing-Image-Search/index.yml) | Sökning i Bing kan du söka efter och hitta högkvalitativa statiska och animerade bilder liknar [Bing.com/images](https://www.Bing.com/images). Du kan begränsa sökningar för att inkludera eller exkludera avbildningar av attribut, inklusive storlek, färg, licens och färskhet. Du kan också söka efter populära bilder, ladda upp bilder för att få insikter om dem och visa miniatyrer. |
| [API för nyhetssökning](../Bing-News-Search/index.yml) | Den nyhetssökning i Bing kan du hitta nyhetsartiklar liknar [Bing.com/news](https://www.Bing.com/news). API: et returnerar nyhetsartiklar från flera källor eller vissa domäner. Du kan söka i kategorier för att hämta populära artiklar, de viktigaste berättelserna och rubriker.
| [API för Videosökning](../Bing-Video-Search/index.yml) | Bing-Videosökning kan du hitta videor på webben. Hämta trendande videor, relaterat innehåll och miniatyrer. |
| [Visuell sökning](../Bing-visual-search/index.yml) | Ladda upp en bild eller använda en URL för att få insiktsfulla information om det, som visuellt liknande produkter, bilder och relaterade sökningar. |
 [Sök i lokala företag](../bing-local-business-search/index.yml) | API:et för sökning efter lokala företag i Bing gör det möjligt för dina program att hitta kontakt- och platsinformation om lokala företag utifrån sökkriterier. |

## <a name="the-bing-custom-search-api"></a>Anpassad sökning i Bing

Skapa en anpassad Sökinstans med den [Bing Custom Search](../Bing-Custom-Search/index.yml) API kan du skapa en sökfunktion som fokuserar på att endast innehåll och ämnen som intresserar dig. När du har angett i domäner, webbplatser och specifika webbsidor som söker i Bing, kommer till exempel resultat skräddarsys specifikt innehållet. Du kan lägga till den anpassade automatiska förslag i Bing, bild, och ta del av videon Search API: er för att ytterligare anpassa din sökning.  

## <a name="additional-bing-search-apis"></a>Ytterligare Bing Search API: er

Följande Bing Search API: erna kan du förbättra din sökning genom att kombinera dem med andra API: er för Bing search.

| API | Beskrivning |
| -- | -- | 
| [Automatiska förslag i Bing](../Bing-Autosuggest/index.yml) | Förbättra programmets sökupplevelse med den automatiska förslag i Bing genom att returnera föreslagna sökningar i realtid.  |
| [Bing statistik](bing-web-stats.md) | Bing statistik tillhandahåller analyser för Bing Search API: er används i ditt program. Några av de tillgängliga analyser är samtalsvolym, övre frågesträngar och geografisk fördelning. |

## <a name="next-steps"></a>Nästa steg

* API: et för Bing Search [prisinformation](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/)
* I [användnings- och visningskraven för Bing](./use-display-requirements.md) specificeras godtagbar användning för det innehåll och den information du får via API:erna för Bing-sökning.

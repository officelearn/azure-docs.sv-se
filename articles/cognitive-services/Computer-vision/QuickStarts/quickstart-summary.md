---
title: Snabbstartssammanfattning av API för visuellt innehåll
titleSuffix: Azure Cognitive Services
description: I de här snabbstarterna får du analysera en bild, skapa en miniatyrbild och extrahera tryckt och handskriven text med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: quickstart
ms.date: 08/28/2018
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 645c57c9bfeaefcfd2fc50b2dd431713fffaa42b
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2018
ms.locfileid: "53578792"
---
# <a name="quickstart-summary"></a>Snabbstart: Sammanfattning

Dessa snabbstarter innehåller information och kodexempel som hjälper dig att snabbt komma igång med tjänsten Visuellt innehåll.

Exemplen gör HTTP-direktanrop till API:et för visuellt innehåll. Se avsnittet *SDK-snabbstarter* för exempel på hur du använder klientbibliotek för Visuellt innehåll, som innehåller metoder för att innesluta HTTP-anrop.

Du kan snabbt experimentera med API:erna för visuellt innehåll genom att prova [Open API-testkonsolen](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

Du kan använda tjänsten Visuellt innehåll för att utföra följande uppgifter:

* Analysera en fjärrbild
* Analysera en lokal bild
* Identifiera kändisar och landmärken (domänmodeller)
* Generera en miniatyrbild
* Identifiera och extrahera tryckt text (OCR) från en bild
* Identifiera och extrahera handskriven text från en bild

Koden i de olika exemplen skiljer sig inte mycket. Dock framhäver de olika funktioner i Visuellt innehåll och olika tekniker för att utbyta data med tjänsten, till exempel:

* _Generate a thumbnail_ (Skapa en miniatyrbild) returnerar en bild som en bytematris i själva svarsinnehållet.
* _Analyze a local image_ (Analysera en lokal bild) kräver att bilden ingår i begäran som en bytematris.
* _Extract handwritten text_ (Extrahera handskriven text) kräver två anrop för att hämta texten.

## <a name="summary"></a>Sammanfattning

| Snabbstart               | Parametrar för begäran                          | Svar          |
| ------------------------ | ------------------------------------------- | ----------------  |
| Analysera en fjärrbild   | visualFeatures=Categories,Description,Color | JSON-sträng       |
| Analysera en lokal bild    | data=image_data (bytematris)                | JSON-sträng       |
| Identifiera kändisar       | model=celebrities                           | JSON-sträng       |
| Skapa en miniatyrbild     | width=200&height=150&smartCropping=true     | bytematris        |
| Extrahera tryckt text     | language=unk&detectOrientation=true         | JSON-sträng       |
| Extrahera handskriven text | handwriting=true                            | URL, JSON-sträng  |

## <a name="next-steps"></a>Nästa steg

Utforska API:erna för visuellt innehåll som används för att analysera en bild, identifiera kändisar och landmärken, skapa en miniatyrbild och extrahera tryckt och handskriven text.

> [!div class="nextstepaction"]
> [Utforska API:er för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

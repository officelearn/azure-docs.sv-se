---
title: Varumärkes identifiering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp som rör identifiering av märkes-/logo typer med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: b4bcd07cf7549029d09f5acd58fdf8f92fffed74
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2019
ms.locfileid: "68945289"
---
# <a name="detect-popular-brands-in-images"></a>Identifiera populära varumärken i bilder

Varumärkes identifiering är ett specialiserat läge för [objekt identifiering](concept-object-detection.md) som använder en databas med tusentals globala logo typer för att identifiera kommersiella varumärken i bilder eller video. Du kan använda den här funktionen för att till exempel identifiera vilka varumärken som är mest populära på sociala medier eller förekommer oftast i medieproduktplacering.

Tjänsten Visuellt innehåll identifierar om det finns varumärkes logo typer i en specifik bild. i så fall returneras märkes namnet, en förtroende poäng och koordinaterna för en avgränsnings ruta runt logo typen.

Den inbyggda logo typ databasen omfattar populära varumärken i konsument elektronik, kläder med mera. Om du upptäcker att det varumärke som du söker efter inte upptäcks av Visuellt innehålls tjänsten, kan du vara bättre på att skapa och träna din egen logo typ detektor med hjälp av tjänsten [Custom vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) .

## <a name="brand-detection-example"></a>Exempel på varumärkes identifiering

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när du identifierar varumärken i exempel bilderna.

![En röd skjorta med Microsofts etikett och logo typ](./Images/red-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":20,
         "y":97,
         "w":62,
         "h":52
      }
   }
]
```

I vissa fall hämtar varumärkes detektorn både logo typ bilden och det namnlösa varumärkes namnet som två separata logo typer.

![En grå sweatshirt med en Microsoft-etikett och logo typ på den](./Images/gray-shirt-logo.jpg)

```json
"brands":[  
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":106,
         "w":55,
         "h":46
      }
   },
   {  
      "name":"Microsoft",
      "rectangle":{  
         "x":58,
         "y":86,
         "w":202,
         "h":63
      }
   }
]
```

## <a name="use-the-api"></a>Använda API:et

Varumärkes identifierings funktionen är en del av API: et [analys av avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Brands` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"brands"` avsnittet.

* [Snabbstart: Analysera en avbildning (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)

---
title: Varumärkesidentifiering - Datorseende
titleSuffix: Azure Cognitive Services
description: I den här artikeln beskrivs ett specialiserat objektidentifieringsläge. varumärkes- och/eller logotypidentifiering med hjälp av API:et för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131737"
---
# <a name="detect-popular-brands-in-images"></a>Upptäck populära varumärken i bilder

Varumärkesidentifiering är ett specialiserat [objektidentifiering](concept-object-detection.md) som använder en databas med tusentals globala logotyper för att identifiera kommersiella varumärken i bilder eller video. Du kan använda den här funktionen för att till exempel identifiera vilka varumärken som är mest populära på sociala medier eller förekommer oftast i medieproduktplacering.

Tjänsten Computer Vision identifierar om det finns varumärkeslogotyper i en viss bild. I så fall returneras varumärket, en konfidenspoäng och koordinaterna för en markeringsram runt logotypen.

Den inbyggda logotypdatabasen täcker populära varumärken inom hemelektronik, kläder med mera. Om du upptäcker att varumärket du letar efter inte upptäcks av tjänsten Computer Vision kan du vara bättre betjänt av att skapa och träna din egen logotypdetektor med hjälp av custom [vision-tjänsten.](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/)

## <a name="brand-detection-example"></a>Exempel på varumärkesidentifiering

Följande JSON-svar illustrerar vad Datorseende returnerar när du upptäcker varumärken i exempelbilderna.

![En röd skjorta med microsoftetikett och logotyp på den](./Images/red-shirt-logo.jpg)

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

I vissa fall kommer varumärket detektorn plocka upp både logotypen bilden och stiliserade varumärket som två separata logotyper.

![En grå tröja med en Microsoft-etikett och logotyp på den](./Images/gray-shirt-logo.jpg)

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

Varumärkesidentifieringsfunktionen är en del av [Api:et för analyseravbildning.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Du kan anropa det här API:et via en inbyggd SDK eller via REST-anrop. Inkludera `Brands` i frågeparametern **visualFeatures.** Sedan, när du får hela JSON svar, helt enkelt tolka `"brands"` strängen för innehållet i avsnittet.

* [Snabbstart: Datorseende .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)

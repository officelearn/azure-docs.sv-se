---
title: Varumärkes identifiering – Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Den här artikeln beskriver ett specialiserat läge för objekt identifiering. identifiering av märke och/eller logo typ med hjälp av API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: pafarley
ms.openlocfilehash: 50e4fe1e2573c8566bbdf5697bb81b025a00935c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131737"
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

* [Snabb start: Visuellt innehåll .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabb start: analysera en avbildning (REST API)](./quickstarts/csharp-analyze.md)

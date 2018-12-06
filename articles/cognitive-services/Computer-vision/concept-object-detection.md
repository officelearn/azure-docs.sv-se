---
title: Objektidentifiering - visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för objektidentifiering med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: cgronlun
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: pafarley
ms.openlocfilehash: 7f0f86e783edb55bc3193df073c92c9523a90176
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976041"
---
# <a name="object-detection"></a>Objektidentifiering

Objektidentifiering liknar [taggning](concept-tagging-images.md), men API: et returnerar de omgivande koordinaterna för avgränsningsfält (i bildpunkter) för varje objekt hittades. Till exempel om en bild innehåller ett hund, cat och person, identifiera åtgärden visar en lista över dessa objekt tillsammans med deras koordinater i avbildningen. Du kan använda den här funktionen för att bearbeta relationerna mellan objekt i en bild. Du kan även se om det finns flera instanser av samma tagg i en bild.

Identifiera API: et gäller taggar baserat på antalet objekt eller en levande saker som identifierats i avbildningen. Observera att nu finns det ingen formell relation mellan taxonomi för taggning och taxonomi som används för objektidentifiering av. På en konceptuell nivå hittar API: et identifiera endast objekt och levande saker, medan tagg-API kan även inkludera sammanhangsberoende termer som ”inom”, som inte går att lokalisera med avgränsar rutorna.

## <a name="object-detection-example"></a>Exempel för identifiering av objekt

Följande JSON-svar visar vad för visuellt innehåll returnerar när du söker efter objekt i exempelbild.

![En kvinna som använder en Microsoft Surface-enhet i en se](./Images/windows-kitchen.jpg)

```json
{
   "objects":[
      {
         "rectangle":{
            "x":730,
            "y":66,
            "w":135,
            "h":85
         },
         "object":"kitchen appliance",
         "confidence":0.501
      },
      {
         "rectangle":{
            "x":523,
            "y":377,
            "w":185,
            "h":46
         },
         "object":"computer keyboard",
         "confidence":0.51
      },
      {
         "rectangle":{
            "x":471,
            "y":218,
            "w":289,
            "h":226
         },
         "object":"Laptop",
         "confidence":0.85,
         "parent":{
            "object":"computer",
            "confidence":0.851
         }
      },
      {
         "rectangle":{
            "x":654,
            "y":0,
            "w":584,
            "h":473
         },
         "object":"person",
         "confidence":0.855
      }
   ],
   "requestId":"a7fde8fd-cc18-4f5f-99d3-897dcd07b308",
   "metadata":{
      "width":1260,
      "height":473,
      "format":"Jpeg"
   }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [kategorisera bilder](concept-categorizing-images.md) och [som beskriver avbildningar](concept-describing-images.md).
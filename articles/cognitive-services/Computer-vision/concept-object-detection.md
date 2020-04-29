---
title: Objekt identifiering-Visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig begrepp som rör funktionen för objekt identifiering i API för visuellt innehåll användning och begränsningar.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 3957e15a09bd7e7ecd814d169451af3241108b64
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80131663"
---
# <a name="detect-common-objects-in-images"></a>Identifiera gemensamma objekt i avbildningar

Objekt identifiering liknar [taggning](concept-tagging-images.md), men API: et returnerar koordinaterna för avgränsnings rutan (i bild punkter) för varje objekt som hittas. Om en bild exempelvis innehåller en hund, en katt och en person, kommer identifieringsåtgärden visa en lista över dessa objekt tillsammans med deras koordinater i bilden. Du kan använda den här funktionen för att bearbeta relationerna mellan objekten i en bild. Du kan också avgöra om det finns flera instanser av samma tagg i en bild.

Identifierings-API: et använder taggar baserat på objekt eller levnads saker som identifieras i avbildningen. Det finns för närvarande ingen formell relation mellan taggarna taxonomi och objekt identifiering. Identifierings-API: et söker bara efter objekt och levnads saker, medan tagg-API: et även kan innehålla sammanhangsbaserade termer som "inomhus", som inte kan lokaliseras med markerings rutor.

## <a name="object-detection-example"></a>Exempel på objekt identifiering

Följande JSON-svar illustrerar vad Visuellt innehåll returnerar när objekt identifieras i exempel bilden.

![En kvinna som använder en Microsoft-funktionsyta i en kök](./Images/windows-kitchen.jpg)

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

## <a name="limitations"></a>Begränsningar

Det är viktigt att notera begränsningarna för objekt identifiering så att du kan undvika eller minimera effekterna av falska negativa objekt (missade objekt) och begränsad information.

* Objekt identifieras normalt inte om de är små (mindre än 5% av bilden).
* Objekt identifieras vanligt vis inte om de ordnas nära varandra (en trave med plåtar, till exempel).
* Objekt särskiljs inte av varumärke-eller produkt namn (olika typer av sodas på en butiks hylla, till exempel). Du kan dock få varumärkes information från en avbildning med hjälp av funktionen [varumärkes identifiering](concept-brand-detection.md) .

## <a name="use-the-api"></a>Använda API:et

Funktionen objekt identifiering är en del av API: et [analys av avbildning](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Du kan anropa API: et via en inbyggd SDK eller via REST-anrop. Inkludera `Objects` i **visualFeatures** -Frågeparametern. När du sedan får det fullständiga JSON-svaret ska du bara parsa strängen för innehållet i `"objects"` avsnittet.

* [Snabb start: Visuellt innehåll .NET SDK](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
* [Snabb start: analysera en avbildning (REST API)](./quickstarts/csharp-analyze.md)
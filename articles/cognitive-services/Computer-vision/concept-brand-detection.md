---
title: Varumärke identifiering – visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Begrepp för varumärke/logotypen identifiering med hjälp av den API för visuellt innehåll.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: conceptual
ms.date: 01/26/2019
ms.author: pafarley
ms.openlocfilehash: a5cf4a2b0f5e2e2c83e9fe6aa9237c943fa23ed9
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55864816"
---
# <a name="brand-detection"></a>Varumärke identifiering

Varumärke identifiering är ett specialläge av [objekt identifiering](concept-object-detection.md) som använder en databas med tusentals olika globala logotyper för att identifiera varumärken i en video. Du kan använda den här funktionen, till exempel att identifiera vilka varumärken är mest populära i sociala medier eller mycket i media produktplacering.

Visuellt tjänsten identifierar om det finns varumärke logotyper i en viss avbildning; i så fall returnerar den namnet och ett förtroenderesultat koordinaterna för en avgränsningsruta runt logotypen.

Inbyggda logotyp databas täcker populära varumärken i consumer electronics och kläder. Om du upptäcker att varumärket du letar efter inte har identifierats av tjänsten för visuellt innehåll, du kan betjänas bättre skapa och träna din egen logotyp detektor med hjälp av den [Custom Vision](https://docs.microsoft.com/azure/cognitive-services/Custom-Vision-Service/) service.

## <a name="brand-detection-example"></a>Varumärke identifiering exempel

Följande JSON-svar visar vad för visuellt innehåll returnerar när du söker efter varumärken i avbildningarna som exempel.

![En grå sweatshirt med ett Microsoft-etiketten och en logotyp på den](./Images/gray-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.706,
         "rectangle":{
            "x":470,
            "y":862,
            "w":338,
            "h":327
         }
      }
   ],
   "requestId":"5fda6b40-3f60-4584-bf23-911a0042aa13",
   "metadata":{
      "width":2286,
      "height":1715,
      "format":"Jpeg"
   }
}
```
I vissa fall tar varumärke detektor över både fyrkantig logotyp och snygg namnet som två separata logotyper.

![En red shirt med ett Microsoft-etiketten och en logotyp på den](./Images/red-shirt-logo.jpg)

```json
{
   "brands":[
      {
         "name":"Microsoft",
         "confidence":0.657,
         "rectangle":{
            "x":436,
            "y":473,
            "w":568,
            "h":267
         }
      },
      {
         "name":"Microsoft",
         "confidence":0.85,
         "rectangle":{
            "x":101,
            "y":561,
            "w":273,
            "h":263
         }
      }
   ],
   "requestId":"10dcd2d6-0cf6-4a5e-9733-dc2e4b08ac8d",
   "metadata":{
      "width":1286,
      "height":1715,
      "format":"Jpeg"
   }
}
```

## <a name="use-the-api"></a>Använda API: et
Funktionen för identifiering av varumärke är en del av den [analysera bild](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) API. Du kan anropa detta API via en intern SDK eller REST-anrop. När du får det fullständiga JSON-svaret helt enkelt parsa strängen för innehållet i den `"brands"` avsnittet.

* [Snabbstart: Analysera en bild (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
* [Snabbstart: Analysera en bild (REST API)](./quickstarts/csharp-analyze.md)
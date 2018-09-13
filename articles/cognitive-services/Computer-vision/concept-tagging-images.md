---
title: Tagga bilder
titleSuffix: Computer Vision - Cognitive Services - Azure
description: Begrepp för att tagga bilder med hjälp av visuellt innehåll i Azure Cognitive Services.
services: cognitive-services
author: deken
manager: nolachar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/29/2018
ms.author: v-deken
ms.openlocfilehash: b06265bbdd5ba642c5395823e98a6a76171baff4
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44725536"
---
# <a name="tagging-images"></a>Tagga bilder

Visuellt innehåll returnerar taggar som baseras på över 2 000 identifierbara objekt, levande varelser, landskap och åtgärder. När taggar är tvetydigt eller inte är allmän kännedom ger API-svaren ”tips” för att tydliggöra taggarnas i samband med en känd inställning betydelse. Taggar är inte ordnade som en taxonomi och inga arvshierarkier finns. En samling innehåll taggar utgör grunden för en avbildning ”beskrivning” visas som mänskliga läsbara språk som är formaterade i fullständiga meningar. Observera att nu engelska är det enda språket som stöds för Bildbeskrivning.

När du laddar upp en bild eller ange en bild-URL, utdata visuellt algoritmer taggar baserat på de objekt, levande varelser och åtgärder som identifierats i avbildningen. Taggar är inte begränsat till huvudsakliga ämnet, till exempel en person i förgrunden, men innehåller även inställningen (inom eller utomhus), möbler, verktyg, anläggningar, djur, Tillbehör, Tillbehör osv.

## <a name="image-tagging-example"></a>Bild taggning exempel

Följande JSON-svar visar vad för visuellt innehåll returnerar när taggning visuella funktioner som har identifierats i exempelbild.

![House_Yard](./Images/house_yard.png).

```json
{
    "tags": [
        {
            "name": "grass",
            "confidence": 0.9999995231628418
        },
        {
            "name": "outdoor",
            "confidence": 0.99992108345031738
        },
        {
            "name": "house",
            "confidence": 0.99685388803482056
        },
        {
            "name": "sky",
            "confidence": 0.99532157182693481
        },
        {
            "name": "building",
            "confidence": 0.99436837434768677
        },
        {
            "name": "tree",
            "confidence": 0.98880356550216675
        },
        {
            "name": "lawn",
            "confidence": 0.788884699344635
        },
        {
            "name": "green",
            "confidence": 0.71250593662261963
        },
        {
            "name": "residential",
            "confidence": 0.70859086513519287
        },
        {
            "name": "grassy",
            "confidence": 0.46624681353569031
        }
    ],
    "requestId": "06f39352-e445-42dc-96fb-0a1288ad9cf1",
    "metadata": {
        "height": 200,
        "width": 300,
        "format": "Jpeg"
    }
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp [kategorisera bilder](concept-categorizing-images.md) och [som beskriver avbildningar](concept-describing-images.md).
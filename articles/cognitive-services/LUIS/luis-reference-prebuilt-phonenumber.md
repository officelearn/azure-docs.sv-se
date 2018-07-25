---
title: LUIS förskapade entiteter phone nummer referens – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller fördefinierade entitetsinformation om telefonnummer i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: 1ae14f72f0dc610b9399e675f49ef5fff51a6965
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/25/2018
ms.locfileid: "39238999"
---
# <a name="phonenumber-entity"></a>Entitet för telefonnummer
Den `phonenumber` entiteten extraherar en mängd olika telefonnummer inklusive landskoden. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden i programmet. Den `phonenumber` entitet stöds i `en-us` kultur endast. 

## <a name="types-of-phonenumber"></a>Typer av telefonnummer
PhoneNumber hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) Github-lagringsplats

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Lösning för fördefinierade phonenumber entitet
I följande exempel visas av lösningen på den **builtin.phonenumber** entitet.

```JSON
{
  "query": "my mobile is 00 44 161 1234567",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.8448457
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.8448457
    }
  ],
  "entities": [
    {
      "entity": "00 44 161 1234567",
      "type": "builtin.phonenumber",
      "startIndex": 13,
      "endIndex": 29,
      "resolution": {
        "value": "00 44 161 1234567"
      }
    }
  ]
}
```


## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [procent](luis-reference-prebuilt-percentage.md), [nummer](luis-reference-prebuilt-number.md), och [temperatur](luis-reference-prebuilt-temperature.md) entiteter. 
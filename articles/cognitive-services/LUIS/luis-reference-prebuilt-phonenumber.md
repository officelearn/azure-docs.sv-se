---
title: LUIS förskapade entiteter phone nummer referens – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller fördefinierade entitetsinformation om telefonnummer i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 06/20/2018
ms.author: diberry
ms.openlocfilehash: bacb09b50c4b1d82daa6be1ef4fc79c88269cf7a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035461"
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
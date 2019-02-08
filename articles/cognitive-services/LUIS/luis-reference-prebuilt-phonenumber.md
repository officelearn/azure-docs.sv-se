---
title: Phone antal fördefinierade entiteter
titleSuffix: Azure
description: Den här artikeln innehåller fördefinierade entitetsinformation om telefonnummer i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/27/2018
ms.author: diberry
ms.openlocfilehash: fa2163b37bda5beba7c36bfdff0778c4062c5546
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863423"
---
# <a name="phonenumber-prebuilt-entity-for-a-luis-app"></a>PhoneNumber fördefinierade entitet för en LUIS-app
Den `phonenumber` entiteten extraherar en mängd olika telefonnummer inklusive landskoden. Eftersom den här entiteten har redan tränats, behöver du inte lägga till exempel yttranden i programmet. Den `phonenumber` entitet stöds i `en-us` kultur endast. 

## <a name="types-of-phonenumber"></a>Typer av telefonnummer
PhoneNumber hanteras från den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text/blob/master/Patterns/Base-PhoneNumbers.yaml) GitHub-lagringsplats

## <a name="resolution-for-prebuilt-phonenumber-entity"></a>Lösning för fördefinierade phonenumber entitet
I följande exempel visas av lösningen på den **builtin.phonenumber** entitet.

```json
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

---
title: Keyphrase fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller keyphrase fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: a2bb486de0999ad61cf2fdfdfb57dd91d376f597
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55206605"
---
# <a name="keyphrase-prebuilt-entity-for-a-luis-app"></a>keyPhrase fördefinierade entitet för en LUIS-app
keyPhrase extraherar massor av nyckelfraser från ett uttryck. Du behöver inte lägga till exempel yttranden som innehåller keyPhrase till programmet. keyPhrase entitet stöds i [många kulturer](luis-language-support.md#languages-supported) som en del av den [textanalys](../text-analytics/overview.md) funktioner. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Lösning för fördefinierade keyPhrase entitet
I följande exempel visas av lösningen på den **builtin.keyPhrase** entitet.

```json
{
  "query": "where is the educational requirements form for the development and engineering group",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.182757929
  },
  "entities": [
    {
      "entity": "development",
      "type": "builtin.keyPhrase",
      "startIndex": 51,
      "endIndex": 61
    },
    {
      "entity": "educational requirements",
      "type": "builtin.keyPhrase",
      "startIndex": 13,
      "endIndex": 36
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om den [procent](luis-reference-prebuilt-percentage.md), [nummer](luis-reference-prebuilt-number.md), och [ålder](luis-reference-prebuilt-age.md) entiteter.

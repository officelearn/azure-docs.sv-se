---
title: Keyphrase fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller keyphrase fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/28/2019
ms.author: diberry
ms.openlocfilehash: 5ef7ccb58533161d8397ad42e70de1999908dc36
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473271"
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

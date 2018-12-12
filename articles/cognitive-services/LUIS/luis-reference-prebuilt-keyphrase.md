---
title: Keyphrase fördefinierade entitet
titleSuffix: Azure
description: Den här artikeln innehåller keyphrase fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: fb23c136a24e0c09705061b8893b532a21c1df74
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081807"
---
# <a name="keyphrase-entity"></a>Entitet för keyPhrase
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

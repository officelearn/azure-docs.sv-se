---
title: LUIS fördefinierade keyphrase entitetsreferenser – Azure | Microsoft Docs
titleSuffix: Azure
description: Den här artikeln innehåller keyphrase fördefinierade entitetsinformation i Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/09/2018
ms.author: diberry
ms.openlocfilehash: 4133b7c7c3fabbe92a3208c567d7b4c6c2c27283
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434406"
---
# <a name="keyphrase-entity"></a>Entitet för keyPhrase
keyPhrase extraherar massor av nyckelfraser från ett uttryck. Du behöver inte lägga till exempel yttranden som innehåller keyPhrase till programmet. keyPhrase entitet stöds i [många kulturer](luis-language-support.md#languages-supported) som en del av den [textanalys](../text-analytics/overview.md) funktioner. 

## <a name="resolution-for-prebuilt-keyphrase-entity"></a>Lösning för fördefinierade keyPhrase entitet
I följande exempel visas av lösningen på den **builtin.keyPhrase** entitet.

```JSON
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

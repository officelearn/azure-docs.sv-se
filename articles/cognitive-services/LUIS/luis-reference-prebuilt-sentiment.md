---
title: Sentiment-analys – LUIS
titleSuffix: Azure Cognitive Services
description: Om sentiment-analysen har kon figurer ATS innehåller LUIS JSON-svaret sentiment analys.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: dee07859da97f76d9a2db3ccbb71ae74d8ff4846
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "78270426"
---
# <a name="sentiment-analysis"></a>Sentimentanalys
Om sentiment-analysen har kon figurer ATS innehåller LUIS JSON-svaret sentiment analys. Läs mer om sentiment-analys i [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) -dokumentationen.


## <a name="resolution-for-sentiment"></a>Lösning för sentiment

Sentiment-data är ett resultat mellan 1 och 0 som anger positiv (närmare 1) eller negativ (närmare 0) sentiment av data.

#### <a name="english-language"></a>[Engelskt språk](#tab/english)

När kulturen är `en-us`är svaret:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Andra språk](#tab/other-languages)

För alla andra kulturer är svaret:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).


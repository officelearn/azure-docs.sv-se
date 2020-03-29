---
title: Sentimentanalys - LUIS
titleSuffix: Azure Cognitive Services
description: Om Sentiment-analys har konfigurerats innehåller LUIS-json-svaret sentimentanalys.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78270426"
---
# <a name="sentiment-analysis"></a>Sentimentanalys
Om Sentiment-analys har konfigurerats innehåller LUIS-json-svaret sentimentanalys. Läs mer om sentimentanalys i [dokumentationen till Text Analytics.](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)


## <a name="resolution-for-sentiment"></a>Lösning för sentiment

Sentimentdata är en poäng mellan 1 och 0 som anger den positiva (närmare 1) eller negativa (närmare 0) sentimentet för data.

#### <a name="english-language"></a>[Engelska språket](#tab/english)

När kulturen `en-us`är är svaret:

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

Läs mer om [slutpunkten för V3-förutsägelse](luis-migration-api-v3.md).


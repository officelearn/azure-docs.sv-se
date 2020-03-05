---
title: Sentiment-analys – LUIS
titleSuffix: Azure Cognitive Services
description: Om attitydanalys är konfigurerad, innehåller json-svar LUIS attitydanalys.
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
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270426"
---
# <a name="sentiment-analysis"></a>Attitydanalys
Om attitydanalys är konfigurerad, innehåller json-svar LUIS attitydanalys. Läs mer om sentiment-analys i [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) -dokumentationen.


## <a name="resolution-for-sentiment"></a>Lösning för sentiment

Åsiktsdata är ett värde mellan 1 och 0 som anger vilka positiva (närmare 1) eller ett negativt (närmare 0) känsla av data.

#### <a name="english-language"></a>[Engelskt språk](#tab/english)

När kulturen är `en-us`är svaret:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languages"></a>[Andra språk](#tab/other-languages)

För alla andra kulturer och är svaret:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * *

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).


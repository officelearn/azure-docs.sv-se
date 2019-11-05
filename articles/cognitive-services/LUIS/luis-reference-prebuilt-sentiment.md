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
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: diberry
ms.openlocfilehash: a91246e4a4b3dc98bf11d2b014478fd40252f14b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73508027"
---
# <a name="sentiment-analysis"></a>Sentimentanalys
Om sentiment-analysen har kon figurer ATS innehåller LUIS JSON-svaret sentiment analys. Läs mer om sentiment-analys i [textanalys](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) -dokumentationen.


## <a name="resolution-for-sentiment"></a>Lösning för sentiment

Sentiment-data är ett resultat mellan 1 och 0 som anger positiv (närmare 1) eller negativ (närmare 0) sentiment av data.

#### <a name="english-languagetabenglish"></a>[Engelskt språk](#tab/english)

När kulturen är `en-us`är svaret:

```JSON
"sentimentAnalysis": {
  "label": "positive",
  "score": 0.9163064
}
```

#### <a name="other-languagestabother-languages"></a>[Andra språk](#tab/other-languages)

För alla andra kulturer är svaret:

```JSON
"sentimentAnalysis": {
  "score": 0.9163064
}
```
* * * 

## <a name="next-steps"></a>Nästa steg

Läs mer om [v3 förutsägelse slut punkten](luis-migration-api-v3.md).


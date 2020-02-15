---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: e879afdbd4c34e9d74405644de86421fb2cbab46
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2020
ms.locfileid: "77279597"
---
Exemplet yttranden följer ett speciellt format.

Fältet `text` innehåller texten för exempelyttrandet. Fältet `intentName` måste motsvara namnet på en befintlig avsikt i LUIS-appen. Fältet `entityLabels` är obligatoriskt. Om du inte vill märka ut några entiteter kan du ange en tom matris.

Om matrisen entityLabels inte är tom måste `startCharIndex` och `endCharIndex` märka ut den entitet som anges i fältet `entityName`. Indexet är nollbaserat, vilket innebär att 6 i det översta exemplet avser ”S” i Seattle och inte blanksteget innan stora S. Om du börjar eller slutar etiketten vid ett blanksteg i texten misslyckas API-anropet om att lägga till yttrandet.

```JSON
[
  {
    "text": "go to Seattle today",
    "intentName": "BookFlight",
    "entityLabels": [
      {
        "entityName": "Location::LocationTo",
        "startCharIndex": 6,
        "endCharIndex": 12
      }
    ]
  },
  {
    "text": "purple dogs are difficult to work with",
    "intentName": "BookFlight",
    "entityLabels": []
  }
]
```

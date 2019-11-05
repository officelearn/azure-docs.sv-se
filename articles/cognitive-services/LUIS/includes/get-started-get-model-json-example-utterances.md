---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 10/19/2019
ms.author: diberry
ms.openlocfilehash: ba400beb13d0bd4b29eee0c60617ec7ad128dd77
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73503740"
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

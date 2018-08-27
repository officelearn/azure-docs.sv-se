---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: include
ms.custom: include file
ms.date: 08/16/2018
ms.author: diberry
ms.openlocfilehash: 419f15901b665b43b850922f77bd32d7aac8d3a2
ms.sourcegitcommit: ebb460ed4f1331feb56052ea84509c2d5e9bd65c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/24/2018
ms.locfileid: "42920430"
---
Yttranden-exempelfil **utterances.json**, följer ett visst format. 

Den `text` fältet innehåller texten i exempel-uttryck. Den `intentName` fält måste motsvara namnet på en befintlig avsikt i LUIS-app. Fältet `entityLabels` är obligatoriskt. Om du inte vill att märka alla entiteter kan du ange en tom matris.

Om matrisen entityLabels inte är tom, den `startCharIndex` och `endCharIndex` måste du markera den entitet som anges i den `entityName` fält. Indexet är nollbaserat, vilket innebär att 6 i den övre exemplet refererar till ”S” Seattle och inte blanksteget innan kapital S. Om du börja eller sluta etikett på ett blanksteg i texten, misslyckas API-anrop för att lägga till talade.

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
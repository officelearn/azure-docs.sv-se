---
title: ta med fil
description: ta med fil
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.date: 05/18/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: diberry
ms.openlocfilehash: acf3968510bc45838f26c4b3cf366abdee06f298
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655490"
---
Exemplet yttranden följer ett speciellt format.

Fältet `text` innehåller texten för exempelyttrandet. Fältet `intentName` måste motsvara namnet på en befintlig avsikt i LUIS-appen. Fältet `entityLabels` är obligatoriskt. Om du inte vill märka ut några entiteter kan du ange en tom matris.

Om matrisen entityLabels inte är tom måste `startCharIndex` och `endCharIndex` märka ut den entitet som anges i fältet `entityName`. Indexet är noll-baserat. Om du börjar eller slutar etiketten på ett blank steg i texten, Miss lyckas API-anropet för att lägga till yttranden.

```JSON
[
    {
        "text": "order a cheese pizza",
        "intentName": "ModifyOrder",
        "entityLabels":[]
    },
    {
        "text": "order a large pepperoni pizza",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 8,
                "endCharIndex": 28
            }
        ]
    },
    {
        "text": "order 2 large pepperoni pizzas on thin crust",
        "intentName": "ModifyOrder",
        "entityLabels": [
            {
                "entityName": "Order",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entityName": "FullPizzaWithModifiers",
                "startCharIndex": 6,
                "endCharIndex": 61
            },
            {
                "entity": "Quantity",
                "startPos": 6,
                "endPos": 7
            },
            {
                "entity": "PizzaType",
                "startPos": 14,
                "endPos": 22
            },
            {
                "entity": "Size",
                "startPos": 8,
                "endPos": 12
            },
            {
                "entity": "Crust",
                "startPos": 34,
                "endPos": 37
            }
        ]
    }
]
```

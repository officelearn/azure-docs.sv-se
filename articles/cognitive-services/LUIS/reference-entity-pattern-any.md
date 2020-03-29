---
title: Pattern.any entitetstyp - LUIS
titleSuffix: Azure Cognitive Services
description: Pattern.any är en platshållare med variabel längd som endast används i ett mönsters malluttryck för att markera var entiteten börjar och slutar.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 5164bf55ef8233cf34a470524da3bc852678d79a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "75979162"
---
# <a name="patternany-entity"></a>Entiteten Pattern.any

Pattern.any är en platshållare med variabel längd som endast används i ett mönsters malluttryck för att markera var entiteten börjar och slutar.  

Pattern.any entiteter måste markeras i exemplen [mönstermall,](luis-how-to-model-intent-pattern.md) inte avsiktsanvändarexemplen.

**Entiteten passar bra när:**

* Slutet på entiteten kan förväxlas med den återstående texten i uttrycket.

## <a name="usage"></a>Användning

Med tanke på ett klientprogram som söker efter böcker baserat på titel, extraherar pattern.any hela titeln. En mall yttrande med pattern.any för `Was {BookTitle} written by an American this year[?]`den här boken sökning är .

I följande tabell har varje rad två versioner av uttrycket. Det översta uttrycket är hur LUIS från början ser uttrycket. Det är inte klart var bokens titel börjar och slutar. Den nedre uttrycket använder en Pattern.any-entitet för att markera början och slutet av entiteten.

|Yttrande med entitet i fetstil|
|--|
|`Was The Man Who Mistook His Wife for a Hat and Other Clinical Tales written by an American this year?`<br><br>Var **mannen som misstog sin fru för en hatt och andra kliniska tales** skriven av en amerikan i år?|
|`Was Half Asleep in Frog Pajamas written by an American this year?`<br><br>Var **Half Asleep i Frog Pyjamas** skriven av en amerikan i år?|
|`Was The Particular Sadness of Lemon Cake: A Novel written by an American this year?`<br><br>Var **den särskilda sorg lemon cake: en roman** skriven av en amerikansk i år?|
|`Was There's A Wocket In My Pocket! written by an American this year?`<br><br>Var **det en Wocket i fickan!** skriven av en amerikan i år?|
||



## <a name="example-json"></a>Exempel på JSON

Överväg följande fråga:

`where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

Med det inbäddade formulärnamnet som ska extraheras som ett Pattern.any:

`Understand your responsibilities as a member of the community`

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

```JSON
"entities": [
  {
    "entity": "understand your responsibilities as a member of the community",
    "type": "FormName",
    "startIndex": 18,
    "endIndex": 78,
    "role": ""
  }
```


#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)

Detta är JSON `verbose=false` om anges i frågesträngen:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ]
}
```

Detta är JSON `verbose=true` om anges i frågesträngen:

```json
"entities": {
    "FormName": [
        "Understand your responsibilities as a member of the community"
    ],
    "$instance": {
        "FormName": [
            {
                "type": "FormName",
                "text": "Understand your responsibilities as a member of the community",
                "startIndex": 18,
                "length": 61,
                "modelTypeId": 7,
                "modelType": "Pattern.Any Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * *

## <a name="next-steps"></a>Nästa steg

I den här [självstudien](luis-tutorial-pattern.md)använder du **pattern.any-entiteten** för att extrahera data från yttranden där yttrandena är väl formaterade och där slutet av data lätt kan förväxlas med de återstående orden i uttrycket.

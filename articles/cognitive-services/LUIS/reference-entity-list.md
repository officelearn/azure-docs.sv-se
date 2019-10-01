---
title: Lista enhets typ – LUIS
titleSuffix: Azure Cognitive Services
description: Lista entiteter representerar en fast, avslutad uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listan över entiteter. Använd rekommendations funktionen om du vill se förslag på nya ord baserade på den aktuella listan.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 09/29/2019
ms.author: diberry
ms.openlocfilehash: 1757faf8ab2be0b62956b6939ee068929f9275a4
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695243"
---
# <a name="list-entity"></a>Lista entitet 

Lista entiteter representerar en fast, avslutad uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listan över entiteter. Använd den **rekommenderar** funktionen för att se förslag för nya ord baserat på den aktuella listan. Om det finns mer än en entitet i listan med samma värde, returneras varje entitet i frågan slutpunkt. 

En lista entitet har inte registrerats på datorn. Det är en exakt denna matchning. LUIS markerar alla motsvarar ett objekt i en lista som en entitet i svaret. 

**Entiteten passar bra när text data:**

* Är en känd uppsättning.
* Ändras inte ofta. Om du behöver ändra listan ofta eller vill att listan ska expanderas själv, är en enkel entitet som ökar med en fras lista ett bättre alternativ. 
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i yttrandet stämmer exakt med en synonym eller det kanoniska namnet. LUIS använder inte listan för något mer än exakta textmatchningar. Fuzzy Matching, SKIFT läges okänslighet, igenkänning, plural och andra variationer löses inte med en List-entitet. Om du vill hantera variationer bör du överväga att använda ett [mönster](luis-concept-patterns.md#syntax-to-mark-optional-text-in-a-template-utterance) med valfri textsyntax.

![lista entitet](./media/luis-concept-entities/list-entity.png)

## <a name="example-json"></a>Exempel på JSON

Anta att appen har en lista som heter `Cities`, så att variationer av stadsnamn inklusive stad flygplats (Sea tac), flygplatsen (SEA), postnummer (98101), och phone riktnummer (206).

|Listobjekt|Objektet synonymer|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

I den föregående uttryck ordet `paris` mappas till paris-objektet som en del av den `Cities` lista entitet. Entiteten listan matchar både objektets normaliserade namn samt synonymer för objektet.

#### <a name="v2-prediction-endpoint-responsetabv2"></a>[Slut punkts svar för v2 förutsägelse](#tab/V2)

```JSON
  "entities": [
    {
      "entity": "paris",
      "type": "Cities",
      "startIndex": 18,
      "endIndex": 22,
      "resolution": {
        "values": [
          "Paris"
        ]
      }
    }
  ]
```

#### <a name="v3-prediction-endpoint-responsetabv3"></a>[V3 slut punkts svar för förutsägelse](#tab/V3)


Detta är JSON om `verbose=false` anges i frågesträngen:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Detta är JSON om `verbose=true` anges i frågesträngen:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ],
    "$instance": {
        "Cities": [
            {
                "type": "Cities",
                "text": "paris",
                "startIndex": 18,
                "length": 5,
                "modelTypeId": 5,
                "modelType": "List Entity Extractor",
                "recognitionSources": [
                    "model"
                ]
            }
        ]
    }
}
```

* * * 

|Dataobjekt|Entitetsnamn|Value|
|--|--|--|
|Lista entitet|`Cities`|`paris`|


## <a name="next-steps"></a>Nästa steg

I den här [självstudien](luis-quickstart-intent-and-list-entity.md)får du lära dig hur du använder en **list-entitet** för att extrahera exakta matchningar av text från en lista över kända objekt. 

---
title: Listentitetstyp - LUIS
description: Listentiteter representerar en fast, sluten uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listentiteter. Använd funktionen Rekommendera om du vill se förslag på nya ord baserat på den aktuella listan.
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 795d16bc2e0c4223ff3ac283a72493923d3ab355
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79297245"
---
# <a name="list-entity"></a>Lista entitet

Listentiteter representerar en fast, sluten uppsättning relaterade ord tillsammans med deras synonymer. LUIS identifierar inte ytterligare värden för listentiteter. Använd funktionen **Rekommendera** om du vill se förslag på nya ord baserat på den aktuella listan. Om det finns mer än en listentitet med samma värde returneras varje entitet i slutpunktsfrågan.

En listentitet är inte maskininlärd. Det är en exakt textmatchning. LUIS markerar en matchning till ett objekt i en lista som en entitet i svaret.

**Entiteten passar bra när textdata:**

* Är en känd uppsättning.
* Det förändras inte ofta. Om du behöver ändra listan ofta eller vill att listan ska expandera sig själv är en enkel entitet som har ökat med en fraslista ett bättre val.
* Uppsättningen inte överskrider de högsta [gränserna](luis-boundaries.md) för LUIS för den här entitetstypen.
* Texten i uttryck är en skiftlägesokänslig matchning med en synonym eller det kanoniska namnet. LUIS använder inte listan utöver matchningen. Luddig matchning, avledning, plural och andra varianter löses inte med en listentitet. Om du vill hantera variationer bör du överväga att använda ett [mönster](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) med valfri textsyntax.

![lista entitet](./media/luis-concept-entities/list-entity.png)

## <a name="example-json-to-import-into-list-entity"></a>Exempel på .json som ska importeras till listentiteten

  Du kan importera värden till en befintlig listentitet med följande .json-format:

  ```JSON
  [
      {
          "canonicalForm": "Blue",
          "list": [
              "navy",
              "royal",
              "baby"
          ]
      },
      {
          "canonicalForm": "Green",
          "list": [
              "kelly",
              "forest",
              "avacado"
          ]
      }
  ]
  ```

## <a name="example-json-response"></a>Exempel på JSON-svar

Anta att appen har `Cities`en lista med namnet, som tillåter varianter av stadsnamn, inklusive flygplatsstad (Sea-tac), flygplatsnummer (SEA), postnummer (98101) och telefonnummer (206).

|Listobjekt|Artikel synonymer|
|---|---|
|`Seattle`|`sea-tac`, `sea`, `98101`, `206`, `+1` |
|`Paris`|`cdg`, `roissy`, `ory`, `75001`, `1`, `+33`|

`book 2 tickets to paris`

I föregående uttryck mappas ordet `paris` till paris-objektet som `Cities` en del av listentiteten. Listentiteten matchar både objektets normaliserade namn och artikel synonymerna.

#### <a name="v2-prediction-endpoint-response"></a>[V2-effektpunktssvar för förutsägelse](#tab/V2)

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

#### <a name="v3-prediction-endpoint-response"></a>[V3-effektslutpunktssvar för förutsägelse](#tab/V3)


Detta är JSON `verbose=false` om anges i frågesträngen:

```json
"entities": {
    "Cities": [
        [
            "Paris"
        ]
    ]
}
```

Detta är JSON `verbose=true` om anges i frågesträngen:

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

|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Lista entitet|`Cities`|`paris`|


## <a name="next-steps"></a>Nästa steg

I den här [självstudien](tutorial-list-entity.md)får du lära dig hur du använder en **listentitet** för att extrahera exakta matchningar med text från en lista med kända objekt.

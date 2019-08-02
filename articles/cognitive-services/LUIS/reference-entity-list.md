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
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ca9f8b570ee28b1913c8ec81c66a5b70827c04d6
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68559965"
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

Ett annat exempel uttryck med en synonym för Paris:

`book 2 tickets to roissy`

```JSON
"entities": [
  {
    "entity": "roissy",
    "type": "Cities",
    "startIndex": 18,
    "endIndex": 23,
    "resolution": {
      "values": [
        "Paris"
      ]
    }
  }
]
```

|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Enkel enhet|`Customer`|`bob jones`|

## <a name="next-steps"></a>Nästa steg

I den [](luis-quickstart-intent-and-list-entity.md)här självstudien får du lära dig hur du använder en **list-entitet** för att extrahera exakta matchningar av text från en lista över kända objekt. 

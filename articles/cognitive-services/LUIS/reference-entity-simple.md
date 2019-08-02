---
title: Enkel entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: En enkel entitet är en generisk entitet som beskriver ett enda koncept och som har lärts från den dator som har lärts. Eftersom enkla entiteter vanligt vis är namn, till exempel företags namn, produkt namn eller andra kategorier av namn, lägger du till en fras lista när du använder en enkel entitet för att öka signalen av de namn som används.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: c1514b6cd512924a162a524d11e888055fa06514
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563205"
---
# <a name="simple-entity"></a>Enkel entitet 

En enkel entitet är en generisk entitet som beskriver ett enda koncept och som har lärts från den dator som har lärts. Eftersom enkla entiteter vanligt vis är namn, till exempel företags namn, produkt namn eller andra kategorier av namn, lägger du till en [fras lista](luis-concept-feature.md) när du använder en enkel entitet för att öka signalen av de namn som används. 

**Entiteten passar bra när:**

* Data formateras inte konsekvent, utan anger samma sak. 

![enkel enhet](./media/luis-concept-entities/simple-entity.png)

## <a name="example-json"></a>Exempel på JSON

`Bob Jones wants 3 meatball pho`

I den föregående uttryck `Bob Jones` är märkt som en enkel `Customer` entitet.

De data som returneras från slutpunkten innehåller entitetsnamnet, identifierade texten från uttryck, platsen för den identifierade texten och poängen:

```JSON
"entities": [
  {
  "entity": "bob jones",
  "type": "Customer",
  "startIndex": 0,
  "endIndex": 8,
  "score": 0.473899543
  }
]
```

|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Enkel enhet|`Customer`|`bob jones`|

## <a name="next-steps"></a>Nästa steg

I den [](luis-quickstart-primary-and-secondary-data.md)här självstudien extraherar du data från en uttryck med hjälp av den **enkla entiteten**. Om du vill öka extraherings precisionen lägger du till en [fras lista](luis-concept-feature.md) med termer som är speciella för den enkla entiteten.
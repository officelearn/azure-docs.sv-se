---
title: Sammansatt entitetstyp – LUIS
titleSuffix: Azure Cognitive Services
description: En sammansatt entitet består av andra entiteter, till exempel färdiga entiteter, enkla, reguljära uttryck och list enheter. Separata entiteter utgör en helhet.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/24/2019
ms.author: diberry
ms.openlocfilehash: ea258275cf954bc6e06da03324c2ae93de0e7fde
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563238"
---
# <a name="composite-entity"></a>Sammansatt entitet 

En sammansatt entitet består av andra entiteter, till exempel färdiga entiteter, enkla, reguljära uttryck och list enheter. Separata entiteter utgör en helhet. 

**Den här entiteten passar bra när data:**

* Är relaterade till varandra. 
* De är relaterade till varandra i yttrandet.
* Använda en mängd olika typer av enheter.
* Måste grupperas och bearbetas av klient programmet som en informations enhet.
* Ha en rad olika användar yttranden som kräver maskin inlärning.

![Sammansatt entitet](./media/luis-concept-entities/composite-entity.png)

## <a name="example-json"></a>Exempel på JSON

Betrakta en sammansatt entitet av `number` fördefinierad `Location::ToLocation` och med följande uttryck:

`book 2 tickets to paris`

Observera att `2`, antalet, och `paris`, ToLocation har ord mellan dem som inte tillhör någon av enheterna. Grön linje, används i en taggade uttryck i den [LUIS](luis-reference-regions.md) webbplats, anger en sammansatt entitet.

![Sammansatt entitet](./media/luis-concept-data-extraction/composite-entity.png)

Sammansatta entiteter returneras i en `compositeEntities` matris och alla enheter i sammansatt returneras också i de `entities` matris:

```JSON

"entities": [
    {
    "entity": "2 tickets to cairo",
    "type": "ticketInfo",
    "startIndex": 0,
    "endIndex": 17,
    "score": 0.67200166
    },
    {
    "entity": "2",
    "type": "builtin.number",
    "startIndex": 0,
    "endIndex": 0,
    "resolution": {
        "subtype": "integer",
        "value": "2"
    }
    },
    {
    "entity": "cairo",
    "type": "builtin.geographyV2",
    "startIndex": 13,
    "endIndex": 17
    }
],
"compositeEntities": [
    {
    "parentType": "ticketInfo",
    "value": "2 tickets to cairo",
    "children": [
        {
        "type": "builtin.geographyV2",
        "value": "cairo"
        },
        {
        "type": "builtin.number",
        "value": "2"
        }
    ]
    }
]
```    

|Dataobjekt|Entitetsnamn|Värde|
|--|--|--|
|Fördefinierade enhet - nummer|”builtin.number”|”2”|
|Fördefinierad entitet – GeographyV2|”Location::ToLocation”|”paris”|

## <a name="next-steps"></a>Nästa steg

I den [](luis-tutorial-composite-entity.md)här självstudien lägger du till en **sammansatt enhet** för att bunta extraherade data av olika typer till en enda som innehåller entiteten. Genom att paketera data extrahera klientprogrammet enkelt relaterade data i olika datatyper.

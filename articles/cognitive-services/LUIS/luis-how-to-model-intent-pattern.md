---
title: Mönster Lägg till precision – LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till mallar för att förbättra förutsägelse noggrannhet i Language Understanding-program (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: cbd8ad73ff4a03790dd6b22d5ce33acf09a2b125
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491361"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Så här lägger du till mönster för att förbättra förutsägelse noggrannhet
När en LUIS-app tar emot slut punkts yttranden använder du ett [mönster](luis-concept-patterns.md) för att förbättra förutsägelse noggrannheten för yttranden som visar ett mönster i Word-ordning och Word-val. Mönster använder en speciell [syntax](luis-concept-patterns.md#pattern-syntax) för att ange platsen för: [entiteter](luis-concept-entity-types.md), entitets [roller](luis-concept-roles.md)och valfri text.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-template-utterance-to-create-pattern"></a>Lägg till mall uttryck för att skapa mönster
1. Öppna appen genom att välja namnet på sidan **Mina appar** och välj sedan **mönster** i den vänstra panelen under **förbättra appens prestanda**.

    ![Skärm bild av mönster lista](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Välj rätt avsikt för mönstret. 

    ![Välj avsikt](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. I text rutan mall skriver du mallen uttryck och väljer RETUR. När du vill ange entitetsnamnet använder du rätt syntax för mönster enhet. Starta entitetens syntax med `{`. Listan med entiteter visas. Välj rätt entitet och välj sedan Retur. 

    ![Skärm bild av entiteten för mönster](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om entiteten innehåller en [roll](luis-concept-roles.md), anger du rollen med ett enda kolon `:`efter entitetsnamnet, till exempel `{Location:Origin}`. Listan över roller för entiteterna visas i en lista. Välj rollen och välj sedan Retur. 

    ![Skärm bild av entitet med roll](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    När du har valt rätt entitet, slutför Ange mönstret och välj sedan Retur. När du är klar med att ange mönster [tränar](luis-how-to-train.md) du din app.

    ![Skärm bild av det angivna mönstret med båda typerna av entiteter](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Träna din app när du har ändrat modellen med mönster
När du har lagt till, redigerat, tagit bort eller omtilldelat ett mönster, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för dina ändringar för att påverka slut punkts frågor. 

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Använd sammanhangsbaserade verktygsfält

I kontext verktygsfältet ovanför mönster listan kan du göra följande:

* Sök efter mönster
* Redigera ett mönster
* Tilldela om enskilda mönster till olika avsikter
* Tilldela om flera mönster till olika avsikter
* Ta bort ett enstaka mönster
* Ta bort flera mönster
* Filtrera mönster lista efter entitet
* Filter-mönster-lista-för-avsikt
* Ta bort enhets-eller bevarande filter
* Lägg till mönster från befintlig uttryck på avsikts-eller entitet-Sidan

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett mönster](luis-tutorial-pattern.md) med ett mönster. alla och roller med en själv studie kurs.
* Lär dig hur du [tränar](luis-how-to-train.md) din app.

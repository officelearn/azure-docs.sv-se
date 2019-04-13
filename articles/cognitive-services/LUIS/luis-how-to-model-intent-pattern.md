---
title: Mönster öka noggrannheten
titleSuffix: Language Understanding - Azure Cognitive Services
description: Lägga till mönstret mallar för att förbättra förutsägelsefunktionen i Språkförståelse (LUIS) program.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: 202b9632b7a7faaf955874a0300edbe5134b7fa1
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/12/2019
ms.locfileid: "59521262"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Hur du lägger till mönster för att förbättra förutsägelsefunktionen
När en LUIS-app tar emot endpoint yttranden måste använda en [mönstret](luis-concept-patterns.md) förbättra prognosens noggrannhet för yttranden som avslöjar ett mönster i ordföljden och word valet. Mönster som använder specifika [syntax](luis-concept-patterns.md#pattern-syntax) att ange platsen för: [entiteter](luis-concept-entity-types.md), entiteten [roller](luis-concept-roles.md), och valfria text.

## <a name="add-template-utterance-to-create-pattern"></a>Lägg till mall-uttryck för att skapa mönstret
1. Öppna din app genom att välja dess namn på **Mina appar** och välj sedan **mönster** i den vänstra panelen under **förbättra apprestanda**.

    ![Skärmbild av lista över mönster](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Välj rätt avsikten för mönstret. 

    ![Välj avsikt](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Ange mall-uttryck i textrutan mallen och välj RETUR. Använda rätt mönstret entitet syntax när du vill ange enhetens namn. Börja med entiteten syntax för `{`. Lista över entiteter visas. Välj rätt entiteten och tryck sedan på RETUR. 

    ![Skärmbild av entitet för mönstret](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om din entitet innehåller en [rollen](luis-concept-roles.md), indikera vilken roll med ett enda kolon `:`efter att entiteten namnet, till exempel `{Location:Origin}`. I listan över roller för enheterna som visas i en lista. Välj rollen och tryck sedan på RETUR. 

    ![Skärmbild av entiteten med rollen](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    När du har valt rätt entiteten har angett mönstret och tryck sedan på RETUR. När du är klar att ange mönster [träna](luis-how-to-train.md) din app.

    ![Skärmbild av angivna mönstret med båda typerna av enheter](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Träna din app när du har ändrat modell med mönster
När du lägger till, redigera, ta bort eller omtilldela ett mönster [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

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

## <a name="use-contextual-toolbar"></a>Använda sammanhangsberoende verktygsfältet

Sammanhangsberoende verktygsfältet ovanför listan mönster kan du:

* Sök efter mönster
* Redigera ett mönster
* Omtilldela enskilda mönstret till olika avsikt
* Tilldela flera mönster till olika avsikt
* Delete-a-single-pattern
* Ta bort flera mönster
* Filtrera enheter efter entitet
* Filter-pattern-list-by-intent
* Ta bort entitet eller avsiktlig filter
* Lägg till mönster från befintliga uttryck på avsikten eller entitet

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [bygga ett mönster](luis-tutorial-pattern.md) med en pattern.any och roller med en självstudie.
* Lär dig hur du [träna](luis-how-to-train.md) din app.

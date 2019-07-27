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
ms.date: 04/01/2019
ms.author: diberry
ms.openlocfilehash: f0e801afde240d8b2d6e0798e6894b2e83f9c5cb
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560431"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Så här lägger du till mönster för att förbättra förutsägelse noggrannhet
När en LUIS-app tar emot slut punkts yttranden använder du ett [mönster](luis-concept-patterns.md) för att förbättra förutsägelse noggrannheten för yttranden som visar ett mönster i Word-ordning och Word-val. Mönster använder en speciell [syntax](luis-concept-patterns.md#pattern-syntax) för att ange platsen för: [entiteter](luis-concept-entity-types.md), entitets [roller](luis-concept-roles.md)och valfri text.

## <a name="add-template-utterance-to-create-pattern"></a>Lägg till mall-uttryck för att skapa mönstret
1. Öppna din app genom att välja dess namn på **Mina appar** och välj sedan **mönster** i den vänstra panelen under **förbättra apprestanda**.

    ![Skärmbild av lista över mönster](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Välj rätt avsikten för mönstret. 

    ![Välj avsikt](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Ange mall-uttryck i textrutan mallen och välj RETUR. Använda rätt mönstret entitet syntax när du vill ange enhetens namn. Börja med entiteten syntax för `{`. Lista över entiteter visas. Välj rätt entiteten och tryck sedan på RETUR. 

    ![Skärmbild av entitet för mönstret](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om entiteten innehåller en [roll](luis-concept-roles.md), anger du rollen med ett enskilt kolon `:`, efter `{Location:Origin}`namnet på entiteten, till exempel. I listan över roller för enheterna som visas i en lista. Välj rollen och tryck sedan på RETUR. 

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

## <a name="use-contextual-toolbar"></a>Använd sammanhangsbaserade verktygsfält

I kontext verktygsfältet ovanför mönster listan kan du göra följande:

* Sök efter mönster
* Redigera ett mönster
* Omtilldela enskilda mönstret till olika avsikt
* Tilldela flera mönster till olika avsikt
* Ta bort ett enstaka mönster
* Ta bort flera mönster
* Filtrera enheter efter entitet
* Filter-mönster-lista-för-avsikt
* Ta bort entitet eller avsiktlig filter
* Lägg till mönster från befintliga uttryck på avsikten eller entitet

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett mönster](luis-tutorial-pattern.md) med ett mönster. alla och roller med en själv studie kurs.
* Lär dig hur du [träna](luis-how-to-train.md) din app.

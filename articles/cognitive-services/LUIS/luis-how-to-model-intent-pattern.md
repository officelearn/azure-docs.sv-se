---
title: Mönster öka noggrannheten
titleSuffix: Language Understanding - Azure Cognitive Services
description: Lär dig hur du lägger till mönstret mallar i program för Språkförståelse (LUIS) att förbättra förutsägelsefunktionen.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: cff9a17b488363ce6acd80cd90f15c58c6fe6cb5
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885769"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Hur du lägger till mönster för att förbättra förutsägelsefunktionen
När en LUIS-app tar emot endpoint yttranden, använda den [konceptet](luis-concept-patterns.md) av mönster förbättra prognosens noggrannhet för yttranden som avslöjar ett mönster i ordföljden och word valet. Mönster Använd [entiteter](luis-concept-entity-types.md) och deras roller för att extrahera data med hjälp av specifika mönstret syntax. 

## <a name="add-template-utterance-to-create-pattern"></a>Lägg till mall-uttryck för att skapa mönstret
1. Öppna din app genom att välja dess namn på **Mina appar** och välj sedan **mönster** i den vänstra panelen under **förbättra apprestanda**.

    ![Skärmbild av lista över mönster](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Välj rätt avsikten för mönstret. 

    ![Välj avsikt](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Ange mall-uttryck i textrutan mallen och välj RETUR. Använda rätt mönstret entitet syntax när du vill ange enhetens namn. Börja med entiteten syntax för `{`. Lista över entiteter visas. Välj rätt entiteten och tryck sedan på RETUR. 

    ![Skärmbild av entitet för mönstret](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om din entitet innehåller en roll kan indikera vilken roll med ett enda kolon `:`efter att entiteten namnet, till exempel `{Location:Origin}`. I listan över roller för enheterna som visas i en lista. Välj rollen och tryck sedan på RETUR. 

    ![Skärmbild av entiteten med rollen](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    När du har valt rätt entiteten har angett mönstret och tryck sedan på RETUR. När du är klar att ange mönster [träna](luis-how-to-train.md) din app.

    ![Skärmbild av angivna mönstret med båda typerna av enheter](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Sök efter mönster
Sökningen kan du hitta de mönster som innehåller viss text.  

1. Välj förstoringsglasikonen.

    ![Skärmbild av mönster sida med verktyget sökikonen markerat](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Skriv söktexten i sökrutan i det övre högra hörnet av listan över mönster och tryck RETUR. Lista över mönster har uppdaterats för att visa endast de mönster som inklusive söktexten.

    ![Skärmbild av mönster-sidan med söktext i sökrutan markerat](./media/luis-how-to-model-intent-pattern/search-text.png)

    Om du vill avbryta sökningen och återställer din fullständig lista över mönster, tar du bort den söktext som du har skrivit.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Redigera ett mönster
1. Om du vill redigera ett mönster, Välj ellipsen (***...*** ) knappen längst till höger på raden för mönstret och välj **redigera**. 

    ![Skärmbild av redigera menyalternativ i mönstret raden](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Ange eventuella ändringar i textrutan. När du är klar anger väljer du. När du är klar redigering mönster [träna](luis-how-to-train.md) din app.

    ![Skärmbild av Redigera mönster](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Omtilldela enskilda mönstret till olika avsikt

Att omtilldela en enda mönstret till en annan typ av avsikt, välj listrutan avsikt till höger om texten mönster och välj en annan typ av avsikt.

![Skärmbild av omtilldela enskilda mönstret till olika avsikt](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Tilldela flera mönster till olika avsikt

För att tilldela flera mönster i en annan typ av avsikt, markerar du kryssrutan till vänster om varje mönster eller markera kryssrutan översta. Den **omtilldela avsikt** visas i verktygsfältet. Välj rätt avsikten för mönster. 

![Skärmbild av omtilldela flera mönster till olika avsikt](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Ta bort ett enda mönster

1. Om du vill ta bort ett mönster, Välj ellipsen (***...*** ) knappen längst till höger på raden för mönstret och välj **ta bort**. 

    ![Skärmbild av ta bort uttryck](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Välj **Ok** att bekräfta borttagningen.

    ![Skärmbild av ta bort bekräftelse](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Ta bort flera mönster

1. Om du vill ta bort flera mönster, markerar du kryssrutan till vänster om varje mönster eller markera kryssrutan längst upp. Den **ta bort mönster (s)** visas i verktygsfältet. Välj **ta bort mönster (s)**.  

    ![Skärmbild av att ta bort flera mönster](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. Den **ta bort mönster** bekräftelsedialogruta visas. Välj **Ok** att slutföra borttagningen.

    ![Skärmbild av att ta bort flera mönster bekräftelserutan](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Filtrera enheter efter entitet

Om du vill filtrera listan över mönster av en specifik enhet, Välj **entitet filter** i verktygsfältet ovanför mönstren. 

![Skärmbild av filtrering mönster av enhet](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

När filtret tillämpas entitetsnamnet visas ovanför verktygsfältet. 

## <a name="filter-pattern-list-by-intent"></a>Filtrera enheter efter avsikt

Om du vill filtrera listan över mönster av en specifik avsikt, Välj **avsikt filtrerar** i verktygsfältet ovanför mönstren. 

![Skärmbild av filtrering mönster med avsikt](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

När filtret tillämpas avsikt namnet visas ovanför verktygsfältet. 

## <a name="remove-entity-or-intent-filter"></a>Ta bort entitet eller avsiktlig filter
När filtreras mönsterlistan, enheten eller avsiktlig namn visas ovanför verktygsfältet. Välj namnet om du vill ta bort filtret.

![Skärmbild av att ta bort entiteten från filter](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Filtret tas bort och visa alla mönster. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Lägg till mönster från befintliga uttryck på avsikten eller entitet
Du kan skapa ett mönster från en befintlig uttryck på antingen den **avsikt** eller **entitet** sidan. Alla uttryck på vilken avsikt eller enhet som visas i en lista med i den högra kolumnen att ge åtkomst till uttryck på servernivå alternativ som **redigera**, **ta bort**, och **Lägg till som mönster**.

1. På den valda raden i uttryck, Välj ellipsen (***...*** ) till höger om uttryck och välj **Lägg till som mönster**.

    [![Skärmbild av yttranden tabell med Lägg till mönster som markerats i alternativmenyn](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Skärmbild av yttranden tabell med Lägg till mönster som markerats i alternativmenyn")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Ändra mönstret enligt den [syntaxregler](luis-concept-patterns.md#pattern-syntax). Om det uttryck som du har valt är märkt med entiteter, finns dessa entiteter redan i mönstret med rätt syntax.

    ![Skärmbild av filtrerade mönster av enhet](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Träna din app när du har ändrat modell med mönster
När du lägger till, redigera, ta bort eller omtilldela ett mönster [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [bygga ett mönster](luis-tutorial-pattern.md) med en pattern.any och roller.
* Lär dig hur du [träna](luis-how-to-train.md) din app.
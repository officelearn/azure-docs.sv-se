---
title: Lägga till mallar för mönster i THOMAS appar | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du lägger till mönster mallar i språk förstå (THOMAS) program för att förbättra förutsägelsefunktionen.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: 68c0ea1fd3f2e60e0adec631f33c8bd09a3d9960
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/08/2018
ms.locfileid: "35356390"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Hur du lägger till mönster för att förbättra förutsägelsefunktionen
När en THOMAS app får endpoint utterances kan använda den [konceptet](luis-concept-patterns.md) av mönster för att förbättra förutsägelsefunktionen för utterances som avslöjar ett mönster i word ordning och val av word. Mönster Använd [entiteter](luis-concept-entity-types.md) och deras roller att extrahera data med hjälp av specifika mönster syntax. 

## <a name="add-template-utterance-to-create-pattern"></a>Lägg till mall utterance för att skapa mönster
1. Öppna din app genom att markera namnet på **Mina appar** och väljer sedan **mönster** i den vänstra rutan under **förbättra prestanda för**.

    ![Skärmbild av lista över mönster](./media/luis-how-to-model-intent-pattern/patterns-1.png)

2. Välj rätt avsett för mönstret. 

    ![Välj avsikt](./media/luis-how-to-model-intent-pattern/patterns-2.png)

3. Ange mall-utterance i textrutan mall och välj RETUR. Använda rätt mönster entitet syntax när du vill ange entitetsnamnet. Börja med entiteten syntax för `{`. Lista över enheter visar. Välj rätt entiteten och välj sedan RETUR. 

    ![Skärmbild av entiteten för.](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om entiteten innehåller en roll, ange roll med ett enda kolon `:`när entiteten namn, till exempel `{Location:Origin}`. Lista över roller för entiteter visas i en lista. Välj rollen och välj sedan RETUR. 

    ![Skärmbild av entiteten med rollen](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    När du har valt rätt entiteten har angett mönstret och välj sedan RETUR. När du är klar anger mönster [träna](luis-how-to-train.md) din app.

    ![Skärmbild av angivna mönster med båda typer av enheter](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="search-patterns"></a>Söksträngar
Sökningen kan du söka efter mönster som innehåller vissa angiven text.  

1. Välj förstoringsglaset.

    ![Skärmbild av mönster sida med verktyget sökikonen markerat](./media/luis-how-to-model-intent-pattern/search-icon.png)

    Skriv söktexten i sökrutan i det övre högra hörnet av listan över mönster och välj RETUR. Lista över mönster uppdateras för att visa de mönster som inklusive söktexten.

    ![Skärmbild av mönster sida med söktexten i sökrutan markerat](./media/luis-how-to-model-intent-pattern/search-text.png)

    Ta bort söktexten som du har angett för att avbryta sökningen och återställa en fullständig lista över mönster.

<!-- TBD: should I be able to click on the magnifying glass again to close the search box? It doesn't reset the list. -->

## <a name="edit-a-pattern"></a>Redigera ett mönster
1. Om du vill redigera ett mönster, väljer du tre punkter (...)-ikonen längst till höger på raden för mönstret och välj sedan **redigera**. 

    ![Skärmbild av redigera menyalternativet i mönstret rad](./media/luis-how-to-model-intent-pattern/patterns-three-dots.png) 

2. Ange eventuella ändringar i textrutan. Välj ange när du är klar. När du är klar Redigera mönster [träna](luis-how-to-train.md) din app.

    ![Skärmbild av Redigera mönster](./media/luis-how-to-model-intent-pattern/edit-pattern.png)

## <a name="reassign-individual-pattern-to-different-intent"></a>Tilldela enskilda mönstret till olika

Markera kryssrutan avsiktshantering listan till höger om texten mönster för att tilldela ett enda mönster till olika och väljer olika.

![Skärmbild av omtilldela enskilda mönstret till olika](./media/luis-how-to-model-intent-pattern/reassign-individual-pattern.png)

## <a name="reassign-several-patterns-to-different-intent"></a>Tilldela om flera olika mönster

Om du vill tilldela flera mönster till en annan typ av avsikt, markerar du kryssrutan till vänster om varje mönster eller markerar du kryssrutan översta. Den **omtilldela avsikt** visas i verktygsfältet. Välj rätt avsett för mönster. 

![Skärmbild av omtilldela flera olika mönster](./media/luis-how-to-model-intent-pattern/reassign-many-patterns.png)

## <a name="delete-a-single-pattern"></a>Ta bort ett enda mönster

1. Om du vill ta bort ett mönster, väljer du tre punkter (...)-ikonen längst till höger på raden för mönstret och välj sedan **ta bort**. 

    ![Skärmbild av ta bort utterance](./media/luis-how-to-model-intent-pattern/patterns-three-dots-ddl.png)

2. Välj **Ok** att bekräfta borttagningen.

    ![Skärmbild av ta bort bekräftelse](./media/luis-how-to-model-intent-pattern/confirm-delete.png)

## <a name="delete-several-patterns"></a>Ta bort flera mönster

1. Välj kryssrutan till vänster om varje mönster eller översta kryssrutan för att ta bort flera mönster. Den **ta bort mönster (s)** visas i verktygsfältet. Välj **ta bort mönster (s)**.  

    ![Skärmbild av borttagning av flera mönster](./media/luis-how-to-model-intent-pattern/delete-many-patterns.png)

2. Den **ta bort mönster** bekräftelsedialogruta visas. Välj **Ok** att slutföra borttagningen.

    ![Skärmbild av borttagning av flera mönster](./media/luis-how-to-model-intent-pattern/delete-many-patterns-confirmation.png)

## <a name="filter-pattern-list-by-entity"></a>Filtrera mönster av enheten

Om du vill filtrera listan över mönster av en specifik enhet, Välj **entitet filter** i verktygsfältet ovanför mönster. 

![Skärmbild av mönster för filtrering av enheten](./media/luis-how-to-model-intent-pattern/filter-entities-1.png)

När filtret används entitetsnamnet visas under verktygsfältet. 

## <a name="filter-pattern-list-by-intent"></a>Filtrera mönster av avsikt

Om du vill filtrera listan över mönster av en specifik avsikt, Välj **avsikt filtrerar** i verktygsfältet ovanför mönster. 

![Skärmbild av mönster för filtrering av avsikt](./media/luis-how-to-model-intent-pattern/filter-intents-1.png)

När filtret tillämpas avsiktshantering namnet som visas under verktygsfältet. 

## <a name="remove-entity-or-intent-filter"></a>Ta bort enheten eller avsiktshantering filter
När mönsterlistan filtreras visas entiteten eller avsiktshantering namn under verktygsfältet. Välj om du vill ta bort filtret.

![Skärmbild av filtrerade mönster av enheten](./media/luis-how-to-model-intent-pattern/filter-entities-2.png)

Filtret bort och visa alla mönster. 

## <a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>Lägg till mönster från befintliga utterance på avsikten eller entitet
Du kan skapa ett mönster från en befintlig utterance på antingen den **avsikt** eller **entiteten** sidan. Alla utterances på en sida med avsikt eller enhet som visas i en lista med den högra kolumnen som ger tillgång till utterance nivå alternativ som **redigera**, **ta bort**, och **Lägg till som mönster**.

1. Markera de tre punkterna (...) till höger om utterance och välj på den markerade raden i utterance **Lägg till som mönster**.

    [![](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png "Skärmbild av utterances tabellen med Lägg till mönster som markerats i alternativmenyn")](./media/luis-how-to-model-intent-pattern/add-pattern-from-utterance.png)

2. Ändra mönster enligt den [syntaxregler](luis-concept-patterns.md#pattern-syntax). Om utterance som du har valt är märkt med entiteter har dessa enheter redan mönster med korrekt syntax.

    ![Skärmbild av filtrerade mönster av enheten](./media/luis-how-to-model-intent-pattern/confirm-patterns-modal.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Träna din app när du har ändrat modellen med mönster
När du lägger till, redigera, ta bort eller tilldela om ett mönster [träna](luis-how-to-train.md) och [publicera](PublishApp.md) din app för att ändringarna ska påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [bygga ett mönster](luis-tutorial-pattern.md) med en pattern.any och roller.
* Lär dig hur du [träna](luis-how-to-train.md) din app.
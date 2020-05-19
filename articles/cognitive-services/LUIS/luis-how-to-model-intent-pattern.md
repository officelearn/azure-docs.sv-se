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
ms.date: 05/06/2020
ms.author: diberry
ms.openlocfilehash: 077e29856842972fae2c723d4a2c368cbb80df06
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83593264"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Så här lägger du till mönster för att förbättra förutsägelse noggrannhet
När en LUIS-app tar emot slut punkts yttranden använder du ett [mönster](luis-concept-patterns.md) för att förbättra förutsägelse noggrannheten för yttranden som visar ett mönster i Word-ordning och Word-val. Mönster använder en speciell [syntax](luis-concept-patterns.md#pattern-syntax) för att ange platsen för: [entiteter](luis-concept-entity-types.md), entitets [roller](luis-concept-roles.md)och valfri text.

> [!CAUTION]
> Mönster innehåller bara de datorer som har lärt sin föräldrar, inte underentiteter.

## <a name="adding-example-utterances-as-pattern"></a>Lägger till exempel yttranden som mönster

Om du vill lägga till ett mönster för en entitet är det _enklaste_ sättet att skapa mönstret från sidan information om avsikt. Detta säkerställer att syntaxen matchar exemplet uttryck.

1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. På sidan för **avsikts** listor väljer du namnet på den uttryck som du vill skapa en mall uttryck från.
1. På sidan information om avsikt väljer du raden för det exempel uttryck som du vill använda som mall-uttryck och väljer sedan **+ Lägg till som mönster** i verktygsfältet kontext.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du väljer exempel uttryck som ett mall mönster på sidan information om avsikt.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. I popup-fönstret väljer du **Done** på sidan **Bekräfta mönster** . Du behöver inte definiera entiteternas underentiteter eller funktioner. Du behöver bara ange den enhet som har registrerats av enheten.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild som visar hur du uttryck i exemplet som ett mall mönster på sidan information om avsikt.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Om du behöver redigera mallen, till exempel välja text som valfri, med `[]` hakparenteserna (hakparenteser), måste du göra den här redigeringen från sidan **mönster** .

1. I navigerings fältet väljer du **träna** för att träna appen med det nya mönstret.

## <a name="add-template-utterance-using-correct-syntax"></a>Lägg till mall-uttryck med korrekt syntax
1. Logga in på [Luis-portalen](https://www.luis.ai)och välj din **prenumerations** -och **redigerings resurs** för att se vilka appar som tilldelats den här redigerings resursen.
1. Öppna din app genom att välja namnet på sidan **Mina appar** .
1. Välj **mönster** i den vänstra panelen under **förbättra appens prestanda**.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av mönster lista](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Välj rätt avsikt för mönstret.

1. I text rutan mall skriver du mallen uttryck och väljer RETUR. När du vill ange entitetsnamnet använder du rätt syntax för mönster enhet. Starta Entity-syntaxen med `{` . Listan med entiteter visas. Välj rätt entitet.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av entiteten för mönster](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om entiteten innehåller en [roll](luis-concept-roles.md), anger du rollen med ett enskilt kolon, `:` efter namnet på entiteten, till exempel `{Location:Origin}` . Listan över roller för entiteterna visas i en lista. Välj rollen och välj sedan Retur.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av entitet med roll](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    När du har valt rätt entitet, slutför Ange mönstret och välj sedan Retur. När du är klar med att ange mönster [tränar](luis-how-to-train.md) du din app.

    > [!div class="mx-imgBorder"]
    > ![Skärm bild av det angivna mönstret med båda typerna av entiteter](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

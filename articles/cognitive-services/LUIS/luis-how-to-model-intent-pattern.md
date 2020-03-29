---
title: Mönster lägger till noggrannhet - LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till mönstermallar för att förbättra förutsägelsenoggrannheten i LUIS-program (Language Understanding).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "76311724"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Hur man lägger till mönster för att förbättra förutsägelse noggrannhet
När en LUIS-app har fått slutpunktsyttranden använder du ett [mönster](luis-concept-patterns.md) för att förbättra förutsägelsenoggrannheten för yttranden som visar ett mönster i ordordning och ordval. Mönster använder specifik [syntax](luis-concept-patterns.md#pattern-syntax) för att ange platsen för: [entiteter,](luis-concept-entity-types.md)entitetsroller och valfri text. [roles](luis-concept-roles.md)

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Mönster inkluderar endast maskininlärda entitetsföräldrar, inte underkomponenter.

## <a name="adding-example-utterances-as-pattern"></a>Lägga till exempelyttranden som mönster

Om du vill lägga till ett mönster för en entitet är det _enklaste_ sättet att skapa mönstret från sidan Avsiktsinformation. Detta säkerställer att din syntax matchar exempelutsyntheten.

1. Välj appen på sidan **Mina appar** i [luis-portalen.](https://preview.luis.ai)
1. På listsidan avsikter väljer du **avsiktsnamnet** för det exempelutsikt som du vill skapa ett mallutseende från.
1. På sidan Avsiktsinformation markerar du raden för det exempelutseende som du vill använda som mallutseende och väljer sedan **+ Lägg till som mönster** i kontextverktygsfältet.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av att välja exempelutseende som ett mallmönster på sidan Avsiktsinformation.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. Välj **Klar** på sidan **Bekräfta mönster** i popup-rutan. Du behöver inte definiera entiteternas underkomponenter, begränsningar eller beskrivningar. Du behöver bara lista den maskininlärda entiteten.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av bekräftande exempelyttrande som ett mallmönster på sidan Avsiktsinformation.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Om du behöver redigera mallen, till exempel välja `[]` text som valfritt, med (kvadratiska) hakparenteser, måste du göra den här redigeringen från sidan **Mönster.**

1. I navigeringsfältet väljer du **Träna** för att träna appen med det nya mönstret.

## <a name="add-template-utterance-using-correct-syntax"></a>Lägga till mallutsyn med rätt syntax

1. Öppna appen genom att välja namnet på sidan **Mina appar** och välj sedan Mönster på den vänstra panelen under Förbättra **appens** **prestanda**.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av mönsterlistan](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Välj rätt avsikt för mönstret.

1. Skriv mallutseendet i malltextrutan och välj Retur. När du vill ange entitetsnamnet använder du rätt mönsterentitetssyntax. Börja entitetsyntaxen med `{`. Listan över entiteter visas. Välj rätt entitet.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av entitet för mönster](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om din entitet innehåller en [roll](luis-concept-roles.md)anger `:`du rollen med ett `{Location:Origin}`enda kolon, efter entitetsnamnet, till exempel . Listan över roller för entiteterna visas i en lista. Välj rollen och välj sedan Retur.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av entitet med roll](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    När du har valt rätt entitet slutför du mönstret och väljer sedan Retur. När du är klar med att ange mönster [tränar du](luis-how-to-train.md) din app.

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av inmatade mönster med båda typerna av entiteter](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Träna din app efter att ha bytt modell med mönster
När du har lagt till, redigerat, ta bort eller återanvisa ett mönster [tränar](luis-how-to-train.md) och [publicerar](luis-how-to-publish-app.md) du appen för att ändringarna ska påverka slutpunktsfrågorna.

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

## <a name="use-contextual-toolbar"></a>Använda kontextuellt verktygsfält

Med det kontextuella verktygsfältet ovanför mönsterlistan kan du:

* Sök efter mönster
* Redigera ett mönster
* Tilldela om individuellt mönster till olika avsikter
* Tilldela om flera mönster till olika avsikter
* Ta bort-ett-en-mönster
* Ta bort flera mönster
* Filtrera mönsterlista efter entitet
* Filter-mönster-lista efter avsikt
* Ta bort entitets- eller avsiktsfilter
* Lägga till mönster från befintlig yttrande på avsikts- eller entitetssida

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skapar ett mönster](luis-tutorial-pattern.md) med en pattern.any och roller med en handledning.
* Läs om hur du [tränar](luis-how-to-train.md) din app.

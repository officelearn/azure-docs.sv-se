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
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311724"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Så här lägger du till mönster för att förbättra förutsägelse noggrannhet
När en LUIS-app tar emot slut punkts yttranden använder du ett [mönster](luis-concept-patterns.md) för att förbättra förutsägelse noggrannheten för yttranden som visar ett mönster i Word-ordning och Word-val. Mönster använder en speciell [syntax](luis-concept-patterns.md#pattern-syntax) för att ange platsen för: [entiteter](luis-concept-entity-types.md), entitets [roller](luis-concept-roles.md)och valfri text.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Mönster innehåller bara de datorer som har lärt sin föräldrar, inte del komponenter.

## <a name="adding-example-utterances-as-pattern"></a>Lägger till exempel yttranden som mönster

Om du vill lägga till ett mönster för en entitet är det _enklaste_ sättet att skapa mönstret från sidan information om avsikt. Detta säkerställer att syntaxen matchar exemplet uttryck.

1. I [Luis-portalen för förhands granskning](https://preview.luis.ai)väljer du appen på sidan **Mina appar** .
1. På sidan för **avsikts** listor väljer du namnet på den uttryck som du vill skapa en mall uttryck från.
1. På sidan information om avsikt väljer du raden för det exempel uttryck som du vill använda som mall-uttryck och väljer sedan **+ Lägg till som mönster** i verktygsfältet kontext.

    > [!div class="mx-imgBorder"]
    > ![skärm bild som visar hur du väljer exempel uttryck som ett mall mönster på sidan information om avsikt.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. I popup-fönstret väljer du **Done** på sidan **Bekräfta mönster** . Du behöver inte definiera entitetens del komponenter, begränsningar eller beskrivningar. Du behöver bara ange den enhet som har registrerats av enheten.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av confirming-uttryck som ett mall mönster på sidan information om avsikt.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Om du behöver redigera mallen, till exempel välja text som valfri, med hakparenteserna `[]` (fyrkant) måste du göra den här redigeringen från **mönster** sidan.

1. I navigerings fältet väljer du **träna** för att träna appen med det nya mönstret.

## <a name="add-template-utterance-using-correct-syntax"></a>Lägg till mall-uttryck med korrekt syntax

1. Öppna din app genom att välja dess namn på **Mina appar** och välj sedan **mönster** i den vänstra panelen under **förbättra apprestanda**.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av mönster listan](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Välj rätt avsikten för mönstret.

1. Ange mall-uttryck i textrutan mallen och välj RETUR. Använda rätt mönstret entitet syntax när du vill ange enhetens namn. Börja med entiteten syntax för `{`. Lista över entiteter visas. Välj rätt entitet.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av en entitet för mönster](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Om entiteten innehåller en [roll](luis-concept-roles.md), anger du rollen med ett enda kolon `:`efter entitetsnamnet, till exempel `{Location:Origin}`. I listan över roller för enheterna som visas i en lista. Välj rollen och tryck sedan på RETUR.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av entiteten med roll](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    När du har valt rätt entiteten har angett mönstret och tryck sedan på RETUR. När du är klar att ange mönster [träna](luis-how-to-train.md) din app.

    > [!div class="mx-imgBorder"]
    > ![skärm bild av det angivna mönstret med båda typerna av entiteter](./media/luis-how-to-model-intent-pattern/patterns-5.png)

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

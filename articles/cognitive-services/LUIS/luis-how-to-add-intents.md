---
title: Lägg till avsikter – LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till avsikter i LUIS-appen för att identifiera grupper med frågor eller kommandon som har samma avsikt.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: 66a3350dee60772ce706af8995179dcd8c485b64
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "73904334"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Lägg till avsikter för att fastställa användar avsikt för yttranden

Lägg [till avsikter](luis-concept-intent.md) i Luis-appen för att identifiera grupper med frågor eller kommandon som har samma avsikt. 

Avsikter hanteras från det övre navigerings fältets **build** -avsnitt och sedan från den vänstra panelens **avsikter**. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Lägga till avsikt

1. I [Luis Preview Portal](https://preview.luis.ai)väljer du **build** för att Visa avsikter. 
1. På sidan **avsikter** väljer du **+ skapa**.
1. I dialog rutan **Skapa ny avsikt** anger du namnet på avsikten, till exempel `ModifyOrder`och väljer sedan **Slutför**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till avsikt](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Avsikts behoven är exempel yttranden.

## <a name="add-an-example-utterance"></a>Lägg till ett exempel uttryck

Exempel på yttranden är text exempel på användar frågor eller kommandon. Om du vill lära dig Language Understanding (LUIS) när du vill förutsäga avsikten måste du lägga till exempel yttranden i ett avsikts syfte. LUIS måste vara i intervallet 15 till 30 exempel yttranden för att börja förstå avsikten. Lägg inte till exempel yttranden i bulk. Varje uttryck bör väljas noggrant för att se om det skiljer sig från exempel som redan finns i avsikten. 

1. På sidan information om information anger du en relevant uttryck som du förväntar dig av dina användare `Deliver a large cheese pizza` , t. ex. i text rutan under namnet på avsikten och trycker sedan på RETUR.
 
    > [!div class="mx-imgBorder"]
    > ![Skärm bild av sidan information om information, där uttryck är markerat](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konverterar alla yttranden till gemener och lägger till blank steg runt [token](luis-language-support.md#tokenization) , till exempel bindestreck.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Fel vid förutsägelse av avsikt 

Ett exempel på en uttryck i ett avsikt kan ha ett förutsägelse fel mellan avsikten som exempel-uttryck är i och den avsikt som fastställs under träningen. 

Om du vill hitta uttryck förutsägelse fel och åtgärda dem använder du **filter** alternativen felaktig och oklar kombination med **visnings** alternativet i **detaljerad vy**. 

![Använd filter alternativet om du vill hitta uttryck förutsägelse fel och åtgärda dem.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

När filtren och vyn används och det finns exempel yttranden med fel visas yttranden och problemen i exempel uttryck-listan.

> [!div class="mx-imgBorder"]
> ![! [När filtren och vyn används och det finns exempel yttranden med fel visas yttranden och problemen i exempel uttryck-listan.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Varje rad visar den aktuella inlärningens förutsägelse Poäng för exemplet uttryck, närmsta rival, vilket är skillnaden i dessa två resultat. 

### <a name="fixing-intents"></a>Åtgärda avsikter

Om du vill lära dig hur du åtgärdar förutsägelse fel för avsikt använder du [sammanfattnings instrument panelen](luis-how-to-use-dashboard.md). Sammanfattnings instrument panelen innehåller analys för den aktiva versionens senaste utbildning och erbjuder de vanligaste förslagen för att åtgärda din modell.  

## <a name="using-the-contextual-toolbar"></a>Använda sammanhangsbaserade verktygsfält

Verktygsfältet kontext innehåller andra åtgärder:

* Redigera eller ta bort exempel uttryck
* Omtilldela exempel uttryck till en annan avsikt
* Filter och vyer: Visa endast yttranden som innehåller filtrerade entiteter eller Visa valfri information
* Sök i exempel yttranden

## <a name="train-your-app-after-changing-model-with-intents"></a>Träna din app efter att ha ändrat modellen med avsikter

När du har lagt till, redigerat eller tagit bort avsikter kan du [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app så att ändringarna tillämpas på slut punkts frågor. Träna inte efter varje enskild ändring. Träna efter en grupp av ändringar. 

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att lägga till [exempel yttranden](luis-how-to-add-example-utterances.md) med entiteter. 

---
title: Lägg till avsikter - LUIS
titleSuffix: Azure Cognitive Services
description: Lägg till avsikter i LUIS-appen för att identifiera grupper av frågor eller kommandon som har samma avsikter.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "73904334"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Lägga till avsikter för att avgöra användarens avsikt att yttra sig

Lägg till [avsikter](luis-concept-intent.md) i LUIS-appen för att identifiera grupper av frågor eller kommandon som har samma avsikt. 

Avsikter hanteras från det övre navigeringsfältets **byggavsnitt** och sedan från den vänstra panelens **avsikter**. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-intent"></a>Lägga till avsikt

1. Välj **Skapa** för att visa avsikter i [LUIS-förhandsgranskningsportalen.](https://preview.luis.ai) 
1. På sidan **Avsikter** väljer du **+ Skapa**.
1. I dialogrutan **Skapa ny avsikt** anger du avsiktsnamnet, till exempel `ModifyOrder`, och väljer **Klar**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till avsikt](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Avsikten behöver exempel yttranden.

## <a name="add-an-example-utterance"></a>Lägga till ett exempel yttrande

Exempel yttranden är textexempel på användarfrågor eller kommandon. Om du vill lära ut SPRÅKFÖRSTÅELSE (LUIS) när du ska förutsäga den här avsikten måste du lägga till exempelyttranden i en avsikt. LUIS behöver i intervallet 15 till 30 exempel yttranden för att börja förstå avsikten. Lägg inte till exempelyttranden i grupp. Varje yttrande bör väljas noggrant för hur det skiljer sig från exempel som redan finns i avsikten. 

1. På sidan avsiktsinformation anger du ett relevant uttryck som `Deliver a large cheese pizza` du förväntar dig av användarna, till exempel i textrutan under avsiktsnamnet, och trycker sedan på Retur.
 
    > [!div class="mx-imgBorder"]
    > ![Skärmbild av informationssidan för avsikter, med uttryck markerat](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png) 

    LUIS konverterar alla yttranden till gemener och lägger till blanksteg runt [token,](luis-language-support.md#tokenization) till exempel bindestreck.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Fel vid avsiktspremiss 

Ett exempel yttrande i en avsikt kan ha en avsikt förutsägelse fel mellan avsikten exemplet yttrande är för närvarande i och avsikten bestäms under utbildningen. 

Om du vill hitta uttrycksförutsägelsfel och åtgärda dem använder du **filteralternativen** för felaktig och otydlig i kombination med alternativet **Visa** **i detaljerad vy**. 

![Om du vill hitta förutsägelsefel för uttryck och åtgärda dem använder du alternativet Filter.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

När filter och vy tillämpas, och det finns exempel yttranden med fel, visar exempelutsynslistan yttranden yttranden yttranden och problemen.

> [!div class="mx-imgBorder"]
> ![! [När filter och vy används och det finns exempelyttranden med fel, visar exempelutsiktslistan yttranden yttranden yttranden och problemen.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

Varje rad visar den aktuella träningens förutsägelsepoäng för exempelutsägelsan, den närmaste rivalens poäng, vilket är skillnaden i dessa två poäng. 

### <a name="fixing-intents"></a>Fastställande avsikter

Om du vill veta hur du åtgärdar fel för avsiktsprediktion använder du [översiktsinstrumentpanelen sammanfattning](luis-how-to-use-dashboard.md). Sammanfattningsinstrumentpanelen innehåller analyser för den aktiva versionens senaste träning och ger de bästa förslagen för att åtgärda din modell.  

## <a name="using-the-contextual-toolbar"></a>Använda det kontextuella verktygsfältet

Kontextverktygsfältet innehåller andra åtgärder:

* Redigera eller ta bort exempelyttrande
* Tilldela om exempelutsikt till en annan avsikt
* Filter och vyer: visa bara yttranden som innehåller filtrerade entiteter eller visa valfri information
* Sök igenom exempelyttranden

## <a name="train-your-app-after-changing-model-with-intents"></a>Träna din app efter att ha bytt modell med avsikter

När du har lagt till, redigerat eller tagit bort avsikter [tränar](luis-how-to-train.md) och [publicerar](luis-how-to-publish-app.md) du appen så att ändringarna tillämpas på slutpunktsfrågor. Träna inte efter varje enskild förändring. Träna efter en grupp ändringar. 

## <a name="next-steps"></a>Nästa steg

Läs mer om att lägga till [exempelyttranden](luis-how-to-add-example-utterances.md) med entiteter. 

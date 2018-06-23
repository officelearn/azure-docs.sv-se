---
title: Träna appen THOMAS - Azure | Microsoft Docs
description: Använd språket förstå (THOMAS) för att träna din modell.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 4593954e9e0a60beaa5ee86df848f908b23c6b20
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355431"
---
# <a name="train-your-luis-app"></a>Träna THOMAS appen

Utbildning är lära appen språk förstå (THOMAS) för att förbättra sin kunskap för naturligt språk. Träna THOMAS appen efter uppdateringar till modellen, till exempel att lägga till, redigera, etikettering eller ta bort entiteter, avsikter eller utterances. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Utbildning och [testning](luis-concept-test.md) en app är en återkommande process. När du tränar THOMAS appen testa du den med exempel utterances att se om avsikter och enheter identifieras korrekt. Om de inte göra uppdateringar till THOMAS appen, tåg och testa igen. 

## <a name="train-your-app"></a>Träna din app
Om du vill starta återkommande process, måste du först träna appen THOMAS minst en gång. Kontrollera att varje avsikt har minst en utterance innan utbildning.

1. Åtkomst till din app genom att markera namnet på den **Mina appar** sidan. 

2. I appen, Välj **Train** i den övre panelen. 

    ![Train-knappen](./media/luis-how-to-train/train-button.png)

3. När utbildning är klar, visas en grön meddelandefält längst upp i webbläsaren.

    ![Sidan tåg och testa appen](./media/luis-how-to-train/train-success.png)

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Om du har en eller flera avsikter i din app som inte innehåller exempel utterances, kan du träna din app. Lägg till utterances för alla avsikter. Mer information finns i [lägger du till exempel utterances](luis-how-to-add-example-utterances.md).

## <a name="next-steps"></a>Nästa steg

* [Etiketten föreslagna utterances med THOMAS](Label-Suggested-Utterances.md) 
* [Använda funktioner för att förbättra appen THOMAS prestanda](luis-how-to-add-features.md) 
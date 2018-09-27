---
title: Träna din LUIS-version
titleSuffix: Azure Cognitive Services
description: Utbildning är lära din appversion för Språkförståelse (LUIS) att förbättra förståelse för naturliga språk. Träna LUIS-appen efter uppdateringar till modellen, till exempel att lägga till, redigera, etiketter eller ta bort entiteter, avsikter eller yttranden.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: f27716cc416b162a5b2df5542d709058f3b3e903
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182050"
---
# <a name="train-your-luis-app-version"></a>Träna dina LUIS appversion

Utbildning är lära appen Språkförståelse (LUIS) för att förbättra förståelse för naturliga språk. Träna LUIS-appen efter uppdateringar till modellen, till exempel att lägga till, redigera, etiketter eller ta bort entiteter, avsikter eller yttranden. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Utbildning och [testning](luis-concept-test.md) en app är en iterativ process. När du tränar LUIS-appen testa du den med exempel yttranden om avsikter och entiteter identifieras korrekt. Om de inte gör uppdateringar till LUIS-app, träna och testa igen. 

## <a name="how-to-train"></a>Hur du tränar
Om du vill starta iterativ process, måste du först träna LUIS-appen minst en gång. Kontrollera att varje avsikt har minst ett uttryck innan utbildning.

1. Få åtkomst till din app genom att välja dess namn på den **Mina appar** sidan. 

2. I din app, väljer **träna** i den övre panelen. 

3. När utbildning är klar, visas en grön meddelandefältet överst i webbläsaren.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Om du har en eller flera avsikter i din app som inte innehåller exempel yttranden, kan du träna din app. Lägg till yttranden för alla dina avsikter. Mer information finns i [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Träna med alla data
Utbildning använder en liten andel av negativa sampling. Om du vill använda alla data i stället för små negativt sampling kan använda den [Version inställningar API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med den `UseAllTrainingData` inställd på Sant för att inaktivera den här funktionen. 

## <a name="next-steps"></a>Nästa steg

* [Etiketten föreslagna yttranden med LUIS](luis-how-to-review-endoint-utt.md) 
* [Använda funktioner för att förbättra prestanda för dina LUIS-app](luis-how-to-add-features.md) 
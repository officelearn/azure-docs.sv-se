---
title: Träna appen
titleSuffix: Language Understanding - Azure Cognitive Services
description: Utbildning är lära din appversion för Språkförståelse (LUIS) att förbättra förståelse för naturliga språk. Träna LUIS-appen efter uppdateringar till modellen, till exempel att lägga till, redigera, etiketter eller ta bort entiteter, avsikter eller yttranden.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 57829d5656a49d4266ad56ea6e7605ac25420c95
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218962"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Träna din aktiva version av LUIS-app 

Utbildning är lära appen Språkförståelse (LUIS) för att förbättra förståelse för naturliga språk. Träna LUIS-appen efter uppdateringar till modellen, till exempel att lägga till, redigera, etiketter eller ta bort entiteter, avsikter eller yttranden. 

<!--
When you train a LUIS app by example, LUIS generalizes from the examples you have labeled, and it learns to recognize the relevant intents and entities. This teaches LUIS to improve classification accuracy in the future. -->

Utbildning och [testning](luis-concept-test.md) en app är en iterativ process. När du tränar LUIS-appen testa du den med exempel yttranden om avsikter och entiteter identifieras korrekt. Om de inte gör uppdateringar till LUIS-app, träna och testa igen. 

Utbildning tillämpas på den aktiva versionen i LUIS-portalen. 

## <a name="how-to-train-interactively"></a>Hur du tränar interaktivt

Starta iterativ process i den [LUIS portal](https://www.luis.ai), måste du först att träna LUIS-appen minst en gång. Kontrollera att varje avsikt har minst ett uttryck innan utbildning.

1. Få åtkomst till din app genom att välja dess namn på den **Mina appar** sidan. 

2. I din app, väljer **träna** i den övre panelen. 

3. När utbildning är klar, visas en grön meddelandefältet överst i webbläsaren.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Om du har en eller flera avsikter i din app som inte innehåller exempel yttranden, kan du träna din app. Lägg till yttranden för alla dina avsikter. Mer information finns i [lägger du till exempel yttranden](luis-how-to-add-example-utterances.md).

## <a name="train-with-all-data"></a>Träna med alla data

Utbildning använder en liten andel av negativa sampling. Om du vill använda alla data i stället för små negativt sampling kan använda den [Version inställningar API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med den `UseAllTrainingData` ange värdet som SANT för att Stäng av den här funktionen. 

## <a name="unnecessary-training"></a>Onödiga utbildning

Du behöver inte träna efter varje enskild ändring. Utbildning bör utföras när en grupp med ändringar tillämpas på modellen och nästa steg som du vill göra är att testa eller publicera. Om du inte behöver testa eller publicera utbildning finns inte behövs. 

## <a name="training-with-the-rest-apis"></a>Utbildning med REST-API: er

Utbildning i LUIS-portalen är ett enda steg för att trycka på den **träna** knappen. Utbildning med REST-API: er är en tvåstegsprocess. Först är att [begär utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) med HTTP POST. Begära det [utbildning status](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) med HTTP Get. 

Du måste avsöka status tills alla modeller har tränas om du vill veta när utbildning har slutförts. 

## <a name="next-steps"></a>Nästa steg

* [Etiketten föreslagna yttranden med LUIS](luis-how-to-review-endoint-utt.md) 
* [Använda funktioner för att förbättra prestanda för dina LUIS-app](luis-how-to-add-features.md) 

---
title: Träna app-LUIS
titleSuffix: Azure Cognitive Services
description: Träning är en process för att lära sig din Language Understanding (LUIS)-app-version för att förbättra sin naturliga språk förståelse. Träna din LUIS-app efter uppdateringar av modellen, till exempel lägga till, redigera, märka eller ta bort entiteter, avsikter eller yttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 259ea23c05f0c0a138ad54b6efd11aad2061cf7a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500224"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Träna din aktiva version av LUIS-appen 

Träning är en process för att lära sig din Language Understanding-app (LUIS) för att förbättra sin naturliga språk förståelse. Träna din LUIS-app efter uppdateringar av modellen, till exempel lägga till, redigera, märka eller ta bort entiteter, avsikter eller yttranden. 

Utbildning och [testning](luis-concept-test.md) av en app är en iterativ process. När du har tränat din LUIS-app testar du den med exempel yttranden för att se om intentarna och entiteterna identifieras korrekt. Om de inte är det kan du göra uppdateringar till LUIS-appen, träna och testa igen. 

Träning tillämpas på den aktiva versionen i LUIS-portalen. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="how-to-train-interactively"></a>Så här tränar du interaktivt

För att starta den iterativa processen i [Luis-portalen](https://www.luis.ai)måste du först träna din Luis-app minst en gång. Se till att varje avsikt har minst en uttryck innan träning.

1. Öppna din app genom att välja namnet på sidan **Mina appar** . 

2. I din app väljer du **träna** på den övre panelen. 

3. När inlärningen är klar visas ett grönt meddelande fält överst i webbläsaren.

<!-- The following note refers to what might cause the error message "Training failed: FewLabels for model: <ModelName>" -->

>[!NOTE]
>Om du har en eller flera avsikter i din app som inte innehåller exempel yttranden kan du inte träna din app. Lägg till yttranden för alla dina avsikter. Mer information finns i [Lägg till exempel yttranden](luis-how-to-add-example-utterances.md).

## <a name="training-date-and-time"></a>Datum och tid för utbildning

Datum och tid för utbildning är GMT + 2. 

## <a name="train-with-all-data"></a>Träna med alla data

Träning använder en liten procent andel av negativ sampling. 

Om du vill använda alla data i stället för den små negativa insamlingen använder du [API: et](#version-settings-api-use-of-usealltrainingdata).

<!--

 or the [LUIS portal setting](#luis-portal-setting-to-use-all-training-data)

### LUIS portal setting to use all training data

!!!IGNITE


-->

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Versions inställningar API-användning av UseAllTrainingData

Använd [API för versions inställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med `UseAllTrainingData` ange True för att inaktivera den här funktionen. 

## <a name="unnecessary-training"></a>Onödig utbildning

Du behöver inte träna efter varje enskild ändring. Träningen bör utföras efter att en grupp av ändringar har tillämpats på modellen och nästa steg du vill göra är att testa eller publicera. Om du inte behöver testa eller publicera behövs ingen utbildning. 

## <a name="training-with-the-rest-apis"></a>Utbildning med REST API: er

Utbildning i LUIS-portalen är ett enda steg där man trycker på knappen **träna** . Utbildning med REST API: er är en två stegs process. Det första är att [begära utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) med http post. Begär sedan [utbildnings status](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) med http get. 

För att veta när inlärningen är klar måste du avsöka statusen tills alla modeller har tränats. 

## <a name="next-steps"></a>Nästa steg

* [Märk föreslagna yttranden med LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Använd funktioner för att förbättra LUIS-appens prestanda](luis-how-to-add-features.md) 

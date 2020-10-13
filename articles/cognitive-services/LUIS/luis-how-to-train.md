---
title: Träna app-LUIS
titleSuffix: Azure Cognitive Services
description: Träning är en process för att lära sig din Language Understanding (LUIS)-app-version för att förbättra sin naturliga språk förståelse. Träna din LUIS-app efter uppdateringar av modellen, till exempel lägga till, redigera, märka eller ta bort entiteter, avsikter eller yttranden.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: a53857061110d5a77ac3d166277e7076f4f7f9c6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91541381"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Träna din aktiva version av LUIS-appen

Träning är en process för att lära sig din Language Understanding-app (LUIS) för att förbättra sin naturliga språk förståelse. Träna din LUIS-app efter uppdateringar av modellen, till exempel lägga till, redigera, märka eller ta bort entiteter, avsikter eller yttranden.

Utbildning och [testning](luis-concept-test.md) av en app är en iterativ process. När du har tränat LUIS-appen testar du den med indataexempel för att se om avsikterna och entiteterna identifieras korrekt. Om de inte är det kan du göra uppdateringar till LUIS-appen, träna och testa igen.

Träning tillämpas på den aktiva versionen i LUIS-portalen.

## <a name="how-to-train-interactively"></a>Så här tränar du interaktivt

För att starta den iterativa processen i [Luis-portalen](https://www.luis.ai)måste du först träna din Luis-app minst en gång. Se till att varje avsikt har minst en uttryck innan träning.

1. Öppna din app genom att välja namnet på sidan **Mina appar** .

1. I din app väljer du **träna** på den övre panelen.

1. När inlärningen är klar visas ett meddelande längst upp i webbläsaren.

## <a name="training-date-and-time"></a>Datum och tid för utbildning

Datum och tid för utbildning är GMT + 2.

## <a name="train-with-all-data"></a>Träna med alla data

Träning använder en liten procent andel av negativ sampling. Om du vill använda alla data i stället för den små negativa insamlingen använder du [API: et](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Versions inställningar API-användning av UseAllTrainingData

Använd [API för versions inställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med `UseAllTrainingData` värdet true för att inaktivera den här funktionen.

## <a name="unnecessary-training"></a>Onödig utbildning

Du behöver inte träna efter varje enskild ändring. Träningen bör utföras efter att en grupp av ändringar har tillämpats på modellen och nästa steg du vill göra är att testa eller publicera. Om du inte behöver testa eller publicera behövs ingen utbildning.

## <a name="training-with-the-rest-apis"></a>Utbildning med REST API: er

Utbildning i LUIS-portalen är ett enda steg där man trycker på knappen **träna** . Utbildning med REST API: er är en två stegs process. Det första är att [begära utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) med http post. Begär sedan [utbildnings status](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) med http get.

För att veta när inlärningen är klar måste du avsöka statusen tills alla modeller har tränats.

## <a name="next-steps"></a>Nästa steg

* [Interaktiv testning](luis-interactive-test.md)
* [Batch-testning](luis-how-to-batch-test.md)

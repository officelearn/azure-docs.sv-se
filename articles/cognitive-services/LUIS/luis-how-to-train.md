---
title: Tågapp - LUIS
titleSuffix: Azure Cognitive Services
description: Utbildning är processen att lära din språk understanding (LUIS) appversion för att förbättra dess naturliga språk förståelse. Träna LUIS-appen efter uppdateringar av modellen, till exempel lägga till, redigera, etikettera eller ta bort entiteter, avsikter eller yttranden.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 1da8ab3015730c6b3e1962301a34b1ad43b1aad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219867"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Träna din aktiva version av LUIS-appen 

Utbildning är processen att lära din språk understanding (LUIS) app för att förbättra dess naturliga språk förståelse. Träna LUIS-appen efter uppdateringar av modellen, till exempel lägga till, redigera, etikettera eller ta bort entiteter, avsikter eller yttranden. 

Utbildning och [testning av](luis-concept-test.md) en app är en iterativ process. När du har tränat LUIS-appen testar du den med exempelyttranden för att se om avsikter och entiteter känns igen korrekt. Om de inte är det gör du uppdateringar av LUIS-appen, tränar och testar igen. 

Utbildning tillämpas på den aktiva versionen i LUIS-portalen. 

## <a name="how-to-train-interactively"></a>Hur man tränar interaktivt

Om du vill starta iterativ processen i [LUIS-portalen](https://www.luis.ai)måste du först träna LUIS-appen minst en gång. Se till att varje avsikt har minst ett uttryck innan träningen.

1. Öppna appen genom att välja dess namn på sidan **Mina appar.** 

1. Välj **Träna** i den övre panelen i appen. 

1. När utbildningen är klar visas ett meddelande högst upp i webbläsaren.

## <a name="training-date-and-time"></a>Datum och tid för utbildning

Träningsdatum och -tid är GMT + 2. 

## <a name="train-with-all-data"></a>Träna med alla data

Utbildning använder en liten andel av negativa provtagning. Om du vill använda alla data i stället för det lilla negativa kommplet använder du [API:et](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Api-användning av AnvändningAllTrainingData

Använd [API:et för versionsinställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med `UseAllTrainingData` den inställda på true för att inaktivera den här funktionen. 

## <a name="unnecessary-training"></a>Onödig utbildning

Du behöver inte träna efter varje enskild förändring. Utbildning bör göras när en grupp av ändringar tillämpas på modellen, och nästa steg du vill göra är att testa eller publicera. Om du inte behöver testa eller publicera är utbildning inte nödvändig. 

## <a name="training-with-the-rest-apis"></a>Utbildning med REST API:er

Utbildning i LUIS-portalen är ett enda steg för att trycka på **tågknappen.** Utbildning med REST API:er är en tvåstegsprocess. Den första är att [begära utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) med HTTP POST. Begär sedan [träningsstatus](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) med HTTP Get. 

För att veta när utbildningen är klar måste du avsöka statusen tills alla modeller har tränats. 

## <a name="next-steps"></a>Nästa steg

* [Interaktiv testning](luis-interactive-test.md)
* [Batch-testning](luis-how-to-batch-test.md)

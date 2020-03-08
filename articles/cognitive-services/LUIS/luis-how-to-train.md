---
title: Träna app-LUIS
titleSuffix: Azure Cognitive Services
description: Utbildning är lära din appversion för Språkförståelse (LUIS) att förbättra förståelse för naturliga språk. Träna LUIS-appen efter uppdateringar till modellen, till exempel att lägga till, redigera, etiketter eller ta bort entiteter, avsikter eller yttranden.
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
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361168"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Träna din aktiva version av LUIS-appen 

Utbildning är lära appen Språkförståelse (LUIS) för att förbättra förståelse för naturliga språk. Träna LUIS-appen efter uppdateringar till modellen, till exempel att lägga till, redigera, etiketter eller ta bort entiteter, avsikter eller yttranden. 

Utbildning och [testning](luis-concept-test.md) av en app är en iterativ process. När du tränar LUIS-appen testa du den med exempel yttranden om avsikter och entiteter identifieras korrekt. Om de inte gör uppdateringar till LUIS-app, träna och testa igen. 

Utbildning tillämpas på den aktiva versionen i LUIS-portalen. 

## <a name="how-to-train-interactively"></a>Hur du tränar interaktivt

För att starta den iterativa processen i [Luis-portalen](https://www.luis.ai)måste du först träna din Luis-app minst en gång. Kontrollera att varje avsikt har minst ett uttryck innan utbildning.

1. Öppna din app genom att välja namnet på sidan **Mina appar** . 

1. I din app väljer du **träna** på den övre panelen. 

1. När inlärningen är klar visas ett meddelande längst upp i webbläsaren.

## <a name="training-date-and-time"></a>Datum och tid för utbildning

Datum och tid för utbildning är GMT + 2. 

## <a name="train-with-all-data"></a>Träna med alla data

Utbildning använder en liten andel av negativa sampling. Om du vill använda alla data i stället för den små negativa insamlingen använder du [API: et](#version-settings-api-use-of-usealltrainingdata).

### <a name="version-settings-api-use-of-usealltrainingdata"></a>Versions inställningar API-användning av UseAllTrainingData

Använd [API för versions inställningar](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) med `UseAllTrainingData` ange True för att inaktivera den här funktionen. 

## <a name="unnecessary-training"></a>Onödiga utbildning

Du behöver inte träna efter varje enskild ändring. Utbildning bör utföras när en grupp med ändringar tillämpas på modellen och nästa steg som du vill göra är att testa eller publicera. Om du inte behöver testa eller publicera utbildning finns inte behövs. 

## <a name="training-with-the-rest-apis"></a>Utbildning med REST-API: er

Utbildning i LUIS-portalen är ett enda steg där man trycker på knappen **träna** . Utbildning med REST-API: er är en tvåstegsprocess. Det första är att [begära utbildning](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) med http post. Begär sedan [utbildnings status](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) med http get. 

Du måste avsöka status tills alla modeller har tränas om du vill veta när utbildning har slutförts. 

## <a name="next-steps"></a>Nästa steg

* [Interaktiv testning](luis-interactive-test.md)
* [Batch-testning](luis-how-to-batch-test.md)

---
title: Granska endpoint yttranden att använda aktiv inlärning i Språkförståelse (LUIS)
titleSuffix: Azure Cognitive Services
description: Aktiv inlärning är en av tre strategier för att förbättra förutsägelsefunktionen och enklast. Med aktiv inlärning, din granska endpoint yttranden för rätt avsikt och entiteten. LUIS väljer endpoint yttranden som det är osäker på.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 78cc2a8a2b9295654d0c6264cbf4a4d634b16544
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038178"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Aktivera aktiv inlärning genom att granska endpoint yttranden
Aktiv inlärning är en av tre strategier för att förbättra förutsägelsefunktionen och enklast. Med aktiv inlärning, din granska endpoint yttranden för rätt avsikt och entiteten. LUIS väljer endpoint yttranden som det är osäker på.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning
Aktiv inlärning är en tvåstegsprocess. Först väljer LUIS yttranden som tas emot i appens slutpunkten som behöver verifiering. Det andra steget utförs av appägare eller deltagare för att verifiera de valda yttranden för [granska](luis-how-to-review-endoint-utt.md), inklusive rätt avsikten och alla entiteter i avsikten. När du har granskat talade, träna och publicera appen igen. 

## <a name="which-utterances-are-on-the-review-list"></a>Vilka yttranden finns med i listan för granskning
LUIS lägger till yttranden granska listan när upp aktiveringen syftet har en låg poäng eller övre två avsikter är för Stäng. 

## <a name="single-pool-for-utterances-per-app"></a>Enda pool för yttranden per app
Den **granska endpoint yttranden** listan inte ändras beroende på vilken version. Det finns en enstaka pool med yttranden att granska, oavsett versionen av de yttranden du aktivt redigerar eller versionen av den app som publicerades på slutpunkten. 

## <a name="where-are-the-utterances-from"></a>Var finns yttranden från
Slutpunkten yttranden är hämtade från slutanvändarens frågor på programmets HTTP-slutpunkt. Om din app publiceras inte eller har inte tagits emot träffar ännu kan har du inte yttranden att granska. Om ingen slutpunkt träffar tas emot för en specifik avsikt eller enhet som har du inte yttranden att granska som de. 

## <a name="schedule-review-periodically"></a>Schemalägga Granska regelbundet
Granska föreslagna yttranden behöver inte göras varje dag men bör vara en del av din regelbundet underhåll av LUIS. 

## <a name="delete-review-items-programmatically"></a>Ta bort granska objekt via programmering
Om din app är stor, kan du välja att granska vissa yttranden och ta bort resten från listan över programmässigt. Detta görs genom att först [komma](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) i listan och sedan [tar bort](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) yttranden efter-ID.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granska](luis-how-to-review-endoint-utt.md) endpoint yttranden

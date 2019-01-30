---
title: Granska användare uttryck
titleSuffix: Language Understanding - Azure Cognitive Services
description: Med aktiv inlärning, din granska endpoint yttranden för rätt avsikt och entiteten. LUIS väljer endpoint yttranden som det är osäker på.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: diberry
ms.openlocfilehash: b33d9d6d879de0cb131bed2a215237556f37920f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55218563"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Begrepp för att aktivera aktiv inlärning genom att granska endpoint yttranden
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
Använd den **[ta bort omärkta yttranden](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** API. Säkerhetskopiera dessa yttranden före borttagning av  **[exportera loggfilerna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granska](luis-how-to-review-endoint-utt.md) endpoint yttranden

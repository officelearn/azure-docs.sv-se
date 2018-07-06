---
title: Granska endpoint yttranden att använda aktiv inlärning i Språkförståelse (LUIS) – Azure | Microsoft Docs
description: Använda funktionen aktiv inlärning med namnet ”granska endpoint yttranden' att förbättra prestanda förutsägelser snabbare.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 06/08/2018
ms.author: v-geberr;
ms.openlocfilehash: e416eec29ff9f4ac96eabf11c87424abeba0c75b
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37855725"
---
# <a name="enable-active-learning-by-reviewing-endpoint-utterances"></a>Aktivera aktiv inlärning genom att granska endpoint yttranden
Aktiv inlärning är en av tre strategier för att förbättra förutsägelsefunktionen och enklast. 

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning
Aktiv inlärning är en tvåstegsprocess. Först väljer LUIS yttranden som tas emot i appens slutpunkten som behöver verifiering. Det andra steget utförs av appägare eller deltagare för att verifiera de valda yttranden för [granska](label-suggested-utterances.md), inklusive rätt avsikten och alla entiteter i avsikten. När du har granskat talade, träna och publicera appen igen. 

## <a name="which-utterances-are-on-the-review-list"></a>Vilka yttranden finns med i listan för granskning
LUIS lägger till yttranden granska listan när upp aktiveringen syftet har en låg poäng eller övre två avsikter är för Stäng. 

## <a name="single-pool-for-utterances-per-app"></a>Enda pool för yttranden per app
Den **granska endpoint yttranden** listan inte ändras beroende på vilken version. Det finns en enda pool med yttranden att granska, oavsett vilken version uttryck som du aktivt redigerar eller vilken version av appen har publicerats på slutpunkten. 

## <a name="where-are-the-utterances-from"></a>Var finns yttranden från
Slutpunkten yttranden är hämtade från slutanvändarens frågor på programmets HTTP-slutpunkt. Om din app publiceras inte eller har inte tagits emot träffar ännu kan har du inte yttranden att granska. Om ingen slutpunkt träffar tas emot för en specifik avsikt eller enhet som har du inte yttranden att granska som de. 

## <a name="schedule-review-periodically"></a>Schemalägga Granska regelbundet
Granska föreslagna yttranden behöver inte göras varje dag men bör vara en del av din regelbundet underhåll av LUIS. 

## <a name="delete-review-items-programmatically"></a>Ta bort granska objekt via programmering
Om din app är stor, kan du välja att granska vissa yttranden och ta bort resten från listan över programmässigt. Detta görs genom att först [komma](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) i listan och sedan [tar bort](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) yttranden efter-ID.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granska](Label-Suggested-Utterances.md) endpoint yttranden

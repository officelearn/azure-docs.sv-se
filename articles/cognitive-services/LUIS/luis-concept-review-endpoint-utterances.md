---
title: Granska användarens uttryck-LUIS
titleSuffix: Azure Cognitive Services
description: Med aktiva inlärnings yttranden för gransknings slut punkten för korrekt avsikt och entitet. LUIS väljer slut punkt yttranden den är osäker på.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 772d2bdff0a5e1c6cd999482ec4ef6be29dc2bb1
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486782"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Begrepp för att aktivera aktiv inlärning genom att granska slut punkts yttranden
Active Learning är en av tre strategier för att förbättra förutsägelse noggrannhet och det enklaste att implementera. Med aktiva inlärnings yttranden för gransknings slut punkten för korrekt avsikt och entitet. LUIS väljer slut punkt yttranden den är osäker på.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning
Aktiv inlärning är en två stegs process. Först väljer LUIS yttranden som tas emot i appens slut punkt som behöver verifieras. Det andra steget utförs av appens ägare eller medarbetare för att verifiera den valda yttranden för [granskning](luis-how-to-review-endpoint-utterances.md), inklusive rätt avsikt och alla entiteter i avsikten. När du har granskat yttranden kan du träna och publicera appen igen. 

## <a name="which-utterances-are-on-the-review-list"></a>Vilka yttranden finns i gransknings listan
LUIS lägger till yttranden i gransknings listan när den främsta stängnings metoden har en låg poäng eller om de två viktigaste poängen är för nära. 

## <a name="single-pool-for-utterances-per-app"></a>En pool för yttranden per app
Yttranden-listan för **gransknings slut punkten** ändras inte utifrån versionen. Det finns en enstaka pool med yttranden att granska, oavsett versionen av de yttranden du aktivt redigerar eller versionen av den app som publicerades på slutpunkten. 

## <a name="where-are-the-utterances-from"></a>Var finns yttranden från
Slut punkts yttranden hämtas från slut användar frågor på programmets HTTP-slutpunkt. Om din app inte har publicerats eller inte har fått några träffar än, har du inga yttranden att granska. Om inga slut punkts träffar tas emot för en speciell avsikt eller entitet har du inte yttranden att granska de som innehåller dem. 

## <a name="schedule-review-periodically"></a>Schemalägg granskning regelbundet
Det går inte att granska föreslagna yttranden varje dag utan bör vara en del av ditt vanliga underhåll av LUIS. 

## <a name="delete-review-items-programmatically"></a>Ta bort gransknings objekt program mässigt
Använd yttranden-API: et **[ta bort omärkta](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Säkerhetskopiera dessa yttranden innan du tar bort dem genom att **[Exportera loggfilerna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granskar](luis-how-to-review-endpoint-utterances.md) slut punkts yttranden

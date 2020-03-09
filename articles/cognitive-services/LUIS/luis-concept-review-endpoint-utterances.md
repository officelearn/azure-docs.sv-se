---
title: Granska användarens uttryck-LUIS
titleSuffix: Azure Cognitive Services
description: Med aktiv inlärning, din granska endpoint yttranden för rätt avsikt och entiteten. LUIS väljer endpoint yttranden som det är osäker på.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: diberry
ms.openlocfilehash: 375d4b4e7c3fcafbdfde1ff447bedc3e16aff2f2
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393789"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Begrepp för att aktivera aktiv inlärning genom att granska endpoint yttranden
Aktiv inlärning är en av tre strategier för att förbättra förutsägelsefunktionen och enklast. Med aktiv inlärning, din granska endpoint yttranden för rätt avsikt och entiteten. LUIS väljer endpoint yttranden som det är osäker på.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning
Aktiv inlärning är en tvåstegsprocess. Först väljer LUIS yttranden som tas emot i appens slutpunkten som behöver verifiering. Det andra steget utförs av appens ägare eller medarbetare för att verifiera den valda yttranden för [granskning](luis-how-to-review-endpoint-utterances.md), inklusive rätt avsikt och alla entiteter i avsikten. När du har granskat talade, träna och publicera appen igen.

## <a name="which-utterances-are-on-the-review-list"></a>Vilka yttranden finns med i listan för granskning
LUIS lägger till yttranden granska listan när upp aktiveringen syftet har en låg poäng eller övre två avsikter är för Stäng.

## <a name="single-pool-for-utterances-per-app"></a>Enda pool för yttranden per app
Yttranden-listan för **gransknings slut punkten** ändras inte utifrån versionen. Det finns en enstaka pool med yttranden att granska, oavsett versionen av de yttranden du aktivt redigerar eller versionen av den app som publicerades på slutpunkten.

## <a name="where-are-the-utterances-from"></a>Var finns yttranden från
Slutpunkten yttranden är hämtade från slutanvändarens frågor på programmets HTTP-slutpunkt. Om din app publiceras inte eller har inte tagits emot träffar ännu kan har du inte yttranden att granska. Om ingen slutpunkt träffar tas emot för en specifik avsikt eller enhet som har du inte yttranden att granska som de.

## <a name="schedule-review-periodically"></a>Schemalägga Granska regelbundet
Granska föreslagna yttranden behöver inte göras varje dag men bör vara en del av din regelbundet underhåll av LUIS.

## <a name="delete-review-items-programmatically"></a>Ta bort granska objekt via programmering
Använd yttranden-API: et **[ta bort omärkta](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Säkerhetskopiera dessa yttranden innan du tar bort dem genom att **[Exportera loggfilerna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)** .

## <a name="enable-active-learning"></a>Aktivera aktiv inlärning

Om du vill aktivera aktiv inlärning måste du logga användar frågor. Detta åstadkommer du genom att anropa [slut punkts frågan](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) med parametern och värdet för `log=true` QueryString.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granskar](luis-how-to-review-endpoint-utterances.md) slut punkts yttranden

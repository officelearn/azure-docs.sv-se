---
title: Granska användarutsikt - LUIS
titleSuffix: Azure Cognitive Services
description: Med aktiv inlärning, din översyn slutpunkt yttranden för korrekt avsikt och entitet. LUIS väljer slutpunkt yttranden som den är osäker på.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219944"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Begrepp för att aktivera aktivt lärande genom att granska slutpunktsyttranden
Aktivt lärande är en av tre strategier för att förbättra förutsägelsenoggrannhet och det enklaste att genomföra. Med aktiv inlärning, din översyn slutpunkt yttranden för korrekt avsikt och entitet. LUIS väljer slutpunkt yttranden som den är osäker på.

## <a name="what-is-active-learning"></a>Vad är aktivt lärande
Aktivt lärande är en tvåstegsprocess. Först väljer LUIS yttranden som tas emot vid appens slutpunkt som behöver valideras. Det andra steget utförs av appägaren eller medarbetaren för att validera de valda yttrandena för [granskning](luis-how-to-review-endpoint-utterances.md), inklusive rätt avsikt och alla entiteter som finns i avsikten. När du har granskat yttrandena tränar och publicerar du appen igen.

## <a name="which-utterances-are-on-the-review-list"></a>Vilka yttranden som finns på granskningslistan
LUIS lägger till yttranden i granskningslistan när den översta bränning avsikten har en låg poäng eller de två bästa avsikter poäng är för nära.

## <a name="single-pool-for-utterances-per-app"></a>En pool för yttranden per app
Listan **Granska slutpunktsyttranden** ändras inte baserat på versionen. Det finns en enstaka pool med yttranden att granska, oavsett versionen av de yttranden du aktivt redigerar eller versionen av den app som publicerades på slutpunkten.

## <a name="where-are-the-utterances-from"></a>Var är yttrandena från
Slutpunktsyttranden hämtas från slutanvändarfrågor på programmets HTTP-slutpunkt. Om din app inte har publicerats eller inte har fått träffar ännu har du inga yttranden att granska. Om inga slutpunktsträffar tas emot för en viss avsikt eller entitet har du inga yttranden att granska som innehåller dem.

## <a name="schedule-review-periodically"></a>Schemalägg granskning med jämna mellanrum
Granska föreslagna yttranden behöver inte göras varje dag, men bör vara en del av ditt regelbundna underhåll av LUIS.

## <a name="delete-review-items-programmatically"></a>Ta bort granskningsobjekt programmässigt
Använd **[API:et för ta bort omärkta yttranden.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** Säkerhetskopiera dessa yttranden innan de tas bort genom **[att exportera loggfilerna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Aktivera aktivt lärande

Om du vill aktivera aktiv inlärning måste du logga användarfrågor. Detta åstadkoms genom att anropa `log=true` [slutpunktsfrågan](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) med frågesträngsparametern och värdet.

## <a name="next-steps"></a>Nästa steg

* Läs om hur du [granskar](luis-how-to-review-endpoint-utterances.md) slutpunktsyttranden

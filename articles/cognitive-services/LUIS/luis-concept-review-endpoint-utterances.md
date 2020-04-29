---
title: Granska användarens uttryck-LUIS
description: Med aktiva inlärnings yttranden för gransknings slut punkten för korrekt avsikt och entitet. LUIS väljer slut punkt yttranden den är osäker på.
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 8d267fc441dc2cbf7f8ae3746486d5e7be55f135
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80546858"
---
# <a name="concepts-for-enabling-active-learning-by-reviewing-endpoint-utterances"></a>Begrepp för att aktivera aktiv inlärning genom att granska slut punkts yttranden
Active Learning är en av tre strategier för att förbättra förutsägelse noggrannhet och det enklaste att implementera. Med aktiva inlärnings yttranden för gransknings slut punkten för korrekt avsikt och entitet. LUIS väljer slut punkt yttranden den är osäker på.

## <a name="what-is-active-learning"></a>Vad är aktiv inlärning
Aktiv inlärning är en två stegs process. Först väljer LUIS yttranden som tas emot i appens slut punkt som behöver verifieras. Det andra steget utförs av appens ägare eller medarbetare för att verifiera den valda yttranden för [granskning](luis-how-to-review-endpoint-utterances.md), inklusive rätt avsikt och alla entiteter i avsikten. När du har granskat yttranden kan du träna och publicera appen igen.

## <a name="which-utterances-are-on-the-review-list"></a>Vilka yttranden finns i gransknings listan
LUIS lägger till yttranden i gransknings listan när den främsta stängnings metoden har en låg poäng eller om de två viktigaste poängen är för nära.

## <a name="single-pool-for-utterances-per-app"></a>En pool för yttranden per app
Yttranden-listan för **gransknings slut punkten** ändras inte utifrån versionen. Det finns en enstaka pool med yttranden att granska, oavsett versionen av de yttranden du aktivt redigerar eller versionen av den app som publicerades på slutpunkten.

I [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/58b6f32139e2bb139ce823c9)är versions namnet obligatoriskt och måste finnas i programmet, men används inte utöver den verifieringen. Gransknings yttranden gäller för hela programmet. Om du tar bort yttranden från en _version_påverkas alla versioner.

## <a name="where-are-the-utterances-from"></a>Var finns yttranden från
Slut punkts yttranden hämtas från slut användar frågor på programmets HTTP-slutpunkt. Om din app inte har publicerats eller inte har fått några träffar än, har du inga yttranden att granska. Om inga slut punkts träffar tas emot för en speciell avsikt eller entitet har du inte yttranden att granska de som innehåller dem.

## <a name="schedule-review-periodically"></a>Schemalägg granskning regelbundet
Det går inte att granska föreslagna yttranden varje dag utan bör vara en del av ditt vanliga underhåll av LUIS.

## <a name="delete-review-items-programmatically"></a>Ta bort gransknings objekt program mässigt
Använd yttranden-API: et **[ta bort omärkta](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9)** . Säkerhetskopiera dessa yttranden innan du tar bort dem genom att **[Exportera loggfilerna](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36)**.

## <a name="enable-active-learning"></a>Aktivera aktiv inlärning

Om du vill aktivera aktiv inlärning måste du logga användar frågor. Detta åstadkommer du genom att anropa [slut punkts frågan](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) med parametern `log=true` QueryString och värdet.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [granskar](luis-how-to-review-endpoint-utterances.md) slut punkts yttranden

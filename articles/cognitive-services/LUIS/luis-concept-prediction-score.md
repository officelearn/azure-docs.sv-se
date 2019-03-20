---
title: Poäng för förutsägelse
titleSuffix: Language Understanding - Azure Cognitive Services
description: En förutsägelse poäng indikerar grad av säkerhet som LUIS-API-tjänsten har för förutsägelseresultat baserat på en användare-uttryck.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121765"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Resultat för förutsägelse anger prognosens noggrannhet för avsikt och entiteter

En förutsägelse poäng indikerar grad av säkerhet som LUIS har för förutsägelseresultat baserat på en användare-uttryck.

En förutsägelse poäng är mellan noll (0) och en (1). Ett exempel på en mycket säker på LUIS-poäng är 0,99. Ett exempel på en poäng med låg säkerhet är 0,01. 

|Poäng värde|Konfidensbedömning|
|--|--|
|1|visst matchning|
|0,99|hög exakthet|
|0.01|låg förtroende|
|0|visst gick inte att matcha|

När ett uttryck resulterar i ett låga förtroenderesultat, LUIS markeras som i den [LUIS](luis-reference-regions.md) webbplats **avsikt** sida med den identifierade **märkta avsikt** med röd.

![Poäng avvikelse](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>TOP-bedömning avsikt

Varje uttryck förutsägelse returnerar en top-bedömning avsikt. Den här förutsägelse är en numerisk jämförelse av förutsägelse poäng. Främsta 2 poängen kan ha en mycket liten skillnaden mellan dem. LUIS anger inte den här närhet än returnerar den övre poängen.  

## <a name="return-prediction-score-for-all-intents"></a>Returnera förutsägelse poäng för alla avsikter

Ett test- eller slutpunkten resultat kan innehålla alla avsikter. Den här konfigurationen är inställd på den [endpoint](https://aka.ms/v1-endpoint-api-docs) med den `verbose=true` fråga sträng namn/värde-par.

## <a name="review-intents-with-similar-scores"></a>Granska avsikter med liknande resultat

Granska resultatet för alla avsikter är ett bra sätt att kontrollera att inte bara identifieras korrekt avsikten, men som nästa identifierat avsikts poäng är betydligt lägre konsekvent för yttranden.

Om flera avsikter har Stäng förutsägelse resultat, baserat på kontexten för ett uttryck kan LUIS växla mellan avsikter. För att åtgärda den här situationen fortsätta Lägg till yttranden till varje avsikt med en mängd olika sammanhangsberoende skillnader eller du kan klientprogram, till exempel en chattrobot, så programmässiga beslut om hur du hanterar 2 övre avsikter.

2 avsikter som poängsätts för-nära kan Invertera på grund av icke-deterministisk utbildning. Den främsta poängen kan bli andra upp och att andra översta resultatet kan bli den första översta poängen. För att förhindra att den här situationen, lägger du till exempel yttranden till var och en av de översta två avsikterna för den uttryck med word valmöjligheter och kontext som särskiljer 2 avsikter. Två avsikter ska ha om samma antal exempel yttranden. En tumregel för separering att förhindra invertering på grund av utbildning, är 15% skillnad i poäng.

Du kan stänga av den icke-deterministisk utbildningen av [utbildning med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Skillnader med förutsägelser mellan olika utbildningssessioner

När du tränar samma i en annan app, och de är inte samma, är den här skillnaden eftersom det är icke-deterministisk utbildning (ett element i slumpmässighet). För det andra innebär någon överlappning av ett uttryck till mer än en avsikt främsta syftet för samma uttryck kan ändras baserat på utbildning.

Om din chattrobot kräver en specifik LUIS poäng att ange förtroende för en avsikt, bör du använda poäng skillnaden mellan de två översta avsikterna. Den här situationen ger flexibilitet för utbildning.

## <a name="e-exponent-notation"></a>E (exponent)-format

Förutsägelse poäng kan använda exponent notation *visas* ovan 0 – 1 intervall, till exempel `9.910309E-07`. Det här resultatet är en indikation på en mycket **små** tal.

|E-notering poäng |Faktiska poäng|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Skiljetecken

Skiljetecken är en separat token i LUIS. Ett uttryck som innehåller en punkt i slutet jämfört med ett uttryck som inte innehåller en punkt i slutet är två separata yttranden och kan få två olika förutsägelser. Kontrollera att modellen hanterar skiljetecken antingen i den [exempel yttranden](luis-concept-utterance.md) (med och inte har något skiljetecken) eller i den [mönster](luis-concept-patterns.md) där det är enklare att Ignorera skiljetecken med särskild syntax: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Nästa steg

Se [Lägg till entiteter](luis-how-to-add-entities.md) mer information om hur du lägger till entiteter i din LUIS-app.

---
title: Förutsägelse resultat – LUIS
titleSuffix: Azure Cognitive Services
description: Ett förutsägelse Poäng visar graden av exakthet som LUIS-API-tjänsten har för förutsägelse resultat baserat på en användares uttryck.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: ccbdb60687bd81de57ff675634f9d124f10784e1
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68563956"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Förutsägelse poängen visar förutsägelse noggrannhet för avsikt och entiteter

Ett förutsägelse Poäng visar graden av Tillförlitlighets LUIS som har förutsägelse resultat baserat på en användares uttryck.

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

Varje uttryck förutsägelse returnerar en top-bedömning avsikt. Den här förutsägelsen är en numerisk jämförelse av förutsägelse poängen. De 2 främsta poängen kan ha en mycket liten skillnad mellan dem. LUIS indikerar inte det här avståndet, förutom att returnera det översta resultatet.  

## <a name="return-prediction-score-for-all-intents"></a>Returnera förutsägelse poäng för alla avsikter

Ett test- eller slutpunkten resultat kan innehålla alla avsikter. Den här konfigurationen är inställd på den [endpoint](https://aka.ms/v1-endpoint-api-docs) med den `verbose=true` fråga sträng namn/värde-par.

## <a name="review-intents-with-similar-scores"></a>Granska avsikter med liknande resultat

Granska resultatet för alla avsikter är ett bra sätt att kontrollera att inte bara identifieras korrekt avsikten, men som nästa identifierat avsikts poäng är betydligt lägre konsekvent för yttranden.

Om flera avsikter har Stäng förutsägelse resultat, baserat på kontexten för ett uttryck kan LUIS växla mellan avsikter. För att åtgärda den här situationen fortsätter du att lägga till yttranden till varje avsikt med en större mängd olika kontext skillnader eller så kan du ha klient programmet, t. ex. en chatt-robot, göra programmerings alternativ för hur du hanterar de två främsta intentarna.

2 avsikter, som är för nära resultat, kan inverteras på grund av icke-deterministisk utbildning. Den främsta poängen kan bli andra upp och att andra översta resultatet kan bli den första översta poängen. För att förhindra den här situationen lägger du till exempel yttranden i båda de två viktigaste intentarna för uttryck med ordet och kontexten som särskiljer 2 avsikter. Två avsikter ska ha om samma antal exempel yttranden. En tumregel för separering att förhindra invertering på grund av utbildning, är 15% skillnad i poäng.

Du kan inaktivera icke-deterministisk utbildning genom att [träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Skillnader med förutsägelser mellan olika utbildnings möten

När du tränar samma modell i en annan app, och poängen inte är samma, beror det på att det finns en icke-deterministisk utbildning (ett element av slumpmässig het). För det andra innebär någon överlappning av ett uttryck till mer än en avsikt främsta syftet för samma uttryck kan ändras baserat på utbildning.

Om din chatt-robot kräver en viss LUIS Poäng för att tyda på en avsikt, bör du använda Poäng skillnaden mellan de två viktigaste. Den här situationen ger flexibilitet för variationer i träning.

## <a name="e-exponent-notation"></a>E (exponent)-format

Förutsägelse poäng kan använda exponent notation *visas* ovan 0 – 1 intervall, till exempel `9.910309E-07`. Det här resultatet är en indikation på en mycket **små** tal.

|E-notering poäng |Faktiska poäng|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Skiljetecken

Skiljetecken är en separat token i LUIS. En uttryck som innehåller en period i slutet jämfört med en uttryck som inte innehåller en period i slutet är två separata yttranden och kan få två olika förutsägelser. Kontrollera att modellen hanterar skiljetecken antingen i den [exempel yttranden](luis-concept-utterance.md) (med och inte har något skiljetecken) eller i den [mönster](luis-concept-patterns.md) där det är enklare att Ignorera skiljetecken med särskild syntax: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Nästa steg

Se [Lägg till entiteter](luis-how-to-add-entities.md) mer information om hur du lägger till entiteter i din LUIS-app.

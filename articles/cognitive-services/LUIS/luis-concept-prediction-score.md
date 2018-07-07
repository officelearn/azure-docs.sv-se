---
title: Förstå att förutsägelse resultatet som returneras av LUIS - Azure | Microsoft Docs
description: Lär dig vad förutsägelse poäng innebär i LUIS
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 88d5eb22186248024a356610addab0d43f68a961
ms.sourcegitcommit: 11321f26df5fb047dac5d15e0435fce6c4fde663
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/06/2018
ms.locfileid: "37887138"
---
# <a name="prediction-score"></a>Förutsägelseresultat
En förutsägelse poäng indikerar grad av säkerhet som LUIS har för förutsägelser. 

En förutsägelse poäng är vanligtvis mellan noll (0) och en (1). Ett exempel på en mycket säker på LUIS-poäng är 0,99. Ett exempel på en poäng med låg säkerhet är 0,01. 

|Poäng värde|Konfidensbedömning|
|--|--|
|1|visst matchning|
|0,99|hög exakthet|
|0.01|låg förtroende|
|0|visst gick inte att matcha|

När ett uttryck resulterar i ett låga förtroenderesultat, LUIS markeras som i den [LUIS](luis-reference-regions.md) webbplats **avsikt** sida med den identifierade **märkta avsikt** med röd. 

![Poäng avvikelse](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>TOP-bedömning avsikt
Varje uttryck förutsägelse returnerar en top-bedömning avsikt. Det här är en numerisk jämförelse av förutsägelse poäng. Övre två poängen kan ha en mycket liten skillnaden mellan dem. LUIS anger inte den här närhet än returnerar resultat.  

Om du är orolig nära övre poäng, ska du returnera poäng för alla avsikter. Du kan antingen lägga till yttranden två avsikter som anger deras skillnader word urval och arrangemang eller om du kan ha LUIS-anropa program, till exempel en chattrobot, programmässiga tala om hur du hanterar de två översta avsikterna. 

## <a name="return-prediction-score-for-all-intents"></a>Returnera förutsägelse poäng för alla avsikter
Ett test- eller slutpunkten resultat kan innehålla alla avsikter. Den här konfigurationen är inställd på den [endpoint](https://aka.ms/v1-endpoint-api-docs) med den `verbose=true` fråga sträng namn/värde-par. 

## <a name="review-intents-with-similar-scores"></a>Granska avsikter med liknande resultat
Granska resultatet för alla avsikter är ett bra sätt att kontrollera att inte bara identifieras korrekt avsikten, men som nästa identifierat avsikts poäng är betydligt lägre konsekvent för yttranden. 

Om flera avsikter har Stäng förutsägelse resultat, baserat på kontexten för ett uttryck kan LUIS växla mellan avsikter. Lös problemet genom att fortsätta att lägga till yttranden till varje avsikt med en mängd olika sammanhangsberoende skillnader.   

## <a name="e-exponent-notation"></a>E (exponent)-format

Förutsägelse poäng kan använda exponent notation *visas* ovan 0 – 1 intervall, till exempel `9.910309E-07`. Det här resultatet är en indikation på en mycket **små** tal.

|E-notering poäng |Faktiska poäng|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Skillnader med förutsägelser
När du tränar samma i en annan app, och de är inte det här samma, beror det på att det är en del av slumpmässighet i utbildningen. För det andra innebär någon överlappning av ett uttryck till mer än en avsikt främsta syftet för samma uttryck kan ändras baserat på utbildning.

Om din chattrobot kräver specifika LUIS poäng att ange förtroende för en avsikt, bör du istället använda poäng skillnaden mellan de två översta avsikterna. Detta ger flexibilitet för utbildning. 

## <a name="next-steps"></a>Nästa steg

Se [Lägg till entiteter](luis-how-to-add-entities.md) mer information om hur du lägger till entiteter i din LUIS-app.
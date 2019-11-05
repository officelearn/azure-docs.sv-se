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
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 5b8d97005d8f404a296ddb45e92b65e4aa811aa3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486775"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Förutsägelse poängen visar förutsägelse noggrannhet för avsikt och entiteter

Ett förutsägelse Poäng visar graden av Tillförlitlighets LUIS som har förutsägelse resultat för en användares uttryck.

En förutsägelse Poäng är mellan noll (0) och en (1). Ett exempel på en hög trygg LUIS Poäng är 0,99. Ett exempel på en poäng av låg exakthet är 0,01. 

|Poäng värde|tillit|
|--|--|
|1|bestämd matchning|
|0,99|hög exakthet|
|0,01|låg exakthet|
|0|Det gick inte att matcha|

## <a name="top-scoring-intent"></a>Främsta resultat avsikt

Varje uttryck förutsägelse returnerar ett topp-resultat. Den här förutsägelsen är en numerisk jämförelse av förutsägelse poängen. 

## <a name="proximity-of-scores-to-each-other"></a>Närhet av poängen till varandra

De 2 främsta poängen kan ha en mycket liten skillnad mellan dem. LUIS indikerar inte det här avståndet, förutom att returnera det översta resultatet.  

## <a name="return-prediction-score-for-all-intents"></a>Returnera förutsägelse Poäng för alla avsikter

Ett test-eller slut punkts resultat kan innehålla alla avsikter. Den här konfigurationen är inställd på slut punkten med rätt QueryString-namn/värde-par.

|Förutsägelse-API|QueryString-namn|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Granska avsikter med liknande Poäng

Att granska poängen för alla avsikter är ett bra sätt att kontrol lera att det inte bara är rätt avsikt att identifiera, men att nästa identifierade avsikts resultat är betydligt och konsekvent lägre för yttranden.

Om flera avsikter har nära förutsägelse resultat, baserat på kontexten för en uttryck, kan LUIS växla mellan avsikterna. För att åtgärda den här situationen fortsätter du att lägga till yttranden till varje avsikt med en större mängd olika kontext skillnader eller så kan du ha klient programmet, t. ex. en chatt-robot, göra programmerings alternativ för hur du hanterar de två främsta intentarna.

2 avsikter, som är för nära resultat, kan inverteras på grund av **icke-deterministisk utbildning**. Den översta poängen kan bli den andra överst och den andra översta poängen kan bli den första översta poängen. För att förhindra den här situationen lägger du till exempel yttranden i båda de två viktigaste intentarna för uttryck med ordet och kontexten som särskiljer 2 avsikter. De två avsikterna bör ha ungefär samma antal yttranden. En tumregel för separering för att förhindra inversion på grund av utbildning, är en skillnad på 15% i poängen.

Du kan inaktivera **icke-deterministisk utbildning** genom att [träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Skillnader med förutsägelser mellan olika utbildnings möten

När du tränar samma modell i en annan app, och poängen inte är samma, beror det på att det finns en **icke-deterministisk utbildning** (ett element av slumpmässig het). För det andra innebär överlappande av en uttryck till fler än ett avsikt att det främsta syftet med samma uttryck kan ändras baserat på träning.

Om din chatt-robot kräver en viss LUIS Poäng för att tyda på en avsikt, bör du använda Poäng skillnaden mellan de två viktigaste. Den här situationen ger flexibilitet för variationer i träning.

Du kan inaktivera **icke-deterministisk utbildning** genom att [träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>E-notation (exponent)

Förutsägelse resultat kan använda exponent notation, som *visas* ovanför intervallet 0-1, till exempel `9.910309E-07`. Den här poängen är en indikation på ett mycket **litet** tal.

|Resultat för E-notation |Faktisk Poäng|
|--|--|
|9.910309 e-07|.0000009910309|

## <a name="punctuation"></a>skiljetecken

[Lär dig mer](luis-concept-utterance.md#punctuation-marks) om hur du använder eller ignorerar interpunktion. 

## <a name="next-steps"></a>Nästa steg

Se [Lägg till entiteter](luis-how-to-add-entities.md) för att lära dig mer om hur du lägger till entiteter i Luis-appen.

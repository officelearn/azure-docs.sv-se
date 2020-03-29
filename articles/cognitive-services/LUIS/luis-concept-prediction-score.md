---
title: Förutsägelse poäng - LUIS
titleSuffix: Azure Cognitive Services
description: En förutsägelsepoäng anger graden av förtroende som LUIS API-tjänsten har för förutsägelseresultat, baserat på ett användarutseende.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74280815"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>Förutsägelsepoäng indikerar förutsägelsenoggrannhet för avsikt och entiteter

En förutsägelsepoäng anger graden av förtroende LUIS har för förutsägelseresultat av ett användarutseende.

En förutsägelsepoäng är mellan noll (0) och en (1). Ett exempel på en mycket säker LUIS poäng är 0,99. Ett exempel på ett poängv för lågt självförtroende är 0,01. 

|Poängvärde|Konfidensbedömning|
|--|--|
|1|bestämd match|
|0.99|högt förtroende|
|0,01|lågt förtroende|
|0|definitivt underlåtenhet att matcha|

## <a name="top-scoring-intent"></a>Avsikt med högsta poäng

Varje uttryck förutsägelse returnerar en top-scoring avsikt. Denna förutsägelse är en numerisk jämförelse av förutsägelsepoäng. 

## <a name="proximity-of-scores-to-each-other"></a>Närhet av poäng till varandra

De 2 bästa poängen kan ha en mycket liten skillnad mellan dem. LUIS anger inte denna närhet annat än att returnera den högsta poängen.  

## <a name="return-prediction-score-for-all-intents"></a>Returnera förutsägelsepoäng för alla avsikter

Ett test- eller slutpunktsresultat kan innehålla alla avsikter. Den här konfigurationen anges på slutpunkten med rätt frågesträngnamn/värdepar.

|Förutsägelse-API|Namn på frågesträng|
|--|--|
|V3 (på andra)|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Granska avsikter med liknande poäng

Granska poängen för alla avsikter är ett bra sätt att kontrollera att inte bara är rätt avsikt identifieras, men att nästa identifierade avsiktens poäng är betydligt och konsekvent lägre för yttranden.

Om flera avsikter har nära förutsägelsepoäng, baserat på kontexten för ett uttryck, kan LUIS växla mellan avsikterna. För att åtgärda den här situationen, fortsätta att lägga till yttranden till varje avsikt med ett bredare utbud av kontextuella skillnader eller så kan du ha klientprogrammet, till exempel en chattrobot, göra programmatiska val om hur du hanterar de två högsta avsikterna.

De 2 avsikter, som är alltför nära poäng, kan invertera på grund av **icke-deterministisk utbildning**. Den översta poängen kan bli den andra toppen och den andra högsta poängen kan bli den första högsta poängen. För att förhindra den här situationen lägger du till exempelyttranden till var och en av de två främsta avsikterna för det uttrycket med ordval och kontext som skiljer de två avsikterna åt. De två avsikterna bör ha ungefär samma antal exempel yttranden. En tumregel för separation för att förhindra inversion på grund av träning, är en 15% skillnad i poäng.

Du kan stänga av den **icke-deterministiska träningen** genom [att träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Skillnader med förutsägelser mellan olika träningspass

När du tränar samma modell i en annan app, och poängen inte är desamma, beror den här skillnaden på att det finns **icke-deterministisk träning** (ett inslag av slumpmässighet). För det andra innebär varje överlappning av ett uttryck till mer än en avsikt att den översta avsikten för samma uttryck kan ändras baserat på utbildning.

Om din chattrobot kräver en specifik LUIS-poäng för att indikera förtroende för en avsikt, bör du använda poängskillnaden mellan de två bästa avsikter. Denna situation ger flexibilitet för variationer i utbildning.

Du kan stänga av den **icke-deterministiska träningen** genom [att träna med alla data](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>E (exponent) notation

Förutsägelsepoäng kan använda exponent notation, _som visas_ ovanför intervallet `9.910309E-07`0-1, till exempel . Denna poäng är en indikation på ett mycket **litet** antal.

|E-notationspoäng |Faktiska poäng|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>Skiljetecken

[Läs mer](luis-concept-utterance.md#punctuation-marks) om hur du använder eller ignorerar interpunktion. 

## <a name="next-steps"></a>Nästa steg

Se [Lägga till entiteter](luis-how-to-add-entities.md) om du vill veta mer om hur du lägger till entiteter i LUIS-appen.

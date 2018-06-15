---
title: Förstå förutsägelse poängsättningen som returneras av THOMAS - Azure | Microsoft Docs
description: Lär dig vad förutsägelse poäng innebär THOMAS
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 1620fe80b032245c6ca19279c3981dcff4b9820f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356110"
---
# <a name="prediction-score"></a>Förutsägelse poäng
En förutsägelse poäng anger grad av säkerhet som THOMAS har för förutsägelse resultat. 

En förutsägelse poäng brukar mellan noll (0) och en (1). Ett exempel på en säker på att hög THOMAS poäng är 0,99. Ett exempel på ett resultat av låg säkerhet är 0,01. 

|Poäng värde|Konfidensbedömning|
|--|--|
|1|definitiv matchning|
|0,99|hög exakthet|
|0.01|låg förtroende|
|0|definitiv gick inte att matcha|

När en utterance resulterar i en förtroende låg poäng, THOMAS markeras som i den [THOMAS] [ LUIS] webbplats **avsikt** sida med den identifierade **etikett avsikt**  med röd. 

![Poäng avvikelse](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>TOP-bedömningen avsikt
Varje utterance förutsägelse returnerar en top-bedömningen avsikt. Det här är en numerisk jämförelse av förutsägelse resultat. Övre två resultat kan ha en liten skillnaden mellan dem. THOMAS anger inte den här närhet än returnerar resultat.  

Om du är orolig nära översta resultat bör du gå tillbaka poängen för alla avsikter. Kan du antingen lägga till utterances två avsikter som anger deras skillnader med word val och arrangemang eller om du kan ha THOMAS anropar program, till exempel en chatbot programmässiga val om hur du hanterar två översta avsikter. 

## <a name="return-prediction-score-for-all-intents"></a>Returnera förutsägelse poäng för alla avsikter
Ett test- eller slutpunkt resultat kan innehålla alla avsikter. Den här konfigurationen är inställd på den [endpoint](https://aka.ms/v1-endpoint-api-docs) med den `verbose=true` fråga sträng namn/värde-par. 

## <a name="review-intents-with-similar-scores"></a>Granska avsikter med liknande resultat
Granska resultatet för alla avsikter är ett bra sätt att kontrollera att inte bara identifieras korrekt avsikten, men att nästa identifierade avsikts poäng är betydligt lägre konsekvent för utterances. 

Om flera avsikter har Stäng förutsägelse resultat, baserat på en utterance kontext kan THOMAS växla mellan innehållet. Lös problemet genom att fortsätta att lägga till utterances till varje avsikten med en mängd olika kontextuella skillnader.   

## <a name="e-exponent-notation"></a>Notation E (exponenten)

Förutsägelse resultat kan använda exponent notation *visas* ovan 0-1 intervall som `9.910309E-07`. Det här resultatet är en indikation på en mycket **små** nummer.

|E-notation poäng |Faktiska resultat|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="differences-with-predictions"></a>Skillnader i jämförelse med förutsägelser
När du tränar samma modell i en annan app och poängen inte är detta samma, beror det på att det är en del av slumpmässighet i utbildning. För det andra, innebär alla överlappande en utterance till mer än en avsikt översta avsett för samma utterance kan ändras baserat på utbildning.

Om din chatbot kräver särskilda THOMAS poäng anger syftet förtroende, bör du använda poäng skillnaden mellan de två översta avsikter. Detta ger flexibilitet för variationer i utbildning. 

## <a name="next-steps"></a>Nästa steg

Se [lägga till enheter](luis-how-to-add-entities.md) lära dig mer om hur du lägger till entiteter i appen THOMAS.

[LUIS]:luis-reference-regions.md
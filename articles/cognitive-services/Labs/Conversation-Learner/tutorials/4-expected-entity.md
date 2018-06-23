---
title: Hur du använder egenskapen ”förväntades entitet” för konversationen deltagaren åtgärder - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om att använda egenskapen ”förväntades enhet” i en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354057"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Hur du använder egenskapen ”förväntades entitet” för åtgärder

Den här kursen visar fältet ”förväntades entitet” av åtgärder.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Använd fältet ”förväntades enhet” i en åtgärd för att kommunicera med systemet som du förväntar dig användarens svar till en åtgärd blir till en entitet.

Concretely, om fältet ”förväntades enhet” i en åtgärd har angetts till $entity och sedan på nästa användare utterance, kommer systemet:

1. Först som vanligt, försök hitta enheter med hjälp av machine learning beroende entitet extrahering modellen
2. Om inga entiteter i steg 1, sedan--som en tumregel--tilldela hela användaren utterance $entity.
3. Anropa `EntityDetectionCallback` som vanligt, och gå vidare till val av åtgärd.

## <a name="steps"></a>Steg

### <a name="create-the-application"></a>Skapa programmet

1. Klicka på ny App i Webbgränssnittet,
2. Ange ExpectedEntities i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter sedan ny entitet.
2. Ange namn i enhetsnamnet.
3. Klicka på Skapa

Observera entitetstypen är 'custom' – det innebär att enheten kan exempelvis tränas.  Det finns också förskapad entiteter, vilket innebär att deras beteende kan inte justeras--dessa beskrivs i en annan kursen.

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och sedan ny åtgärd
2. Skriv ”vad är namnet på din?' svar.
3. Ange $name i förväntat entiteter. Klicka på Spara.
    - Det innebär att om den här frågan och och användarens svar har inte alla enheter som upptäckts, bot anta att hela användarens svar är den här entiteten.
2. Klicka på Åtgärder sedan ny åtgärd för att skapa en andra åtgärd.
3. Skriv ”Hello $name' svar.
    - Observera att entiteten automatiskt läggs till som en disqualifying entitet. 
4. Klicka på Spara

Nu har du två åtgärder.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vad är namnet på din”?
    - Observera att svaret ”Hello $name' kan inte väljas eftersom den requies entiteten $name definieras och $name är inte i bot's minne.
2. Ange 'david'. 
    - Observera att namnet är markerad som en enhet. Detta är på grund av tumregel vi som angetts ovan ska välja svaret som entiteten.
5. Klicka på poäng åtgärder
    - Obs namn-värde är nu i den bot minne.
    - 'Hello $name' är nu tillgängligt som ett svar. 
6. Välj 'Hello $name'.
7. Klicka på klar lärare.

Här är två exempel där maskininlärning entiteten extrahering modellen identifierar ett namn, så att ”förväntades entitet” tumregel inte aktiveras.

1. Klicka på ny Train-dialogruta.
2. Ange 'mitt namn är david'.
    - Observera att den identifierar david som namn på entiteten eftersom den har upptäckt ordet innan.
2. Klicka på poäng åtgärder
3. Välj 'Hello $name'.
4. Ange 'mitt namn är susan'.
    - Observera att den identifierar susan som namn eftersom det redan har sett det här mönstret.
2. Klicka på poäng åtgärder.
2. Välj 'Hello susan'.
3. Klicka på klar lärare.

Här är två ytterligare exempel där ”förväntades entitet” tumregel utlöser, men är felaktig och hur vi kan göra en korrigering.

1. Skriv ”Ring mig jose'.
    - Observera att det inte känner till namnet som en enhet.
2. Klicka på jose och markera namn.
3. Klicka på poäng åtgärder.
4. Välj hello $name.
5. Klicka på klar lärare.
1. Klicka på ny Train-dialogruta.
2. Ange ”hello”.
3. Ange ”jag vill kallas frank' som svar på” vad är ditt namn ”.
    - Observera att hela frasen markeras. Det beror på att statistiska modellen inte gick att hitta ett namn, så att tumregeln utlöses och valt hela svaret som entiteten namn.
2. Om du vill åtgärda det, klickar du på den markerade frasen, och klicka sedan på ett rött x. 
3. Markera frank, och klicka sedan på namnet.
2. Klicka på poäng åtgärder
3. Välj 'Hello $name'.
4. Klicka på klar lärare.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Negeras entiteter](./5-negatable-entities.md)

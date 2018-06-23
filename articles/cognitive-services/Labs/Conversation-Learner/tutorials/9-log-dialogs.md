---
title: Hur man loggar dialogrutor i en konversation deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig logga dialogrutor i en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 477545c48aeca05d56fdae28ac65a8f381a482fe
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354114"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-application"></a>Hur man loggar dialogrutor i en konversation deltagaren program

Den här kursen visar hur slutanvändare i gränssnittet konversation deltagaren; hur dialogrutor loggas; och hur du gör ändringar i loggas dialogrutor för att förbättra din modell.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Du kan använda loggen dialogrutorna för att granska och korrigera dialogrutor med slutanvändare.  Mer specifikt kan du åtgärda entiteten etiketter och val av åtgärd att förbättra den tränade modellen och hela systemets prestanda. 

## <a name="steps"></a>Steg

### <a name="create-the-application"></a>Skapa programmet

1. Klicka på ny App i Webbgränssnittet,
2. Ange LogDialogs i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter sedan ny entitet.
2. Ange ort i entitetsnamn.
3. Klicka på Skapa.

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och sedan ny åtgärd
2. Skriv ”vilken stad?' svar.
3. Ange $city i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden:

1. Klicka på Åtgärder sedan ny åtgärd.
3. Skriv ”väder i $city är förmodligen Soligt” som svar.
4. Nödvändiga entiteter, ange $city.
4. Klicka på Skapa.

Nu har du två åtgärder.

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”vad är väder”.
3. Klicka på poäng åtgärder och välj 'Vilken stad'?
2. Ange ”Seattle”.
3. Dubbelklicka på ”Seattle” och välj stad.
    - Den markerad som en stad enhet.
5. Klicka på poäng åtgärder
6. Välj 'väder i $city är förmodligen Soligt'.
7. Klicka på klar lärare.

Lägg till en annan dialogruta för exempel:

1. Klicka på ny åtgärd och sedan dialogrutan Ny tåget.
2. Skriv ”vad är väder i Seattle”?. Lägg märke till Seattle märks med en entitet.
5. Klicka på poäng åtgärder 
6. Välj 'väder i $city är förmodligen Soligt'.
7. Klicka på klar lärare.

### <a name="try-the-bot-as-the-user"></a>Försök i bot som användare
Nu ska vi anta att vi har distribuerat den här bot till användare.

1. Klicka på Logga dialogrutor.
2. Klicka på ny chatt.
    - Detta anger bot när användaren skulle uppstå i chatt webbkontroll till vänster i Användargränssnittet. Du kan ignorera området tomt utrymme till höger.
3. Skriv ”hello”.
4. Bot svar: 'vilken stad'?
4. Typen 'Boston'.
5. Bot svar: 'vilken stad'?
    - Det verkar inte rätt. Vi sparar den här dialogrutan.
2. Klicka på klart testning.

Låt oss börja en ny session:

2. Klicka på ny chatt.
3. Göra prognoser för Boston typ.
4. Bot svar: 'vilken stad'?
2. Klicka på klar lärare.

Nu ska vi göra korrigeringar andra dialogrutan:

1. Klicka på 'prognos Boston' under Log-dialogrutor.
    - Konversationen öppnas.
    - Om du klickar på användarsidan konversationens (här på 'prognos Boston'), kan du ändra entitet etiketter.
    - Om du klickar på system-sida (här på vilken stad) kan du ändra vilken åtgärd som har valts.
5. Klicka på 'prognos Boston'. 
    - Den här grundläggande orsaken är att Boston inte har taggats som en enhet. Vi behöver ändra som.
    - Dubbelklicka på 'Boston' och välj sedan stad.
    - Klicka på Skicka ändringar och klicka på Spara. Detta skapar en utbildning dialogrutan baserat på de ändringar du gjort och släpp utbildning dialogrutor vid ändringar du gjort.
6. Välj ”väder i $city är förmodligen Soligt”.
7. Klicka på klar lärare. Om du går till Train-dialogrutor nu visas den nya åtgärden har lagts till.

![](../media/tutorial9_logdiag1.PNG)

Nu ska vi göra korrigeringar andra dialogrutan:

1. Klicka på ”hello” under Log-dialogrutor.
    - Konversationen öppnas.
3. Observera svaret ”hello” är vilken stad. Men vi vill ändra det till något som passar bättre. Ett bättre svar är något som ”hello, jag är väder bot'. Men det finns ingen åtgärd som utför som så att vi behöver skapa en.
4. Klicka på åtgärden.
    - I svaret, Skriv ”jag väder bot. Jag kan hjälpa dig med prognoser ”.
6. Avmarkera väntan på svar kryssrutan så att det blir en icke-wait-åtgärd.
7. Klicka på Skapa.
8. Klicka för att välja den här nya åtgärden. Klicka sedan på spara.
    - Detta ger dig tillbaka till den punkten i utbildning-sessionen.
6. Klicka på Välj 'vilken stad ”?
7. Typen i 'Boston'. Dubbelklicka på att tagga Boston som en enhet om den inte redan.
8. Klicka på poäng åtgärder.
9. Markera ”väder i $city är förmodligen Soligt”.
10. Klicka på klar lärare.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Entiteten identifiering av återanrop](./10-entity-detection-callback.md)

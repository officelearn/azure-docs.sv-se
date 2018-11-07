---
title: Hur du loggar in dialogrutor i en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig mer om att logga dialogrutor i en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5369e16e0f1adc48eb019f3790dc900577c144af
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51243667"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Hur du loggar in dialogrutor i en Konversationsdeltagare-modell

Den här kursen visar hur du gör slutanvändarens testning i gränssnittet Konversationsdeltagare; hur dialogrutor loggas; och hur du gör ändringar av loggas dialogrutor för att därigenom förbättra din modell.

## <a name="video"></a>Video

[![Förhandsversion av självstudien 9](https://aka.ms/cl-tutorial-09-preview)](https://aka.ms/blis-tutorial-09)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Du kan använda log-dialogrutorna för att granska och korrigera dialogrutor som utförs med slutanvändare.  Mer specifikt kan du åtgärda entitet etiketter och åtgärden val för att förbättra prestandan för den tränade modellen och hela systemet. 

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange LogDialogs i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter och ny entitet.
2. Ange ort i enhetens namn.
3. Klicka på Skapa.

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och ny åtgärd
2. I svaret, skriver du ”vilka city”?.
3. Ange $city i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden:

1. Klicka på åtgärder och ny åtgärd.
3. Skriv ”vädret i $city är förmodligen solig” som svar.
4. Nödvändiga entiteter, ange $city.
4. Klicka på Skapa.

Nu har du två åtgärder.

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”vad blir vädret'.
3. Klicka på poäng åtgärder och välj ”vilka city”?
2. Ange ”Seattle”.
3. Dubbelklicka på ”Seattle” och välj stad.
    - Den markerad som en stad-enhet.
5. Klicka på poäng åtgärder
6. Välj ”vädret i $city är förmodligen solig”.
7. Klicka på klar undervisning.

Lägg till ett annat exempel dialogrutan:

1. Klicka på träna dialogrutor och därefter nytt träna dialogrutan.
2. Skriv ”vad är vädret i Seattle”?. Lägg märke till Seattle märkts med en entitet.
5. Klicka på poäng åtgärder 
6. Välj ”vädret i $city är förmodligen solig”.
7. Klicka på klar undervisning.

### <a name="try-the-bot-as-the-user"></a>Försök roboten som användare
Låt oss anta att vi har distribuerat den här bot till användare.

1. Klicka på logg-dialogrutor.
2. Klicka på dialogrutan för nytt Log.
    - Detta medför roboten när användaren skulle uppleva i webbchattskontroll till vänster i Användargränssnittet. Du kan ignorera området tomt utrymme till höger.
3. Skriv ”hello”.
4. Bot svar: ”vilka city”?
4. Typen 'Boston'.
5. Bot svar: ”vilka city”?
    - Det verkar inte vara rätt. Vi sparar den här dialogrutan.
2. Klicka på klar testning.

Låt oss börja en ny session:

2. Klicka på dialogrutan för nytt Log.
3. Skapa prognoser för Boston typ.
4. Bot svar: ”vilka city”?
2. Klicka på klar testning.

Nu ska vi göra korrigeringar av andra dialogrutan:

1. Klicka på ”prognosen för Boston' under Log-dialogrutor.
    - Då öppnas konversationen.
    - Om du klickar på användarsidan för konversationen (här på 'prognosen för Boston ”), kan du ändra etiketter för entiteten.
    - Om du klickar på system-sida (här på ”vilka city”), du kan ändra vilken åtgärd som har valts.
5. Klicka på ”prognosen för Boston'. 
    - Den här grundläggande orsaken är att Boston inte har taggats som en entitet. Vi behöver du ändra.
    - Dubbelklicka på ”Boston” och välj sedan stad.
    - Klicka på Skicka ändringar och klicka på Spara. Detta skapar en utbildning dialogruta baserat på de ändringar du gjort och släpp utbildning dialogrutor vid ändringen du gjorde.
6. Välj ”vädret i $city är förmodligen vackert”.
7. Klicka på klar undervisning. Om du går till Train-dialogrutor nu visas den nya åtgärden har lagts till.

![](../media/tutorial9_logdiag1.PNG)

Nu ska vi göra korrigeringar av andra dialogrutan:

1. Klicka på ”hello” under Log-dialogrutor.
    - Då öppnas konversationen.
3. Svaret ”hello” är ”vilka city”. Men vi vill ändra det till något som passar bättre. Ett bättre svar är något som ”Hej! jag heter väder-roboten'. Men det finns inga åtgärder som gör som så att vi behöver skapa ett.
4. Klicka på åtgärden.
    - I svaret, skriver du ”jag väder-roboten. Jag kan hjälpa dig med prognoser ”.
6. Avmarkera väntan på svar kryssrutan så att de blir en icke-wait-åtgärden.
7. Klicka på Skapa.
8. Klicka för att välja den här nya åtgärden. Klicka sedan på spara.
    - Detta ger du tillbaka till den tidpunkten i utbildningssessionen.
6. Klicka för att markera ”vilka city”?
7. Ange ”Boston'. Dubbelklicka på taggen Boston som en enhet om den inte redan gjort.
8. Klicka på poäng åtgärder.
9. Klicka för att välja ”vädret i $city är förmodligen vackert”.
10. Klicka på klar undervisning.

![](../media/tutorial9_addnewaction.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Entiteten identifiering av återanrop](./10-entity-detection-callback.md)

---
title: Demo Konversationsdeltagare modell, pizza beställning – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demo Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 052ef249f3367a562e5598b90533c0e52ed75df4
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171392"
---
# <a name="demo-pizza-order"></a>Demo: Pizza ordning
Den här demonstrationen visar en platt ordning bot. Det har stöd för sorteringen i en enda pizza med den här funktionen:

- identifiera pizza toppings i användaren yttranden
- kontrollerar om pizza toppings finns i lager eller slut på lagret och svara på rätt sätt
- komma ihåg pizza toppings från en tidigare och erbjuder att – starta en ny order med samma toppings

## <a name="video"></a>Video

[![Demo Pizza förhandsversion](http://aka.ms/cl-demo-pizza-preview)](http://aka.ms/blis-demo-pizza)

## <a name="requirements"></a>Krav
Den här självstudien krävs att pizza ordning bot körs

    npm run demo-pizza

### <a name="open-the-demo"></a>Öppna demon

Klicka på TutorialDemo Pizza ordningen i listan modell av webbgränssnittet. 

## <a name="entities"></a>Entiteter

Du har skapat tre entiteter.

- Toppings: den här entiteten ackumuleras toppings som användaren har begärt. Den innehåller giltig toppings som finns i lager. Den kontrollerar om det finns en Fyll i eller slut på lagret.
- OutofStock: den här entiteten används för att kommunicera tillbaka till användaren att deras valda Fyll inte är i lager.
- LastToppings: när en beställning placeras den här entiteten används för att erbjuda användaren listan över toppings på deras inbördes ordning.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Åtgärder

Du har skapat en uppsättning åtgärder som exempelvis genom att be användaren vad de vill ha på sina pizza talar om vad de har lagt till hittills, och så vidare.

Det finns även två API-anrop:

- FinalizeOrder: att placera ordningen för pizza
- UseLastToppings: att migrera toppings från föregående ordning 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning
Du har definierat en handfull utbildning dialogrutor. 

![](../media/tutorial_pizza_dialogs.PNG)

Till exempel nu ska vi prova en undervisning-session.

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”beställa en platt'.
2. Klicka på poäng åtgärd.
3. Klicka för att väljer 'vad du vill på din pizza ”?
4. Ange ”svamp och ost”.
    - Observera LUIS med etiketten båda som Toppings. Om det inte korrekt kan du markera och sedan korrigera den.
    - Tecknet '+' bredvid entiteten innebär att den läggs till en uppsättning toppings.
5. Klicka på poäng åtgärder.
    - Observera `mushrooms` och `cheese` är inte i minnet för Toppings.
3. Klicka för att välja ”du har $Toppings på din pizza'
    - Observera att detta är en icke-wait-åtgärden så ber roboten för nästa åtgärd.
6. Välj ”vill du något annat”?
7. Ange ”ta bort svamp och lägga till peppers”.
    - Observera `mushroom` har en '-' Registrera bredvid den och för det ska tas bort. Och `peppers` har tecknet ”+” bredvid den och lägga till den i toppings.
2. Klicka på poäng åtgärd.
    - Observera `peppers` är nu i fetstil eftersom den är ny. Och `mushrooms` har passerats.
8. Klicka för att välja ”du har $Toppings på din pizza'
6. Välj ”vill du något annat”?
7. Ange ”Lägg till ärter”.
    - `Peas` är ett exempel på en sedan som är slut. Det är fortfarande märkt som ett sedan.
2. Klicka på poäng åtgärd.
    - `Peas` visas som OutOfStock.
    - Om du vill se hur det har inträffat, öppna koden på `C:\<\installedpath>\src\demos\demoPizzaOrder.ts`. Titta på EntityDetectionCallback-metoden. Den här metoden anropas efter varje sedan att se om det är i lager. Om inte, den tar bort den från uppsättningen toppings och lägger till i OutOfStock-entiteten. Variabeln inStock definieras ovan den metod som innehåller listan över toppings i lager.
6. Välj ”vi har inte $OutOfStock'.
7. Välj ”vill du något annat”?
8. Ange ”Nej”.
9. Klicka på poäng åtgärd.
10. Välj ”FinalizeOrder” API-anrop. 
    - Detta anropar 'FinalizeOrder ”-funktion som definierats i kod. Detta tar bort toppings och returnerar ”beställningen är på väg”. 
2. Ange ”beställa en annan'. Vi startar en ny order.
9. Klicka på poäng åtgärd.
    - ”ost' och 'peppers' är i RAM-minne som toppings från den senaste ordningen.
1. Välj ”vill du $LastToppings”.
2. Ange ”Ja”
3. Klicka på poäng åtgärd.
    - Roboten vill vidta åtgärder för UseLastToppings. Det är andra av de två metoderna för motringning. Den kopierar den senaste ordning toppings till toppings och rensa senaste toppings. Det här är ett sätt att komma ihåg den senaste ordningen och om användaren säger att de vill ha en annan pizza, ge dessa toppings som alternativ.
2. Klicka på ”du har $Toppings på din pizza'.
3. Välj ”vill du något annat”?
8. Ange ”Nej”.
4. Klicka på klar undervisning.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo – VR-appstartaren](./demo-vr-app-launcher.md)

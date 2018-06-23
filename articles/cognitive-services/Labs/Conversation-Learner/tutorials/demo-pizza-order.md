---
title: Demonstrera konversation deltagaren program, pizza order - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demonstration konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354123"
---
# <a name="demo-pizza-order"></a>Demo: Pizza ordning
Den här demon illustrerar en pizza ordning bot. Den stöder sorteringen av en enskild pizza med den här funktionen:

- identifiera pizza toppings i användaren utterances
- kontrollerar om pizza toppings finns i lager eller lagret och svarar på rätt sätt
- komma ihåg pizza toppings från en tidigare och erbjuda att - starta en ny order med samma toppings

## <a name="requirements"></a>Krav
Den här kursen krävs att pizza ordning bot körs

    npm run demo-pizza

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på TutorialDemo Pizza ordning i App-lista över webbgränssnittet. 

## <a name="entities"></a>Entiteter

Vi har skapat tre entiteter.

- Toppings: ackumuleras toppings användaren uppge. Den innehåller giltig toppings som finns i lager. Den kontrollerar om det finns en Fyll i eller utanför lager.
- OutofStock: används för att kommunicera tillbaka till användaren att deras valda sedan inte är i lager.
- LastToppings: när beställningen görs den här entiteten används för att erbjuda användaren i listan över toppings på deras inbördes ordning.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Åtgärder

Vi har skapat en uppsättning åtgärder, exempelvis genom att be användaren vad de vill ha på sina pizza som talar om vad de har lagt till hittills osv.

Det finns även två API-anrop:

- FinalizeOrder: att placera ordningen för pizza
- UseLastToppings: att migrera toppings från föregående ordning 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning
Vi har definierat en handfull utbildning dialogrutor. 

![](../media/tutorial_pizza_dialogs.PNG)

T.ex, prova med en lärare session.

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange ”beställa ett pizza'.
2. Klicka på poäng åtgärd.
3. Klicka om du vill markera 'vad du vill på din pizza ”?
4. Ange ”svamp och ost”.
    - Observera THOMAS med etiketten både som Toppings. Om som inte var korrekt kan du markera och korrigera den.
    - Tecknet ”+” bredvid entiteten innebär att den läggs till en uppsättning toppings.
5. Klicka på poäng åtgärder.
    - Meddelande svamp och ost är inte i minnet för Toppings.
3. Markera ”du har $Toppings på din pizza'
    - Observera att det här är en icke-wait-åtgärd, så bot ber för nästa åtgärd.
6. Välj 'Vill du göra något annat ”?
7. Ange ”ta bort svamp och lägga till paprikor”.
    - Meddelande **mushroom** har en '-' logga bredvid den för att den ska tas bort. Och paprikor har ”+” för att lägga till toppings.
2. Klicka på poäng åtgärd.
    - Meddelande **paprikor** nu i fetstil eftersom den är ny. Och **svamp** har passerats.
8. Markera ”du har $Toppings på din pizza'
6. Välj 'Vill du göra något annat ”?
7. Ange ”Lägg till ärter'.
    - Ärter är ett exempel på en sedan som är slut. Observera att det fortfarande är märkt som en sedan.
2. Klicka på poäng åtgärd.
    - Ärter visas som OutOfStock.
    - Om du vill se hur detta har hänt, öppnar vi koden i C:\<\installedpath > \src\demos\demoPizzaOrder.ts. Och notera EntityDetectionCallback-metoden. Den här metoden anropas efter varje sedan om den finns i lager. Om inte, det tar bort den från uppsättningen toppings och lägger till OutOfStock entiteten. Variabeln inStock definieras ovanför den metod som har en lista över toppings i lager.
6. Välj 'Har $OutOfStock'.
7. Välj 'Vill du göra något annat ”?
8. Ange ”Nej”.
9. Klicka på poäng åtgärd.
10. Välj 'FinalizeOrder' API-anrop. 
    - Detta anropar 'FinalizeOrder'-funktionen som definieras i koden. Detta tar bort toppings och returnerar 'din order är på väg'. 
2. Ange ”beställa ett annat'. Vi börjar en ny order.
9. Klicka på poäng åtgärd.
    - Observera ost och paprikor är minnet som toppings från den senaste ordningen.
1. Välj ”vill du $LastToppings'.
2. Ange ”Ja”
3. Klicka på poäng åtgärd.
    - Bot vill åtgärda UseLastToppings. Det är andra av de två metoderna återanrop. Den kopierar den senaste ordning toppings till toppings och avmarkera senaste toppings. Detta är ett sätt att komma ihåg den senaste ordningen och om användaren säger att de vill ha en annan pizza, ge dessa toppings som alternativ.
2. Klicka på ”du har $Toppings på din pizza'.
3. Välj 'Vill du göra något annat ”?
8. Ange ”Nej”.
4. Klicka på klar lärare.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo - VR app programstart](./demo-vr-app-launcher.md)

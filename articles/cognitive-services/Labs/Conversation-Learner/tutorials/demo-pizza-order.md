---
title: Demo Conversation Learners modell, pizza order-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demonstrations Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 4d42121468b8a875ecd13fb936d810728bd4b644
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703427"
---
# <a name="demo-pizza-order"></a>Demo: Pizza-ordning
Den här demon illustrerar en pizza order-robot, med stöd för en pizza-beställning av:

- identifiera pizza toppings från användare yttranden
- hantera toppings-inventering och svara på lämpligt sätt
- komma ihåg tidigare beställningar och ändra ordning på en identisk pizza

## <a name="video"></a>Video

[![Demo pizza Preview](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Krav
Den här självstudien kräver att pizza order robot körs

    npm run demo-pizza

### <a name="open-the-demo"></a>Öppna demon

I listan modell i webb gränssnittet klickar du på TutorialDemo pizza order. 

## <a name="entities"></a>Entiteter

Modellen innehåller tre entiteter:

- "Toppings" ackumulerar användarens angivna toppings, om det är tillgängligt.
- "OutofStock" signalerar användaren att den valda topping är utanför lagret
- "LastToppings" innehåller den historiska toppings från sin tidigare ordning

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Åtgärder

Modellen innehåller en uppsättning åtgärder som ber användaren om val av topping, ackumulerade toppings och mycket annat.

Två API-anrop tillhandahålls också:

- "FinalizeOrder" hanterar order uppfyllelse
- "UseLastToppings" bearbetar historisk toppings-information

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Tränings dialog rutor

Det finns flera utbildnings dialog rutor i modellen.

![](../media/tutorial_pizza_dialogs.PNG)

Nu ska vi träna modellen lite mer genom att skapa en annan träna-dialog.

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Beställ a pizza with ost"
    - Ordet "ost" extraherades av enhetens Extractor.
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "du har en ost på din pizza."
5. Välj svaret, "vill du ha något annat?"
6. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "Lägg till svamp och paprika"
7. Klicka på knappen "Poäng åtgärder".
8. Välj svaret, "du har ost, svamp och paprika på din pizza."
9. Välj svaret, "vill du ha något annat?"
10. I Chat-panelen, där det står "Skriv ditt meddelande...", skriver du "ta bort pepparer och Lägg till korv"
11. Klicka på knappen "Poäng åtgärder".
12. Välj svaret, "du har ost, svamp och korv på din pizza."
13. Välj svaret, "vill du ha något annat?"
14. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Lägg till Yam"
15. Klicka på knappen "Poäng åtgärder".
    - Värdet "Yam" har lagts till i "OutofStock" av entitetens returkod för återanrop, eftersom texten inte matchade ingredienser som stöds.
16. Välj svaret, "OutOfStock"
17. Välj svaret, "vill du ha något annat?"
18. I panelen chat, där det står "Skriv ditt meddelande...", skriver du i "nej"
    - "Nej" har inte marker ATS som vilken typ av avsikt som helst. I stället ska vi välja relevant åtgärd baserat på den aktuella kontexten.
19. Klicka på knappen "Poäng åtgärder".
20. Välj svaret, "FinalizeOrder"
    - Om du väljer den här åtgärden skapas kundens aktuella toppings som sparats till entiteten "LastToppings", och borttagning av entiteten "toppings" med FinalizeOrder-callback-koden.
21. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Beställ en annan"
22. Klicka på knappen "Poäng åtgärder".
23. Välj svaret, "vill du ha ost, svamp och korv?"
    - Den här åtgärden är tillgänglig nu på grund av att entiteten "LastToppings" har angetts.
24. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Ja"
25. Klicka på knappen "Poäng åtgärder".
26. Välj svaret, "UseLastToppings"
27. Välj svaret, "du har ost, svamp och korv på din pizza."
28. Välj svaret, "vill du ha något annat?"

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera en Conversation Learner-robot](../deploy-to-bf.md)

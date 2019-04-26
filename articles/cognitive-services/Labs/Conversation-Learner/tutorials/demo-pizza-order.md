---
title: Demo Konversationsdeltagare modell, pizza beställning – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demo Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3fe11bef6c505771ee1e3f2e12e647eafc7c45d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60430999"
---
# <a name="demo-pizza-order"></a>Demo: Pizza ordning
Den här demonstrationen visar en platt ordning bot, stöd för enkel pizza ordning genom att:

- identifiera pizza toppings från användaren yttranden
- Hantera toppings inventering och svara på rätt sätt
- Kom ihåg tidigare beställningar och minskar ordnar om en identisk pizza

## <a name="video"></a>Video

[![Demo Pizza förhandsversion](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Krav
Den här självstudien krävs att pizza ordning bot körs

    npm run demo-pizza

### <a name="open-the-demo"></a>Öppna demon

Klicka på TutorialDemo Pizza ordningen i listan modell av webbgränssnittet. 

## <a name="entities"></a>Entiteter

Modellen innehåller tre enheter:

- ”Toppings” ackumuleras användarens angivna toppings, om det är tillgängligt.
- ”OutofStock” signalerar användaren valda sedan är slut
- ”LastToppings” innehåller historiska toppings från tidigare dataordningen

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Åtgärder

Modellen innehåller en uppsättning åtgärder som begär användarens dem sedan, ackumulerade toppings och mycket mer.

Två API-anrop tillhandahålls också:

- ”FinalizeOrder” hanterar orderhantering
- ”UseLastToppings” bearbetar historiska toppings information

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning

Flera utbildning-dialogrutor hittades i modellen.

![](../media/tutorial_pizza_dialogs.PNG)

Nu ska vi träna modellen lite mer genom att skapa en annan träna dialogruta.

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ”Order en platt med ost” Skriv
    - Ordet ”ost” har extraherats av entiteten extraktor.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar, ”du har ost på din pizza”.
5. Välj svar, ”vill du göra något annat”?
6. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Lägg till svamp och peppers”
7. Klicka på knappen ”poäng åtgärder”.
8. Välj svar ”du har ost, svamp och peppers på din pizza”.
9. Välj svar, ”vill du göra något annat”?
10. På panelen chatt står det ”Skriv meddelandet...”, anger du ”ta bort peppers och lägga till köttråvara”
11. Klicka på knappen ”poäng åtgärder”.
12. Välj svar, ”du har ost, svamp och köttråvara på din pizza”.
13. Välj svar, ”vill du göra något annat”?
14. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Lägg till Jamsrot”
15. Klicka på knappen ”poäng åtgärder”.
    - Värdet för ”Jamsrot” har lagts till ”OutofStock” av identifiering av återanrop enhetskoden, som alla stöds ingredienser inte överensstämmer med texten.
16. Välj svar ”OutOfStock”
17. Välj svar, ”vill du göra något annat”?
18. På panelen chatt står det ”Skriv meddelandet...”, ange ”Nej”
    - ”No” inte har markerats som valfri typ av avsikt. Vi kommer i stället att välja den relevanta åtgärder baserat på den aktuella kontexten.
19. Klicka på knappen ”poäng åtgärder”.
20. Välj svar ”FinalizeOrder”
    - Att välja den här åtgärden resulterade i kundens aktuella toppings komma sparas i ”LastToppings” entitets- och borttagning av entiteten ”Toppings” av FinalizeOrder återanrop koden.
21. På panelen chatt står det ”Skriv meddelandet...”, ”order en annan” Skriv
22. Klicka på knappen ”poäng åtgärder”.
23. Välj svar, ”vill du göra ost, svamp och köttråvara”?
    - Den här åtgärden är nu tillgänglig på grund av entiteten ”LastToppings” har angetts.
24. På panelen chatt står det ”Skriv meddelandet...”, ange ”Ja”
25. Klicka på knappen ”poäng åtgärder”.
26. Välj svar ”UseLastToppings”
27. Välj svar, ”du har ost, svamp och köttråvara på din pizza”.
28. Välj svar, ”vill du göra något annat”?

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Distribuera en robot som Konversationsdeltagare](../deploy-to-bf.md)

---
title: Hur du använder flera värden entiteter med en modell för konversationen Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder flera värden entiteter med en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8544d63f38f88a0e623dff343bf8b5133931b70b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55228312"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Hur du använder flera värden entiteter med en modell för Konversationsdeltagare
Den här kursen visar egenskapen flera värden för entiteter.

## <a name="video"></a>Video

[![Kan ha flera värden entiteter självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Flera värden entiteter ackumuleras värden i en lista i stället för att lagra ett enstaka värde.  De här entiteterna är användbara när användarna kan ange fler än ett värde. Toppings på en platt till exempel.

Entiteter markerats som flera värden har varje erkända instans av entiteten läggas till i en lista i Robotens minnet. Efterföljande igenkänning läggs till dess värde i stället skriva över.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på ”ny modell”.
2. Skriv ”MultiValueEntities” i fältet ”namn” och tryck på RETUR.
3. Klicka på knappen ”Skapa”.

### <a name="entity-creation"></a>Skapa en entitet

1. Klicka på ”entiteter” och knappen ”ny entitet” på den vänstra panelen.
2. Välj ”anpassade tränas” för ”entitetstypen”.
3. Skriv ”toppings” för ”entitetsnamnet”.
4. Kontrollera ”med flera värden” kryssrutan.
    - Flera värden entiteter ackumuleras ett eller flera värden i entiteten.
5. Markera kryssrutan ”Negatable”.
    - Egenskapen ”Negatable” dolda i en annan självstudie.
6. Klicka på knappen ”Skapa”.

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. I ”Robotens svaret...” i fältet ”här är din toppings: $toppings”
    - Ledande dollartecknet anger en enhetsreferens
3. Klicka på knappen ”Skapa”.

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”vilka toppings vill du”?
3. I fältet ”diskvalificera berättigar”, skriver du ”toppings”.
4. Klicka på knappen ”Skapa”.

Nu har du två åtgärder.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>Träna modellen

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Hej”.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”vilka toppings vill du”?
    - Den: e percentilen är 100%, eftersom den enda giltiga åtgärden baserat på begränsningarna.
5. På panelen chatt står det ”Skriv meddelandet...”, anger du ”ost och svamp”
6. Klicka på ”ost” och välj etiketten ”+ toppings”
7. Klicka på ”svamp' och välj etiketten” + toppings ”
8. Klicka på knappen ”poäng åtgärder”.
9. Välj svar ”, här är din toppings: $toppings”
10. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Lägg till peppar”
11. Klicka på ”peppar' och välj etiketten” + toppings ”
12. Klicka på knappen ”poäng åtgärder”.
13. Välj svar ”, här är din toppings: $toppings”
14. På panelen chatt står det ”Skriv meddelandet...”, ”ta bort OST” Skriv
15. Klicka på ”ost” och välj etiketten ”-toppings”
16. Klicka på knappen ”poäng åtgärder”.
17. Välj svar ”, här är din toppings: $toppings”

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förtränade entiteter](./08-pre-trained-entities.md)

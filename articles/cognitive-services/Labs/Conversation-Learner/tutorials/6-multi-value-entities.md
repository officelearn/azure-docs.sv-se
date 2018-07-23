---
title: Hur du använder flera värden entiteter med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder flera värden entiteter med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 6193a515f0d8136e0d420b7554cf26fee8f50953
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173109"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Hur du använder flera värden entiteter med en modell för Konversationsdeltagare
Den här självstudien visas ”flera value”-egenskapen för entiteter.

## <a name="video"></a>Video

[![Förhandsversion av självstudien 6](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

##<a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
En entitet som är ”Flervärde” ackumuleras värden i en lista i stället för att lagra ett enstaka värde.  Detta är användbart för entiteter där användaren kan ange flera värden, till exempel toppings på en platt.

Concretely, om en entitet markeras som ”Flervärde”, sedan var och en känns igen av entitetsinstanserna kommer att läggas till i en lista i robotens minne (i stället skriva över en enda entitet värde).

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange MultiValueEntities i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter och ny entitet.
2. Ange Toppings i enhetens namn.
3. Kontrollera flera värden.
    - Flera värden entiteter ackumuleras ett eller flera värden i entiteten.
2. Kontrollera negeras.  
    - Detta gör att användarna att ta bort toppings från sin lista över ackumulerade pizza toppings.
3. Klicka på Skapa.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och ny åtgärd
2. I svaret, skriver du ”vilka toppings vill”?.
3. Ange Toppings i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden.

1. Klicka på åtgärder och sedan ny åtgärd för att skapa en andra åtgärd.
3. I svaret, skriver du ”här är din toppings: $Toppings'.
4. Klicka på Skapa

Nu har du två åtgärder.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilka toppings vill”?
2. Ange ”svamp och ost”. 
    - Du kan märka noll, en eller flera av entiteterna.
3. Klicka på ”svamp' och välj Toppings.
4. Klicka på ”ost” och välj Toppings.
5. Klicka på poäng åtgärder
    - De två värdena finns nu i entiteten Toppings. 
6. Välj ”här är din toppings: $Toppings'.

Vi kan lägga till fler så här:

7. Ange ”Lägg till peppers”.
    - Klicka på peppers om du under entitet identifiering och välj Toppings.
3. Klicka på poäng åtgärder.
    - ”peppers” visas nu som ytterligare ett värde i Toppings.
6. Välj ”här är din toppings: $Toppings'.

Nu ska vi ta bort en sedan och lägga till en:

2. Skriv ”ta bort peppers och lägga till köttråvara”.
1. Klicka på ”peppers” och klicka på ett rött x ta bort den.
2. Klicka på ”peppers” och välj ”-Toppings'.
3. Klicka på poäng åtgärder.
    - har tagits bort 'peppers' och 'köttråvara ”har lagts till.
6. Välj ”här är din toppings: $Toppings'.

Nu ska vi prova att ta bort allt:

6. Ange ”ta bort svamp, ta bort OST och ta bort köttråvara'.
7. Klicka på var och en av tre och välj ”-Toppings'.
7. Klicka på poäng åtgärder.
    - Alla toppings rensas.
2. Välj ”vilka toppings vill”?
3. Klickar du på klar undervisning

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Inbyggda entiteter](./7-built-in-entities.md)

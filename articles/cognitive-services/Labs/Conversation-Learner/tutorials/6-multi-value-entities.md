---
title: Hur du använder flera värden entiteter med ett samtal deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder flera värden entiteter med ett samtal deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 865b50747b2c9574b5f88d4902bea9e4c8e0e032
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354009"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-application"></a>Hur du använder flera värden entiteter med ett samtal deltagaren program
Den här kursen visar egenskapen ”Flervärde” för entiteter.

##<a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
En entitet som är ”Flervärde” ackumuleras värden i en lista i stället för att lagra ett enstaka värde.  Detta är användbart för entiteter där användaren kan ange flera värden, till exempel toppings på en pizza.

Concretely, om en entitet har markerats som ”Flervärde”, sedan varje känns igen av entitetsinstanserna kommer att läggas till en lista i bot minne (i stället att skriva över en enda entitet värde).

## <a name="steps"></a>Steg

### <a name="create-the-application"></a>Skapa programmet

1. Klicka på ny App i Webbgränssnittet,
2. Ange MultiValueEntities i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter sedan ny entitet.
2. Ange Toppings i enhetsnamnet.
3. Kontroll med flera värden.
    - Flervärde entiteter samlas ett eller flera värden i entiteten.
2. Kontrollera negeras.  
    - Detta medför att ta bort toppings från sin lista över ackumulerade pizza toppings.
3. Klicka på Skapa.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och sedan ny åtgärd
2. Skriv ”vilka toppings vill du fortsätta?' svar.
3. Ange Toppings i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden.

1. Klicka på Åtgärder sedan ny åtgärd för att skapa en andra åtgärd.
3. Skriv i svaret, ' här är din toppings: $Toppings'.
4. Klicka på Skapa

Nu har du två åtgärder.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vilka toppings vill du fortsätta?'
2. Ange ”svamp och ost”. 
    - Du kan märka noll, en eller flera enheter.
3. Klicka på 'svamp' och välj Toppings.
4. Klicka på 'ost' och välj Toppings.
5. Klicka på poäng åtgärder
    - Observera att de två värdena visas nu i entiteten Toppings. 
6. Välj ' här är din toppings: $Toppings'.

Vi kan lägga till fler detta:

7. Ange ”Lägg till paprikor'.
    - Klicka på paprikor om du under entiteten identifiering och välj Toppings.
3. Klicka på poäng åtgärder.
    - Observera att paprikor nu visas som ett ytterligare värde i Toppings.
6. Välj ' här är din toppings: $Toppings'.

Nu ska vi ta bort en sedan och Lägg till en:

2. Skriv ”ta bort paprikor och lägga till korv”.
1. Klicka på 'paprikor ”och klicka på det röda x ta bort den.
2. Klicka på 'paprikor' och välj '-Toppings'.
3. Klicka på poäng åtgärder.
    - Observera att ”paprikor' har tagits bort och 'korv' har lagts till.
6. Välj ' här är din toppings: $Toppings'.

Nu ska vi prova att ta bort allt:

6. Ange ”ta bort svamp tar du bort OST och ta bort korv'.
7. Klicka på var och en av tre och välj '-Toppings'.
7. Klicka på poäng åtgärder.
    - Observera att alla toppings är avmarkerad.
2. Välj 'vilka toppings vill du fortsätta?'
3. Klicka på klar lärare

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Inbyggda enheter](./7-built-in-entities.md)

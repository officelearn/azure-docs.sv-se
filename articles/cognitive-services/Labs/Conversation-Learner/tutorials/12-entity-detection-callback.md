---
title: Hur du använder entiteten identifiering motringning med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder entiteten identifiering motringning med en Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 147887151abd5c1f7455b0efbf9aadbc2d884183
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796844"
---
# <a name="how-to-use-entity-detection-callback"></a>Hur du använder entiteten identifiering av återanrop

Den här självstudien visar återanropet som entiteten identifiering och illustrerar ett vanligt mönster för att lösa entiteter.

## <a name="video"></a>Video

[![Entiteten identifiering av återanrop självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_EntityDetection_Preview)](https://aka.ms/cl_Tutorial_v3_EntityDetection)

## <a name="requirements"></a>Krav
Den här självstudien kräver att den `tutorialEntityDetectionCallback` bot körs.

    npm run tutorial-entity-detection

## <a name="details"></a>Information
Entiteten identifiering av återanrop aktivera ändring av entitetsbeteende för taligenkänning och manipulering av entiteten via kod. Vi visar den här funktionen genom att gå igenom ett typiskt entitet identifiering av återanrop designmönster. Exempelvis matchar ”big apple” till ”new york”.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på ”ny modell”.
2. Skriv ”EntityDetectionCallback” i fältet ”namn” och tryck på RETUR.
3. Klicka på knappen ”Skapa”.

Tre enheter behövs i det här exemplet skapar vi de tre.

### <a name="create-the-custom-trained-entity"></a>Skapa anpassad tränade entitet

1. Klicka på ”entiteter” och knappen ”ny entitet” på den vänstra panelen.
2. Välj ”anpassade tränas” för ”entitetstypen”.
3. Typen ”stad” för ”entitetsnamnet”.
4. Klicka på knappen ”Skapa”.

### <a name="create-the-first-programmatic-entity"></a>Skapa den första programmässiga entiteten

1. Klicka på knappen ”ny entitet”.
2. Välj ”programmässiga” för ”entitetstypen”.
3. Typen ”CityUnknown” för ”entitetsnamnet”.
4. Klicka på knappen ”Skapa”.

### <a name="create-the-first-programmatic-entity"></a>Skapa den första programmässiga entiteten

1. Klicka på knappen ”ny entitet”.
2. Välj ”programmässiga” för ”entitetstypen”.
3. Typen ”CityResolved” för ”entitetsnamnet”.
4. Klicka på knappen ”Skapa”.

Skapa nu tre åtgärder.

### <a name="action-creation"></a>Skapa en åtgärd

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”vilken stad vill”?
3. I fältet ”förväntade entiteten i användarens svar...”, skriver du ”stad”.
4. I fältet ”diskvalificera entiteter”, skriver du ”stad”.
5. Klicka på knappen ”Skapa”.
6. Klicka på knappen ”ny åtgärd”.
7. Fältet i ”Robotens svaret...”, skriver du ”vilken stad vill”?
8. Skriv i fältet ”förväntade entiteten i användarens svar...”, ”jag vet inte den här stad”.
9. Klicka på knappen ”Skapa”.
10. Klicka på knappen ”ny åtgärd”.
11. Fältet i ”Robotens svaret...”, skriver du ”$CityResolved är väldigt trevligt”.
12. Klicka på knappen ”Skapa”.

Här är koden för motringning:

![](../media/tutorial10_callbackcode.PNG)

### <a name="train-the-model"></a>Träna modellen

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Hej”
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”vilken stad vill”?
5. På panelen chatt står det ”Skriv meddelandet...”, ange ”big äpple”
6. Klicka på knappen ”poäng åtgärder”.
    - Att klicka på knappen utlöser entitet identifiering av återanrop
    - Motringning koden anger värdet CityResolved entitet korrekt till ”new york”
7. Välj svar ”, new york är är väldigt trevligt”.

Det här mönstret är vanligt i många scenarier för bot. Användaren yttranden och extraherade entiteter skickas till din affärslogik och denna logik transformeringar uttryck i kanoniskt format, som sedan sparas till programmässiga entiteter för efterföljande aktiverar av dialogrutan.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Sessionen återanrop](./13-session-callbacks.md)

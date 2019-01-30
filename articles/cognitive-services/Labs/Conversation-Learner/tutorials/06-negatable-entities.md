---
title: Hur du använder negeras entiteter med en modell för konversationen Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder negeras entiteter med en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: fea950e2c13d9b5ce0c3619990961e611edd6626
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207385"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Hur du använder negeras entiteter med en konversation Learner modell

Den här självstudien visas ”Negatable”-egenskapen för entiteter.

## <a name="video"></a>Video

[![Negeras entiteter självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Egenskapen ”Negatable” för en entitet kan du märka både normal (positiv) och negativa instanser av entiteten, lär baserat på positiva och negativa modeller och rensa värdet för en befintlig entitet. Entiteter med sina ”Negatable” egenskapsuppsättning kallas negeras entiteter i konversationen Leaner.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på ”ny modell”.
2. Skriv ”NegatableEntity” i fältet ”namn” och tryck på RETUR.
3. Klicka på knappen ”Skapa”.

### <a name="entity-creation"></a>Skapa en entitet

1. Klicka på ”entiteter” och knappen ”ny entitet” på den vänstra panelen.
2. Välj ”anpassad” för ”entitetstypen”.
3. Skriv ”name” för ”entitetsnamnet”.
4. Markera kryssrutan ”Negatable”.
    - Kontrollerar den här egenskapen gör det möjligt för användarna att ange ett värde för entiteten eller säga att det är något *inte* ett värde för entiteten. Resultatet är i det senare fallet borttagning av det matchande entity-värdet.
5. Klicka på knappen ”Skapa”.

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”Jag vet inte ditt namn”.
3. Skriv i fältet ”diskvalificera berättigar”, ”name”.
4. Klicka på knappen ”Skapa”.

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”Jag vet ditt namn. Det är $name ”.
3. Klicka på knappen ”Skapa”.

> [!NOTE]
> ”Namn” entitet läggs automatiskt som en ”krävs enheter” med referens i svaret.

Nu har du två åtgärder.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>Träna modellen

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ange ”hello”.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”jag vet inte ditt namn”.
    - Den: e percentilen är 100%, eftersom den enda giltiga åtgärden baserat på begränsningarna.
5. På panelen chatt står det ”Skriv meddelandet...”, ange ”mitt namn är Frank”
6. Välj ”Frank”, och välj etiketten ”+ namn”
    - Det finns två instanser för entiteten för ”name”: ”+ namn” och ”-namnet”.  (+) Dessutom lägger till eller skriver över värdet. (-) Minustecken tar du bort värdet.
7. Klicka på knappen ”poäng åtgärder”.
    - ”Namn” entitet nu är definierade som ”Frank” i minnet för den modellen, så den ”jag vet ditt namn. Det är $name ”är åtgärden tillgänglig.
8. Välj svar ”jag vet ditt namn. Det är $name ”.
9. På panelen chatt står det ”Skriv meddelandet...”, ange ”mitt namn är inte Frank”.
10. Välj ”Frank” och välj etiketten ”-namnet”
    - Väljer vi ”-namnet” Rensa entitetens aktuella värdet.
11. Klicka på knappen ”poäng åtgärder”.
12. Välj svar ”jag vet inte ditt namn”.
13. På panelen chatt står det ”Skriv meddelandet...”, ange ”mitt namn är Susan”.
14. Välj Susan, och välj etiketten ”+ namn”

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flera värden entiteter](./07-multi-value-entities.md)

---
title: Hur du använder negeras entiteter med ett program i konversationen deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig använda negeras entiteter med ett samtal deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3d65376c9c43ee1407468f3e8bf3e058048bd556
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354066"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-application"></a>Hur du använder negeras entiteter med ett samtal deltagaren program

Den här kursen visar egenskapen ”negeras” för entiteter.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Markera en åtgärd som ”negeras” om användaren kan ”rensa” en entitetsvärdet som ”Nej, jag vill inte $entity” eller ”Nej, inte $entity”. Till exempel ”Nej, jag inte vill lämna Boston”.

Concretely, om egenskapen ”negeras” för en entitet är:

- När etiketter entitet nämns, kan du märka både normala (positivt) förekomster av en enhet och en ”negativ” förekomster av entiteten
- THOMAS lär sig två modeller för entitet: en för positivt instanser och en andra för negativa instanser
- Effekten av en negativ instans av en entitet är att rensa värdet från variabeln entitet (om den finns)

## <a name="steps"></a>Steg

### <a name="create-the-application"></a>Skapa programmet

1. Klicka på ny App i Webbgränssnittet,
2. Ange NegatableEntity i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter sedan ny entitet.
2. Ange namn i enhetsnamnet.
3. Kontrollera negeras.
    - Detta anger användaren kommer att kunna ange ett värde för entiteten eller Säg är något *inte* värdet för entiteten. I det senare fallet leder detta tar bort ett motsvarande värde för entiteten.
3. Klicka på Skapa.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och sedan ny åtgärd
2. Skriv ”vet inte ditt namn' svar.
3. Ange namn i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden.

1. Klicka på Åtgärder sedan ny åtgärd för att skapa en andra åtgärd.
3. I svaret, skriver du ”Jag vet ditt namn. Det är $name'.
4. Klicka på Skapa

Nu har du två åtgärder.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”jag vet inte ditt namn'
    - Observera att får en 100% poäng eftersom det är bara giltig åtgärd.
2. Ange 'mitt namn är david'
3. Välj david, och välj etiketten ”+ namn”
    - Observera att det finns två instanser av 'name': '+ name' och '-name'.  Dessutom innebär att vi tillhandahåller värdet. Minus innebär registreras det i systemet att något som inte är värdet.
5. Klicka på poäng åtgärder
    - Obs namn-värde är nu i den bot minne.
    - ”Jag vet ditt namn. Det är $name' är endast tillgängligt svaret. 
6. Välj ”jag vet ditt namn. Det är $name'.

Vi ska försöka ta bort entiteten negeras:

7. Ange ”gäller inte david'.
    - Meddelandet har ”inte” valts som namn baserat på det tidigare mönstret. Det är fel.
2. Klicka på ”inte” och sedan ett rött x. 
3. Klicka på 'david'.
    - Detta är nu en negativ entitet kommunikation att detta inte är värdet för entiteten namn.
2. Välj ”-namnet '.
3. Klicka på poäng åtgärder.
    - Observera att värdet har det tagits bort från minnet.
2. Välj ”jag vet inte ditt namn', vilket är den enda åtgärden.

Därefter visar vi hur ett nytt värde för namnet kan anges.

3. Ange ”john' som namn. Välj ”john” och klicka på namn.
4. Klicka på poäng åtgärder.
5. Välj ”jag vet ditt namn. Det är $name'.

Nu du ersätta det angivna namnet.

6. Ange 'mitt namn är susan'.
7. Välj ”jag vet ditt namn. Det är $name'.
7. Klicka på poäng åtgärder.
8. Meddelande **susan** har skrivit över **john** i entiteten värden.
9. Ange ”gäller inte susan'.
    - Observera att systemet har betecknats detta som en negativ instans.
2. Klicka på poäng åtgärder.
3. Välj ”jag vet inte ditt namn', vilket är den enda åtgärden.
7. Klicka på klar lärare.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flervärde entiteter](./6-multi-value-entities.md)

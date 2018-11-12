---
title: Hur du använder negeras entiteter med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder negeras entiteter med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: df6499410da3ae67715ade29dbc3cc4146fc2265
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51231999"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Hur du använder negeras entiteter med en modell för Konversationsdeltagare

Den här självstudien visas ”negeras” egenskapen för entiteter.

## <a name="video"></a>Video

[![Självstudien 5 Preview](https://aka.ms/cl-tutorial-05-preview)](https://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Markera en åtgärd som ”negeras” om användaren kan ”rensa” ett värde för entiteten, som i ”Nej, jag vill inte ha $entity” eller ”Nej, inte $entity”. Till exempel ”Nej, jag inte vill lämna från Boston”.

Concretely, om egenskapen ”negeras” för en entitet har angetts:

- När märkning entitet omnämnanden, kan du märka både normal (positiv) instanser av en entitet och en ”negativ” instanser av entiteten
- LUIS lär sig två entitet modeller: en för positivt instanser och en andra för negativa instanser
- Effekten av en negativ instans av en entitet är att ta bort värdet från entiteten variabel (om det finns)

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange NegatableEntity i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter och ny entitet.
2. Ange namn i enhetens namn.
3. Kontrollera negeras.
    - Den här egenskapen anger användaren kommer att kunna ange ett värde för entiteten eller säga att det är något *inte* värdet för entiteten. I det senare fallet leder detta tar bort ett matchande värde för entiteten.
3. Klicka på Skapa.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och ny åtgärd
2. I svaret, skriver du ”Jag vet inte ditt namn”.
3. Ange namn i diskvalificera entiteter.
3. Klicka på Skapa

Skapa sedan den andra åtgärden.

1. Klicka på åtgärder och sedan ny åtgärd för att skapa en andra åtgärd.
3. I svaret, skriver du ”Jag vet ditt namn. Det är $name'.
4. Klicka på Skapa

Nu har du två åtgärder.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”jag vet inte ditt namn”
    - Poängen är 100% eftersom det är den enda giltiga åtgärden.
2. Ange 'mitt namn är david'
3. Välj david, och välj etiketten ”+ namn”
    - Det finns två instanser av ”name”: ”+ namn” och ”-namnet”.  (+) Dessutom lägger till eller skriver över värdet. (-) Minustecken tar du bort värdet.
5. Klicka på poäng åtgärder
    - Namn-värde är nu i robotens minne.
    - ”Jag vet ditt namn. Det är $name ”är det enda tillgängliga svaret. 
6. Välj ”jag vet ditt namn. Det är $name'.

Nu ska vi försöka ta bort entiteten negeras:

7. Ange ”mitt namn is not david”.
    - Meddelandet har ”not” valts som namn baserat på det tidigare mönstret. Den här etiketten är felaktig.
2. Klicka på ”inte” och sedan ett rött x. 
3. Klicka på ”david”.
    - Det här är nu en negativ entitet kommunicerar att detta inte är värdet för entiteten namn.
2. Välj '-name'.
3. Klicka på poäng åtgärder.
    - Observera att värdet har det tagits bort från minnet.
2. Välj ”jag vet inte ditt namn”, vilket är den enda åtgärden.

Därefter visar vi hur ett nytt värde för namn kan anges.

3. Ange ”john” som namn. Markera ”john” och klicka på namnet.
4. Klicka på poäng åtgärder.
5. Välj ”jag vet ditt namn. Det är $name'.

Nu du ersätta det angivna namnet.

6. Ange 'mitt namn är susan'.
7. Välj ”jag vet ditt namn. Det är $name'.
7. Klicka på poäng åtgärder.
8. Observera **susan** har över **john** i entitetsvärden.
9. Ange ”mitt namn is not susan”.
    - Observera att systemet har märkts detta som en negativ instans.
2. Klicka på poäng åtgärder.
3. Välj ”jag vet inte ditt namn”, vilket är den enda åtgärden.
7. Klicka på klar undervisning.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Flera värden entiteter](./6-multi-value-entities.md)

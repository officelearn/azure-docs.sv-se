---
title: Hur du använder vänta och icke-wait-åtgärder med en konversation deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig använda vänta och icke-wait-åtgärder med en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: bb2cbd55b6c8be51213095926bb592169613d1fc
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353946"
---
# <a name="wait-and-non-wait-actions"></a>Vänta en stund och icke-wait-åtgärder

Den här kursen visar skillnaden mellan vänta och icke-wait-åtgärder i konversationen deltagaren.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

- Vänta åtgärd: när systemet har en ”vänta”-åtgärd, den stoppas vidta åtgärder och vänta på användarindata.
- Icke-wait-åtgärd: när systemet har en ”icke vänta”-åtgärd, kommer den omedelbart välja en annan åtgärd (utan att vänta på användaren inpu först).

## <a name="steps"></a>Steg

### <a name="create-a-new-app"></a>Skapa en ny app

1. Klicka på ny App i Webbgränssnittet,
2. Ange WaitNonWait i namn. Klicka på Skapa.

### <a name="create-the-first-wait-action"></a>Skapa den första åtgärden vänta

1. Klicka på Åtgärder sedan ny åtgärd.
2. Ange 'vilken djuret vill du fortsätta?' svar.
    - Detta är en vänta åtgärd, se till att vänta tills ikryssad svar.
3. Klicka på klart.

### <a name="create-a-non-wait-action"></a>Skapa en icke-Wait-åtgärd

1. Klicka på ny åtgärd
2. Skriv ”kor säger moo' svar.
3. Avmarkera väntan på svar kryssrutan.
4. Klicka på Skapa

### <a name="create-a-second-non-wait-action"></a>Skapa en andra icke-Wait-åtgärd

1. Klicka på ny åtgärd
2. Skriv ”lägst säger quack' svar.
3. Avmarkera väntan på svar kryssrutan.
4. Klicka på Skapa

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Typen ”Hej”
3. Klicka på poäng åtgärder och välj 'vilken djuret vill du fortsätta?'.
4. Ange 'ko'
5. Klicka på poäng åtgärder och välj 'Kor säger moo'.
    - Observera att bot inte väntar på indata och tar nästa åtgärd.
2. Välj 'vilken djuret vill du fortsätta?'.
3. Ange 'Anka'
5. Klicka på poäng åtgärder och välj 'Lägst säger quack'.

![](../media/tutorial2_dialogs.PNG)

Notera bot svar med avseende på vänta och icke-wait-åtgärder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Introduktion till entiteter](./3-introduction-to-entities.md)

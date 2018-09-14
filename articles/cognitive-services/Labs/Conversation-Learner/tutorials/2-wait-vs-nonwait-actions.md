---
title: Hur du använder vänta och icke-wait-åtgärder med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder vänta och icke-wait-åtgärder med en Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f349dab23b9156d3a5656e8275533ebe6a82cdf9
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/13/2018
ms.locfileid: "45540941"
---
# <a name="wait-and-non-wait-actions"></a>Vänta och icke-wait-åtgärder

Den här självstudien visar skillnaden mellan vänta åtgärder och åtgärder som icke-wait den Konversationsdeltagare.

## <a name="video"></a>Video

[![Självstudie 2-förhandsversion](http://aka.ms/cl-tutorial-02-preview)](http://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

- Vänta åtgärd: när systemet har en ”vänta”-åtgärd, den slutar att vidta åtgärder och vänta på användarindata.
- Icke-wait-åtgärden: när systemet tar en ”icke-wait”-åtgärd kan det omedelbart ska välja en annan åtgärd (utan att vänta på användarindata först).

## <a name="steps"></a>Steg

### <a name="create-a-new-model"></a>Skapa en ny modell

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange WaitNonWait i namn. Klicka på Skapa.

### <a name="create-the-first-wait-action"></a>Skapa den första Wait-åtgärden

1. Klicka på åtgärder och ny åtgärd.
2. Ange ”vilken de kan vill du”? i svaret.
    - Det här är en Wait-åtgärd, så vänta tills ikryssad svar.
3. Klicka på Skapa.

### <a name="create-a-non-wait-action"></a>Skapa en icke-Wait-åtgärd

1. Klicka på ny åtgärd
2. Skriv ”kor säger moo' som svar.
3. Avmarkera väntan på svar kryssrutan.
4. Klicka på Skapa

### <a name="create-a-second-non-wait-action"></a>Skapa en andra icke-Wait-åtgärd

1. Klicka på ny åtgärd
2. Skriv ”ankor säger quack' som svar.
3. Avmarkera väntan på svar kryssrutan.
4. Klicka på Skapa

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Typen ”hello”
3. Klicka på poäng åtgärder och välj ”vilken de kan vill”?.
4. Ange ”ko'
5. Klicka på poäng åtgärder och välj ”kor säger moo'.
    - Roboten väntar inte på indata och tar nästa åtgärd.
2. Välj ”vilken de kan vill”?.
3. Ange ”ankor'
5. Klicka på poäng åtgärder och välj ”ankor säger quack'.

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> Sekvens med bot-svar för vänta och icke-wait-åtgärder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Introduktion till entiteter](./3-introduction-to-entities.md)

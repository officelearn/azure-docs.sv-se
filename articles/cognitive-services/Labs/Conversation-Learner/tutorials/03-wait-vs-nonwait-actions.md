---
title: Hur du använder vänta och icke-wait-åtgärder med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder vänta och icke-wait-åtgärder med en Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2869e6c59388adf548e5f239d3a831a5a3f060dd
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55214270"
---
# <a name="wait-and-non-wait-actions"></a>Vänta och icke-wait-åtgärder

Den här självstudien visar skillnaden mellan vänta åtgärder och åtgärder som icke-wait den Konversationsdeltagare.

## <a name="video"></a>Video

[![Vänta vs icke-Wait självstudien förhandsversion](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

- Vänta åtgärd: När systemet har en ”vänta”-åtgärd, kommer den slutar att vidta åtgärder och vänta på användarindata.
- Icke-wait-åtgärden: När systemet tar en ”icke-wait”-åtgärd kan väljer det omedelbart en annan åtgärd (utan att vänta på användarindata).

## <a name="steps"></a>Steg

### <a name="create-a-new-model"></a>Skapa en ny modell

1. I Webbgränssnittet, klickar du på en ny modell
2. I fältet ”Name”, skriver du ”vänta icke-Wait”, träffar ange eller klicka på knappen ”Skapa”.

### <a name="create-the-first-two-wait-actions"></a>Skapa de första två vänta åtgärder

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet, skriver du ”vilka pizza vill du”? i ”Robotens svaret...”.
    - Det här är en Wait-åtgärd, så vi ”vänta för Response” ikryssad.
3. Klicka på knappen ”Skapa”.
4. Upprepa de här stegen, skapa en annan åtgärd med ”platt på väg”! som svar av roboten.

### <a name="train-using-those-wait-actions"></a>Träna med de åtgärder som väntar

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ange ”Hi”. 
    - Det här simulerar användarens sida för konversationen.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”vilka pizza vill du”?.
5. Som användare, svarar med ”Margherita”.
6. Klicka på knappen ”poäng åtgärder”.
7. Välj svar ”platt på väg”!.
8. Klicka på knappen ”Spara”.

### <a name="create-a-non-wait-action-while-training"></a>Skapa en icke-Wait-åtgärden vid utbildning
Men du kan skapa den icke-Wait-åtgärden som du gjorde tidigare, kan du också skapa den från i en utbildningssession.
1. Klicka på knappen ”Ny träna dialogrutan”.
2. Som användartyp av i ”Hello”.
3. Klicka på knappen ”poäng åtgärder”.
4. Klicka på den ”+ åtgärd” knappen. 
    - Då kommer du till dialogrutan välbekanta ”skapa en åtgärd”.
5. Skriv Robotens svar som ”Välkommen till Pizza Bot”!
6. Avmarkera kryssrutan ”Vänta för svar”.
7. Klicka på knappen ”Skapa”.
    - Observera att Bot svarar omedelbart med ”Välkommen till Pizza Bot”! Ange en annan Bot-svar och att du igen. Det beror på att Robotens svaret var icke-Wait-åtgärden som vi precis har skapat.
9. Välj svar ”vilka pizza vill du”?.
10. Som användare, svarar med ”Margherita”.
11. Klicka på knappen ”poäng åtgärder”.
12. Välj svar ”platt på väg”!.
13. Klicka på knappen ”Spara”.

> [!NOTE]
> Sekvens med bot-svar för vänta och icke-wait-åtgärder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Introduktion till entiteter](./04-introduction-to-entities.md)

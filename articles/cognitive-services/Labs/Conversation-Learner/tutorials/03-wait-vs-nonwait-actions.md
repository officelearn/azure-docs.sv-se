---
title: Använda wait-och icke-wait-åtgärder med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder Wait-och icke-wait-åtgärder med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5d9a67f0b413b624baec369a219547cf18e9dbd2
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705607"
---
# <a name="wait-and-non-wait-actions"></a>Vänta och ej väntande åtgärder

I den här självstudien visas skillnaden mellan åtgärder för att vänta och inte vänta i Conversation Learner.

## <a name="video"></a>Video

[![Vänta på för hands version av självstudie vid ej Waiting](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information

- Vänte åtgärd: När systemet har tagit en "vänta"-åtgärd slutar det att vidta åtgärder och väntar på användarindata.
- Ej wait-åtgärd: När systemet har åtgärdat en "icke-Waite"-åtgärd kommer det omedelbart att välja en annan åtgärd (utan att vänta på användarindata).

## <a name="steps"></a>Steg

### <a name="create-a-new-model"></a>Skapa en ny modell

1. I webb gränssnittet klickar du på ny modell
2. I fältet "namn" skriver du "vänta på väntar", trycker på RETUR eller klickar på knappen "skapa".

### <a name="create-the-first-two-wait-actions"></a>Skapa de första två wait-åtgärderna

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." anger du "vilken pizza vill du ha?".
    - Detta är en vänte åtgärd, så lämna rutan "vänta på svar" markerad.
3. Klicka på knappen "skapa".
4. Upprepa dessa steg, skapa en annan åtgärd med "pizza på vägen!" som robotens svar.

### <a name="train-using-those-wait-actions"></a>Träna användning av dessa wait-åtgärder

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Hej". 
    - Detta simulerar användarens sida av konversationen.
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "vilken pizza vill du ha?".
5. Som användaren svarar med, "Margherita".
6. Klicka på knappen "Poäng åtgärder".
7. Välj svaret "pizza på vägen!".
8. Klicka på knappen Spara.

### <a name="create-a-non-wait-action-while-training"></a>Skapa en icke-wait-åtgärd medan du tränar
Även om du kan skapa en icke-wait-åtgärd som du gjorde tidigare, kan du också skapa den inifrån en övningssession.
1. Klicka på knappen Ny träna dialog.
2. Som användar typ i, "Hello".
3. Klicka på knappen "Poäng åtgärder".
4. Klicka på knappen "+ åtgärd". 
    - Det tar dig till den välbekanta dialog rutan "skapa en åtgärd".
5. Skriv robotens svar som, "Välkommen till pizza bot!"
6. Avmarkera kryss rutan "vänta på svar".
7. Klicka på knappen "skapa".
    - Observera att roboten svarar omedelbart med, "Välkommen till pizza bot!" och du ombeds att ange ett annat robot svar. Detta beror på att robotens svar var den icke-wait-åtgärd som vi nyss skapade.
9. Välj svaret, "vilken pizza vill du ha?".
10. Som användaren svarar med, "Margherita".
11. Klicka på knappen "Poäng åtgärder".
12. Välj svaret "pizza på vägen!".
13. Klicka på knappen Spara.

> [!NOTE]
> Sekvensen av robot svar med avseende på vänte-och icke-wait-åtgärder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Introduktion till entiteter](./04-introduction-to-entities.md)

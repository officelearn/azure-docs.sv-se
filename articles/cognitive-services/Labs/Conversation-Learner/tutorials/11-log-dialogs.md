---
title: Logga dialog rutor i en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du loggar dialoger i en Conversation Learners modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 388942e8195048d7528a54e5a290f1724c8e876b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703979"
---
# <a name="how-to-log-dialogs-in-a-conversation-learner-model"></a>Så här loggar du dialog rutor i en Conversation Learner modell

Den här självstudien visar hur logg dialog rutor används för att bättre träna Conversation Learner modeller från inspelade interaktioner med verkliga användare i världen.

## <a name="video"></a>Video

[![Förhands granskning av dialog rutor för loggar](https://aka.ms/cl_Tutorial_v3_LogDialogs_Preview)](https://aka.ms/cl_Tutorial_v3_LogDialogs)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

och väder modellen som skapats i tidigare självstudier.

## <a name="details"></a>Information
Logg dialog rutor registreras loggar av din robots interaktion med slutanvändare. Genom att använda dessa logg dialog rutor kan du åtgärda enhets etiketter och åtgärds val för att förbättra modellens prestanda och övergripande system prestanda.

## <a name="steps"></a>Steg

I webb gränssnittet klickar du på "Importera självstudier" och väljer modellen med namnet "självstudie-11-LogDialogs".

Den här modellen innehåller en entitet med namnet "stad" och åtgärder som har utformats för att svara på frågor om väder i denna stad. Två träna-dialog rutor användes för att träna modellen så att prestanda förväntningarna är lite låga. Modellen skulle förbättra med ytterligare utbildning och exponering för verkliga användar interaktioner i hela världen.

### <a name="create-a-new-conversation"></a>Skapa en ny konversation

1. Klicka på "Logga dialog rutor" i den vänstra panelen, sedan på knappen "ny logg dialog ruta".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Austin väder prognos"
3. Klicka på knappen "Slutför testning".
4. Klicka på logg dialog rutan "Austin väder prognos" i listan.
5. Klicka på uttryck "Austin väder prognos" i Chat-panelen.
6. Klicka på "Austin" och klicka sedan på "stad" från listan entitet.
7. Klicka på knappen Skicka ändringar.
    - Den här ändringen i enhet svärdet orsakar efterföljande ändringar i konversationen eftersom vi har nya enhets värden i minnet. Senare åtgärder har troligt vis blivit ogiltiga, särskilt för entiteten "ort".
8. Klicka på "vilken ort?" uttryck på panelen chat.
9. Välj svaret, "Väder i Austin är förmodligen solig".
10. Klicka på knappen "Spara som träna".
    - Träningen har inaktiverats omedelbart

![](../media/T11_logdialog.png)

En sista anteckning. Beroende på affärs behov kan funktionen konversations loggning stängas av genom att gå till Inställningar och avmarkera "Logga konversationer".

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Återanrop för enhets identifiering](./12-entity-detection-callback.md)

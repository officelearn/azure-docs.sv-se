---
title: Så här skapar du en Conversation Learners modell Hello World-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en Conversation Learners modell för Hello World.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bb1d053af8813f05872c56d3b5609f2d7d7d5d8c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705641"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Så här skapar du en Hello World modell med Conversation Learner

I den här självstudien får du lära dig hur du kommer igång med Conversation Learner, inklusive hur du skapar åtgärder, lär dig att använda roboten interaktivt och göra korrigeringar av loggade dialog rutor som kommer från slutanvända

## <a name="video"></a>Video

[![För hands version av Hello World självstudie](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Krav
Om du inte redan har gjort det måste du först se till att alla installations steg har `.env` slutförts, inklusive att skapa en fil med din Luis redigerings nyckel.  Mer information finns i [snabb start](../quickstart.md) .

Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="steps"></a>Steg

Starta på Start sidan i webb gränssnittet.

### <a name="create-the-model"></a>Skapa modellen
1. Klicka på knappen Ny modell.
2. I fältet Namn anger du Hello World.
3. Klicka på knappen "skapa".

Nu bör du se vyn över modellen som du har skapat.

### <a name="create-an-action"></a>Skapa en åtgärd
1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
    - En åtgärd kan vara ett SMS-meddelande som Conversation Learner returnerar till användaren, ett API-anrop eller ett kort.
2. I "robotens svar..." fält typen Hello.
    - Detta är svaret som roboten kommer att returnera.
3. Klicka på knappen "skapa".

Du har skapat den första åtgärden som roboten kan utföra, d.v.s. returnera ett text svar.

### <a name="train-dialogs"></a>Träna dialog rutor
Det är här du tränar modellen om hur du svarar på användarens yttranden.

#### <a name="first-training-dialog"></a>Dialog rutan första utbildning

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. Skriv "Hej" och tryck på RETUR.
    - Ett exempel på vad användaren kan säga i början av en konversation.
3. Klicka på knappen "Poäng åtgärder".
4. Välj "Hej".
    - Du har precis slutfört en fullständig aktivering i den här exempel dialog rutan. 
5. Skriv användaren svarar "Hej".
6. Klicka på knappen "Poäng åtgärder".
7. Klicka på knappen "+ åtgärd".
8. Skriv "Hej!" i "robotens svar..." Klicka sedan på knappen "skapa".
    - Observera att roboten svarade med den åtgärd som du nyss skapade.
9. Klicka på knappen Spara. 
    - Detta kommer att sluta och spara den här övnings dialog rutan.

Nu har du en tränings dialog ruta i modellen, tillsammans med en enda entitet och två åtgärder.

#### <a name="second-training-dialog"></a>Dialog ruta för andra utbildningen
Låt oss göra en mer utbildning och se hur roboten svarar.

1. Klicka på knappen Ny träna dialog.
2. Skriv in, "Hej"
    - Detta liknar den första dialog rutan och vi förväntar oss att få en bättre Poäng från bot.
3. Klicka på knappen "Poäng åtgärder".
    - Positionen och poängen kanske fortfarande inte är tillräckligt exakta och kan kräva ytterligare utbildning.
4. Välj "Hej".
5. Skriv användarens svar, "bye".
6. Klicka på knappen "Poäng åtgärder".
7. Välj "Hej!"
8. Klicka på knappen Spara.

### <a name="log-dialogs"></a>Logg dialog rutor
Det är här du testar, visar och korrigerar konversationer som du eller riktiga användare har haft med din robot.

#### <a name="test-the-model-as-an-end-user"></a>Testa modellen som en slutanvändare
1. Klicka på "Logga dialog rutor" i den vänstra panelen, sedan på knappen "ny logg dialog ruta".
2. Skriv "Hej där".
3. Vänta en kort tid, roboten bör svara automatiskt med "Hello"
4. Ange "byebye"
5. Vänta en kort stund och försök sedan att svara automatiskt med "Hello".
6. Klicka på knappen "Slutför testning".

#### <a name="view-and-correct-a-user-conversation"></a>Visa och korrigera ett användar samtal
Med hjälp av logg dialog rutor kan du Visa en lista över konversations användare som har med din robot. Du kan också redigera dem för att korrigera robotens svar och spara interaktioner som utbildnings dialog rutor. Gör så här:
1. I rutnätet klickar du på loggen för konversationen.
2. Klicka på den senaste robot åtgärden t. ex. "Hej".
3. Välj "Hej!" för att korrigera bot.
4. Klicka på knappen "Spara som träna".

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Introduktion till utbildning](./02-intro-to-training.md)

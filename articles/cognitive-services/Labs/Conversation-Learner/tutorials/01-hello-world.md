---
title: Så här skapar du en ”Hello World” Konversationsdeltagare modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en ”Hello World” Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: b2c43ad2475ab75d251e57fca6009eb1fa2e1f00
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55225303"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Hur du skapar en ”Hello World”-modell med Konversationsdeltagare

Den här självstudiekursen visar hur du kommer igång med Konversationsdeltagare, inklusive att skapa åtgärder, undervisar roboten interaktivt och att göra korrigeringar av loggade dialogrutor som kommer från slutanvändarna.

## <a name="video"></a>Video

[![Hello World-självstudie förhandsversion](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Krav
Om du inte redan gjort först se till att alla steg i installationen har slutförts, inklusive att skapa en `.env` filen med din LUIS redigering av nyckeln.  Se [snabbstarten](../quickstart.md) information.

Den här självstudien krävs att Allmänt självstudien Bot körs

    npm run tutorial-general

## <a name="steps"></a>Steg

Starta på startsidan i Webbgränssnittet.

### <a name="create-the-model"></a>Skapa modellen
1. Klicka på knappen ”ny modell”.
2. Ange ”Hello World” i fältet ”namn”.
3. Klicka på knappen ”Skapa”.

Du bör nu se vyn av modellen som du skapade.

### <a name="create-an-action"></a>Skapa en åtgärd
1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
    - En åtgärd kan vara ett textmeddelande som Konversationsdeltagare tillbaka till användaren, ett API-anrop eller ett kort.
2. I ”Robotens svaret...” fälttyp ”Hello”.
    - Det här är ett svar som returnerar roboten.
3. Klicka på knappen ”Skapa”.

Du har skapat den första åtgärden som roboten kan utföra, dvs. returnera ett textsvar.

### <a name="train-dialogs"></a>Träna dialogrutor
Det här är där du tränar modellen på hur du svarar på användaren yttranden.

#### <a name="first-training-dialog"></a>Den första utbildning dialogrutan

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. Typen ”Hi” tryck på RETUR.
    - Som ett exempel på vad användaren kan det stå i början av en konversation.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj ”Hello”.
    - Du klar är med en fullständig Stäng i det här exemplet dialogrutan. 
5. Skriv svaret från användaren ”hejdå”.
6. Klicka på knappen ”poäng åtgärder”.
7. Klicka på ”+ åtgärd” knappen.
8. Typen ”Goodbye”! fältet ”Robotens svar...” och sedan på knappen ”Skapa”.
    - Observera att roboten svarade med som åtgärd du precis skapade.
9. Klicka på knappen ”Spara”. 
    - Detta kommer Avsluta och spara den här dialogrutan för utbildning.

Nu har du en utbildning dialogrutan i modellen, tillsammans med en enda entitet och två åtgärder.

#### <a name="second-training-dialog"></a>Dialogrutan för andra utbildning
Låt oss göra en mer utbildning och se hur roboten svarar.

1. Klicka på knappen ”Ny träna dialogrutan”.
2. Skriv in ”Hej”
    - Detta liknar den första dialogrutan och vi förväntar oss att få en bra poäng från roboten.
3. Klicka på knappen ”poäng åtgärder”.
    - Position och poäng fortfarande kanske inte tillräckligt aktuell och kan kräva ytterligare utbildning.
4. Välj ”Hello”.
5. Skriv svaret från användaren ”Hej”.
6. Klicka på knappen ”poäng åtgärder”.
7. Välj ”Goodbye”!
8. Klicka på knappen ”Spara”.

### <a name="log-dialogs"></a>Log-dialogrutor
Detta kan du testa, visa och korrigera konversationer som du eller användarna har fått med din robot.

#### <a name="test-the-model-as-an-end-user"></a>Testa modellen som en användare
1. Klicka på ”Log-dialogrutor” och knappen ”Ny Log dialogruta” på den vänstra panelen.
2. Skriv ”Hej”.
3. Vänta en stund roboten bör svara automatiskt med ”Hello”
4. Ange 'byebye'
5. Vänta en stund, igen roboten bör svara automatiskt med ”Hello”.
6. Klicka på knappen ”klar testning”.

#### <a name="view-and-correct-a-user-conversation"></a>Visa och åtgärda en användare konversation
Med hjälp av Log-dialogrutor, du kan visa listan över konversationer användare lagras med din robot. Du kan också redigera dem för att korrigera Robotens svar och spara interaktionerna som utbildning-dialogrutor. Att göra det:
1. Klicka på en logg för konversationen i rutnätet.
2. Klicka på den senaste åtgärden Bot t.ex. "Hello".
3. Välj ”Goodbye”! Korrigera roboten.
4. Klicka på knappen ”Spara som träna dialogrutan”.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Introduktion till utbildning](./02-intro-to-training.md)

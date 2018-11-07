---
title: Så här skapar du en ”Hello World” Konversationsdeltagare modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en ”Hello World” Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 271141f24ff729fc99210af67ad769a5ef83a65c
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51242729"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Hur du skapar en ”Hello World”-modell med Konversationsdeltagare

Den här självstudiekursen visar hur du kommer igång med Konversationsdeltagare, inklusive att skapa åtgärder, undervisar interaktivt och göra ändringar av loggade dialogrutor för slutanvändarna.

## <a name="video"></a>Video

[![Självstudie 1 Preview](https://aka.ms/cl-tutorial-01-preview)](https://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>Krav
Om du inte redan gjort först se till att alla steg i installationen har slutförts, inklusive att skapa en `.env` filen med din LUIS redigering av nyckeln.  Se [snabbstarten](https://github.com/Microsoft/ConversationLearner-Samples) information.

Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="steps"></a>Steg

Starta på startsidan i Webbgränssnittet.

### <a name="create-the-model"></a>Skapa modellen
1. Klicka på ny modell
2. Ange i fältet Namn på Hello World
3. Klicka på Skapa

### <a name="create-an-action"></a>Skapa en åtgärd

1. Klicka på Hello World-modellen för att starta den
2. Klicka på åtgärder och ny åtgärd
    - En åtgärd kan vara ett textmeddelande som Konversationsdeltagare tillbaka till användaren, ett API-anrop eller ett kort.
3. I svaret, skriver du ”Hello World”!
    - Det här är ett svar som returnerar roboten
4. Klicka på Skapa

Du har skapat den första roboten kan t.ex. göra returnera ett textsvar.

### <a name="train-the-bot"></a>Träna roboten

#### <a name="create-the-first-dialog"></a>Den första dialogrutan Skapa

1. Klicka på träna dialogrutor, därefter nytt Train-dialogrutan
2. Ange ett exempel på vad användaren kommer att säga i begging konversationens, till exempel ”hello”.
3. Klicka på poäng åtgärder
4. Välj ”Hello World”!
    - Detta skapar en dialogruta i sin tur ett exempel. 
2. Ange ”goodbye”
3. Klicka på poäng åtgärder
4. Klicka på Lägg till åtgärd och sedan ange klart! svar och klicka sedan på ”Skapa”
5. Klicka på klar undervisning. Detta avslutar den här dialogrutan för utbildning.

Nu har du en undervisning dialogrutan i systemet.

#### <a name="continue-teaching-the-bot"></a>Fortsätta undervisar roboten
Låt oss göra en mer utbildning och se hur roboten svarar.

1. Klicka på dialogrutan för nytt Train
2. Ange ”Hej det”
    - Detta liknar den första dialogrutan och vi förväntar oss att få en bra poäng från roboten.
2. Klicka på poäng åtgärd
    - Position och poäng kanske inte längre är tillräckligt aktuell och kräver ytterligare undervisning.
3. Klicka på Välj bredvid ”Hello World”!
4. Ange sedan ”bye”
5. Klicka på poäng åtgärder
6. Välj klart!
7. Klickar du på klar undervisning

![](../media/tutorial1_actions.PNG)

Vi kommer att göra en annan undervisning session om du vill se hur roboten fungerar.

Upprepa stegen ovan med hjälp av ”Hej” och ”byebye” och notera ändringarna i position och poängen för robotar svaret när du poäng högerklicksåtgärd.

Nu kan du upprepa steg med hjälp av ”howdy” och ”good bye” och Observera att bedömnings visar förbättringarna i poäng som anger roboten har lärt dig interaktionen.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testa roboten som en användare

1. Klicka på Log dialogrutor, därefter nytt Log-dialogrutan
2. Skriv ”Hej”
3. Sedan ”bye”

Du kan också prova att starta en konversation med ”bye” och Observera robotens svar.

### <a name="view-conversations-in-the-log-dialogs"></a>Visa konversationer i den Log-dialogrutor

I loggen dialogrutor, kan du visa listan över konversationer, uppdatera och spara samverkan som utbildning dialogrutor. Att göra det:

1. Klicka på logg över en konversation
2. Om konversationen ser bra ut klickar du på den senaste åtgärden t.ex. ”Goodbye”.
3. Klicka för att välja det föreslagna svaret. 
    - Du kan också välja eller lägga till en annan åtgärd.
4. Sedan klickar du på klar om du vill spara den som en dialogruta för utbildning.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vänta och icke-wait-åtgärder](./2-wait-vs-nonwait-actions.md)
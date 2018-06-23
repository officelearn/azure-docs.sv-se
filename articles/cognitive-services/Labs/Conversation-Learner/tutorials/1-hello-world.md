---
title: Så här skapar du ett program i ”Hello World” konversation deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en ”Hello World” konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 30026285ac6dda45d2f5e3718aae741b928cf242
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354099"
---
# <a name="how-to-create-a-hello-world-application-with-conversation-learner"></a>Hur du skapar ett ”Hello World”-program med konversation deltagaren

Den här kursen visar hur du kommer igång med konversation deltagaren, inklusive att skapa åtgärder, lära interaktivt och gör nödvändiga ändringar av loggade dialogrutor från slutanvändare.

## <a name="requirements"></a>Krav
Om du inte redan gjort först se till att alla steg i installationen har slutförts, inklusive att skapa en `.env` filen med din THOMAS redigering av nyckeln.  Se [Quickstart](https://github.com/Microsoft/ConversationLearner-Samples) mer information.

Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="steps"></a>Steg

Starta på startsidan i Webbgränssnittet.

### <a name="create-the-app"></a>Skapa appen
1. Klicka på ny App
2. Ange Hello World i fältet namn
3. Klicka på Skapa

### <a name="create-an-action"></a>Skapa en åtgärd

1. Klicka på Hello World-app för att starta den
2. Klicka på åtgärder och sedan ny åtgärd
    - En åtgärd kan vara ett textmeddelande konversation deltagaren returneras till användaren, ett API-anrop eller ett kort.
3. I svaret, skriver du ”Hello World”!
    - Detta är svaret på bot returneras
4. Klicka på Skapa

Du har skapat det första som gör bot d.v.s. besvaras text.

### <a name="train-the-bot"></a>Träna bot

#### <a name="create-the-first-dialog"></a>Skapa första dialogrutan

1. Klicka på tåget dialogrutor, sedan nya Train Dialog
2. Ange ett exempel på vad användaren säger i begging konversationens, till exempel ”hello”.
3. Klicka på poäng åtgärder
4. Välj ”Hello World”!
    - Detta skapar en aktivera en exempel-dialogruta. 
2. Ange ”goodbye”
3. Klicka på poäng åtgärder
4. Klicka på Lägg till åtgärd och sedan ange Goodbye! svar och klicka sedan på ”Skapa”
5. Klicka på klar lärare. Detta avslutar den här dialogrutan utbildning.

Nu har du en lärare dialogrutan i systemet.

#### <a name="continue-teaching-the-bot"></a>Fortsätta lära bot
Vi gör en mer utbildning och se hur bot svarar.

1. Klicka på ny Train dialogrutan
2. Ange ”Hej det '
    - Detta liknar första dialogrutan och vi räknar med att få ett bra resultat från bot.
2. Klicka på poäng åtgärd
    - Placering och poäng kanske inte längre är tillräckligt aktuell och kräver ytterligare lärare.
3. Klicka på Välj bredvid ”Hello World”!
4. Ange 'bye ”
5. Klicka på poäng åtgärder
6. Välj Goodbye!
7. Klicka på klar lärare

![](../media/tutorial1_actions.PNG)

Gör vi en annan lärare session för att se hur bot fungerar.

Upprepa stegen ovan med ”Hej” och ”byebye' och notera ändringarna i position och poängen för robotar svaret när du klickar på poäng åtgärd.

Nu kan du upprepa steg med hjälp av 'howdy' och 'good bye ”och Observera att bedömningsprofil visar förbättringar i resultat som anger bot har lärt dig interaktionen.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testa bot som en användare

1. Klicka på Logga dialogrutor, sedan nya loggen Dialog
2. Skriv ”Hej”
3. Sedan 'bye ”

Du kan också prova att starta en konversation med 'bye ”och notera den bot svar.

### <a name="view-conversations-in-the-log-dialogs"></a>Visa konversationer i loggen dialogrutor

I loggen dialoger kan du visa listan konversationer, uppdatera och spara interaktioner som utbildning dialogrutor. Att göra det:

1. Klicka på en logg för en konversation
2. Om konversationen ser bra ut klickar du på den senaste åtgärden t.ex. ”Goodbye”.
3. Klicka på det föreslagna svaret. 
    - Du kan också markera eller lägga till en annan åtgärd.
4. Klicka sedan på klar spara det som en dialogruta för utbildning.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Vänta en stund och icke-wait-åtgärder](./2-wait-vs-nonwait-actions.md)
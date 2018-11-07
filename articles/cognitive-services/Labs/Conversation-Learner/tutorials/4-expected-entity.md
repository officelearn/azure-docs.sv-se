---
title: Hur du använder egenskapen ”förväntades entitet” för Konversationsdeltagare åtgärder – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder egenskapen ”förväntades entitet” för en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9e685d2281330457e83abde0a8ac26e086da7479
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229653"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Hur du använder ”förväntades entitet”-egenskapen för åtgärder

Den här kursen visar fältet ”förväntades entitet” av åtgärder.

## <a name="video"></a>Video

[![Självstudie 4 förhandsversion](https://aka.ms/cl-tutorial-04-preview)](https://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Använd fältet ”förväntades entitet” i en åtgärd för att kommunicera med systemet som du förväntar dig användarens svar till en åtgärd är att ange en entitet.

Concretely, om fältet ”förväntades entitet” i en åtgärd har angetts till $entity och sedan på nästa användaren uttryck, kommer systemet:

1. Först som vanligt försök hitta entiteter med hjälp av machine learning beroende entitet extrahering modellen
2. Om inga entiteter finns i steg 1, sedan – som en tumregel – tilldela hela användaren uttryck $entity.
3. Anropa `EntityDetectionCallback` som vanligt och fortsätt sedan till val av åtgärd.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange ExpectedEntities i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter och ny entitet.
2. Ange namn i enhetens namn.
3. Klicka på Skapa

> [!NOTE]
> Denna typ är 'custom'. Det här värdet innebär att entiteten kan tränas.  Det finns även färdiga entiteter, vilket innebär att deras beteende inte kan ändras.  Dessa enheter beskrivs i den [Pre-Built entiteter självstudien](./7-built-in-entities.md).

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och ny åtgärd.
2. I svaret, skriver du ”vad heter du”?.
3. Ange $name i förväntat entiteter. Klicka på Spara.
    - Det här värdet innebär att om den här frågan ombeds och användarens svar inte har några entiteter som har identifierats, roboten anta att hela användarens svar är den här entiteten.
    - Entiteten läggs automatiskt som en disqualifying enhet. 
2. Klicka på åtgärder och sedan ny åtgärd för att skapa en andra åtgärd.
3. Skriv ”Hello $name' som svar.
    - Entiteten läggs automatiskt som en obligatorisk entitet.
4. Klicka på Spara.

Nu har du två åtgärder.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vad heter du”?
    - Svaret ”Hello $name' kan inte väljas eftersom det kräver entiteten $name definieras och $name inte är i robotens minnet.
2. Ange ”david”. 
    - Namnet är markerad som en entitet. Detta är tack vare tumregeln vi ställa in ovan väljer svaret som entiteten.
5. Klicka på poäng åtgärder
    - Namn-värde är nu i robotens minne.
    - ”Hello $name” är nu tillgänglig som ett svar. 
6. Välj ”Hello $name'.
7. Klicka på klar undervisning.

Här följer två exempel där den entiteten extrahering maskininlärningsmodell identifierar ett namn, så att tumregeln ”förväntades entitet” inte utlöses.

1. Klicka på dialogrutan för nytt träna.
2. Ange 'mitt namn är david'.
    - Modellen identifierar david som entiteten namn eftersom det har sett den här word innan.
2. Klicka på poäng åtgärder
3. Välj ”Hello $name'.
4. Ange 'mitt namn är susan'.
    - Modellen identifierar susan som namnet eftersom det redan har sett det här mönstret.
2. Klicka på poäng åtgärder.
2. Välj ”Hello susan'.
3. Klicka på klar undervisning.

I följande exempel, ”förväntades entitet” tumregeln utlöser, men det är felaktigt. Exemplen visar sedan hur du gör en korrigering.

1. Skriv ”Ring mig jose'.
    - Modellen kan inte identifiera namnet som en entitet.
2. Klicka på jose och välj namn.
3. Klicka på poäng åtgärder.
4. Välj hello $name.
5. Klicka på klar undervisning.
1. Klicka på dialogrutan för nytt träna.
2. Ange ”hello”.
3. Ange som svar på ”vad är namnet på din”, ”jag är kallas frank”.
    - Hela frasen är markerad. Det beror på att statistiska modellen inte gick att hitta ett namn, så tumregeln utlöstes och valt hela svaret som entiteten namn.
2. För att rätta till det, klickar på markerade frasen och sedan klicka på röda Papperskorgen. 
3. Välj frank och klicka sedan på namnet på.
2. Klicka på poäng åtgärder
3. Välj ”Hello $name'.
4. Klicka på klar undervisning.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Negeras entiteter](./5-negatable-entities.md)

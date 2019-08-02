---
title: Använda egenskapen "förväntad entitet" för Conversation Learner åtgärder-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder egenskapen "förväntad entitet" för en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 5fbe4f09d377b9f157368184ab26341782e9aed1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707325"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Använda egenskapen "förväntad entitet" för åtgärder

Den här självstudien visar egenskapen "förväntad entitet" för åtgärder.

## <a name="video"></a>Video

[![Förhands granskning av förväntad entitet](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information
Använd egenskapen "förväntad entitet" för en åtgärd för att spara användarens svar på den här åtgärden i en entitet.

När du lägger till entiteter i egenskapen "förväntade entitet" för en åtgärd kommer systemet att:

1. Börja med att försöka matcha entiteter med hjälp av modellen för extrahering av dator inlärnings enheter
2. Tilldela hela användarens uttryck till $entity baserat på heuristik om inga entiteter hittas
3. Ring `EntityDetectionCallback`och gå vidare till åtgärds val.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I webb gränssnittet klickar du på "ny modell".
2. Skriv "ExpectedEntities" i fältet "namn" och tryck på RETUR.
3. Klicka på knappen "skapa".

### <a name="entity-creation"></a>Skapa entitet

1. Klicka på entiteter i den vänstra panelen, sedan på knappen ny enhet.
2. Välj anpassad tränad för enhets typen.
3. Skriv "name" som namn på entiteten.
4. Klicka på knappen "skapa".

> [!NOTE]
> Entitetstypen anpassad enhets typ innebär att den här entiteten kan tränas, till skillnad från andra typer av entiteter.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." anger du "Vad heter ditt namn?"
3. I fältet "förväntade entiteter" skriver du "name".
4. Klicka på knappen "skapa".

> [!NOTE]
> Entiteter som identifieras och extraheras från användarens svar kommer att sparas i entiteten "namn" om den här åtgärden väljs. Om inga entiteter identifieras kommer hela svaret att sparas i den här entiteten.

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. I "robotens svar..." fält skriver du "Hej $name!"
3. Klicka på knappen "skapa".

> [!NOTE]
> Entiteten "namn" lades automatiskt till som "obligatoriska entiteter" efter referens i svaret.

Nu har du två åtgärder.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Träna modellen

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Hej".
    - Detta simulerar användarens sida av konversationen.
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "Vad heter ditt namn?"
    - "Hej $name!" Det går inte att välja svaret eftersom det här svaret kräver att entiteten "namn" definieras i modellens minne nu.
5. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Frank".
    - "Frank" är markerat som en entitet baserat på den heuristik som vi konfigurerade tidigare för att spara svaret som entiteten.
6. Klicka på knappen "Poäng åtgärder".
    - Entiteten "namn" definieras nu som "Frank" i modellens minne, så att åtgärden "Hej $name" kan väljas som en åtgärd.
7. Välj svaret, "Hej $name!"
8. Klicka på knappen Spara.

Om du lägger till alternativa indata tränas modellen.

1. I avsnittet "Lägg till alternativa ingångar..." anger du "Jose".
    - Modellen känner inte igen namnet som en entitet, så den markerar hela textblocket som entitetens värde
2. Klicka på frasen "Jag är Jose" och klicka sedan på pappers korgs ikonen.
3. Klicka på "Jose" och klicka sedan på "namn" i listan entiteter.
4. Klicka på Poäng åtgärder.
5. Välj svaret, "Hi Frank!"
6. Klicka på knappen Spara.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Negation bara entiteter](./06-negatable-entities.md)

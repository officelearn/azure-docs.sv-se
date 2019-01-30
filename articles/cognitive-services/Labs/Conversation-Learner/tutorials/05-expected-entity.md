---
title: Hur du använder egenskapen ”förväntades entitet” för konversationen Learner åtgärder – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder egenskapen ”förväntades entitet” för en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 01d991cff9b7f7a66740f86e537833ffe4e862c7
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226276"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Hur du använder ”förväntades entitet”-egenskapen för åtgärder

Den här självstudien visas ”förväntades entitet”-egenskapen för åtgärder.

## <a name="video"></a>Video

[![Förväntade entiteten självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Använda egenskapen ”förväntades entitet” för en åtgärd för att spara användarens svar på den här åtgärden i en entitet.

När du lägger till entiteter ”förväntades entitet”-egenskapen för en åtgärd, kommer systemet att:

1. Starta genom att försöka matcha entiteter med hjälp av machine learning-baserad modell för entiteten extrahering
2. Tilldela $entity baserat på heuristik om inga entiteter finns hela användare-uttryck
3. Anropa `EntityDetectionCallback`, och fortsätt sedan till val av åtgärd.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på ”ny modell”.
2. Skriv ”ExpectedEntities” i fältet ”namn” och tryck på RETUR.
3. Klicka på knappen ”Skapa”.

### <a name="entity-creation"></a>Skapa en entitet

1. Klicka på ”entiteter” och knappen ”ny entitet” på den vänstra panelen.
2. Välj ”anpassade tränas” för ”entitetstypen”.
3. Skriv ”name” för ”entitetsnamnet”.
4. Klicka på knappen ”Skapa”.

> [!NOTE]
> Den anpassade tränade entitetstypen innebär att den här entiteten kan tränas, till skillnad från andra typer av enheter.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”vad är namnet på din”?
3. Skriv i fältet ”förväntades entiteter”, ”name”.
4. Klicka på knappen ”Skapa”.

> [!NOTE]
> Entiteter har identifierats och extraheras från användarens svar sparas till entiteten ”name” om du väljer den här åtgärden. Om inga entiteter identifieras måste sparas hela svaret den här entiteten.

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. I ”Robotens svaret...” fältet, skriver du ”Hi $name”!
3. Klicka på knappen ”Skapa”.

> [!NOTE]
> ”Namn” entitet läggs automatiskt som en ”krävs enheter” med referens i svaret.

Nu har du två åtgärder.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Träna modellen

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Hej”.
    - Det här simulerar användarens sida för konversationen.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”vad är namnet på din”?
    - Den ”Hi $name”! svaret kan inte väljas eftersom det här svaret kräver ”name” entitet definieras i modellens minnet nu.
5. På panelen chatt står det ”Skriv meddelandet...”, ange ”Frank”.
    - ”Frank” är markerad som en entitet som baseras på tumregeln vi ställa in tidigare för att spara svaret som entiteten.
6. Klicka på knappen ”poäng åtgärder”.
    - ”Namn” entitet nu definieras som ”Frank” i minnet för den modellen, så ”Hello $name”-åtgärden är valbara som en åtgärd.
7. Välj svar ”Hi $name”!
8. Klicka på knappen ”Spara”.

Lägga till alternativa indata ytterligare tåg modellen.

1. Skriv i fältet ”Lägg till alternativa indata...”, ”jag Jose”.
    - Modellen känner inte igen namnet som en enhet så att hela texten väljs som dess värde
2. Klicka på ”jag Jose” frasen, och klicka sedan på papperskorgsikonen.
3. Klicka på ”Jose” och sedan på ”name” från entitetslistan.
4. Klicka på poäng åtgärder.
5. Välj svar, ”Frank Hej”!
6. Klicka på knappen ”Spara”.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Negeras entiteter](./06-negatable-entities.md)

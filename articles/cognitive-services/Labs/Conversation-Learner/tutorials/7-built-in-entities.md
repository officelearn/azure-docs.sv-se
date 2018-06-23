---
title: Lägga till förskapad entiteter i en konversation deltagaren program - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du lägger till förskapad entiteter i en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f014464419bfac39a9e57e679fcd28a737e9ebdb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355089"
---
# <a name="how-to-add-pre-built-entities"></a>Hur du lägger till förskapad entiteter
Den här kursen visar hur du lägger till ”inbyggd” entiteter i tillämpningsprogrammet konversation deltagaren.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Förskapad entiteter identifiera vanliga typer av entiteter, till exempel siffror, datum, belopp och andra.  Till skillnad från anpassade enheter de arbetar ”out-of-the-box” och kräver inte någon utbildning.  Till skillnad från anpassade entiteter, kan inte deras beteende ändras.  Som standard förskapad entiteter med flera värden – det vill säga den bot minne samlar alla identifierade förekomster av entiteten.

## <a name="steps"></a>Steg

### <a name="create-the-application"></a>Skapa programmet

1. Klicka på ny App i Webbgränssnittet,
2. Ange BuiltInEntities i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter sedan ny entitet.
2. Klicka på EntityType listrutan och välj datetimev2.
    - Alternativ för Programmable och Negatable har inaktiverats eftersom de inte gäller för att skapa före entiteter.
3. Klicka på Skapa.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och sedan ny åtgärd
2. Skriv i svaret, ' datumet är $luis-datetimev2'.
3. Klicka på Skapa.

![](../media/tutorial7_actions.PNG)

Skapa sedan den andra åtgärden:

1. Klicka på Åtgärder sedan ny åtgärd för att skapa en andra åtgärd.
3. Skriv ”vad är datumet?' svar.
4. Ange 'Thomas datetimev2' i diskvalificera entiteter.
4. Klicka på Skapa

![](../media/tutorial7_actions2.PNG)

Nu har du två åtgärder.

### <a name="train-the-bot"></a>Träna bot

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vad är datumet”?
2. Ange dag'. 
    - Meddelande idag märks och visas i den andra raden eftersom den är en förskapad entitet och kan inte redigeras.
5. Klicka på poäng åtgärder
    - Observera datum nu visas i avsnittet för entiteten minne. 
    - Om du placerar muspekaren över datumet visas ytterligare information som tillhandahålls av THOMAS som kan användas och ytterligare kan ändras i kod. 
6. Välj ' datumet är $luis-datetimev2'.
7. Klicka på klar lärare

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Alternativa indata](./8-alternative-inputs.md)

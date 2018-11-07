---
title: Lägga till färdiga entiteter i en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du lägger till färdiga entiteter i en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2dbbf2a47cdc4240e5b0ba38658a4cb8d5307ff8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51260065"
---
# <a name="how-to-add-pre-built-entities"></a>Hur du lägger till färdiga entiteter
Den här självstudien visar hur du lägger till ”färdiga” entiteter i Konversationsdeltagare-modellen.

## <a name="video"></a>Video

[![Förhandsversion av självstudien 7](https://aka.ms/cl-tutorial-07-preview)](https://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Färdiga entiteter identifiera vanliga typer av enheter, till exempel tal, datum, penningbelopp och andra.  Till skillnad från anpassade entiteter kan de fungera ”out-of the box” och kräver inte någon utbildning.  Till skillnad från anpassade entiteter, kan inte deras beteende ändras.  Färdiga entiteter finns flera värden – det vill säga robotens minne samlas alla identifierade förekomster av entiteten som standard.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på en ny modell
2. Ange BuiltInEntities i namn. Klicka på Skapa.

### <a name="create-an-entity"></a>Skapa en entitet

1. Klicka på entiteter och ny entitet.
2. Klicka på EntityType listrutan och välj datetimev2.
    - Alternativ för programmerbart och Negatable har inaktiverats eftersom de inte gäller för att skapa förväg entiteter.
3. Klicka på Skapa.

![](../media/tutorial7_entities.PNG)

### <a name="create-two-actions"></a>Skapa två åtgärder

1. Klicka på åtgärder och ny åtgärd
2. I svaret, skriver du ”datumet är $luis-datetimev2”.
3. Klicka på Skapa.

![](../media/tutorial7_actions.PNG)

Skapa sedan den andra åtgärden:

1. Klicka på åtgärder och sedan ny åtgärd för att skapa en andra åtgärd.
3. I svaret, skriver du ”vad är datumet”?.
4. Ange ”luis-datetimev2” i diskvalificera entiteter.
4. Klicka på Skapa

![](../media/tutorial7_actions2.PNG)

Nu har du två åtgärder.

### <a name="train-the-bot"></a>Träna roboten

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
2. Skriv ”hello”.
3. Klicka på poäng åtgärder och välj ”vad är datumet”?
2. Ange ”dag”. 
    - Meddelande i dag är märkta och visas i den andra raden, eftersom det är en färdiga entitet och kan inte redigeras.
5. Klicka på poäng åtgärder
    - Observera datum nu visas i avsnittet för entiteten minne. 
    - Om du för muspekaren över datumet, ser du ytterligare data som tillhandahålls av LUIS, vilket är användbart och ytterligare kan ändras i kod. 
6. Välj ”datumet är $luis-datetimev2”.
7. Klickar du på klar undervisning

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Alternativa indata](./8-alternative-inputs.md)

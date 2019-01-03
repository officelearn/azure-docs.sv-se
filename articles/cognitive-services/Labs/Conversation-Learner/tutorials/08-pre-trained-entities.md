---
title: Lägga till Pre-Trained entiteter i en konversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du lägger till Pre-trained entiteter i en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c198dfc19a350188f500af86c531be9a9ac424ce
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796841"
---
# <a name="how-to-add-pre-trained-entities"></a>Hur du lägger till Pre-trained entiteter
Den här självstudien visar hur du lägger till Pre-Trained entiteter i konversationen Learner-modellen.

## <a name="video"></a>Video

[![Förtränade entiteter självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Förtränade entiteter identifiera vanliga typer av entiteter, till exempel tal, datum, penningbelopp och andra.  De fungerar ”out-of the box”, kräver inte någon utbildning och deras beteende kan inte ändras till skillnad från anpassade entiteter.  Som standard Pre-Trained entiteter är flera värden kan sparas alla identifierade förekomster av entiteten.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på ”ny modell”.
2. Skriv ”PretrainedEntities” i fältet ”namn” och tryck på RETUR.
3. Klicka på knappen ”Skapa”.

### <a name="entity-creation"></a>Skapa en entitet

1. Klicka på ”entiteter” och knappen ”ny entitet” på den vänstra panelen.
2. Välj ”Förproduktions-Trained/datetimeV2” för ”entitetstypen”.
3. Kontrollera ”med flera värden” kryssrutan.
    - Flera värden entiteter ackumuleras ett eller flera värden i entiteten.
    - Negeras egenskaper har inaktiverats för Pre-Trained entiteter.
4. Klicka på knappen ”Skapa”.

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. I ”Robotens svaret...” i fältet ”datumet är $builtin-datetimev2”
3. Klicka på knappen ”Skapa”.

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”vad är datumet”?
    - Förtränade entiteter kan inte vara krävs entiteter när de identifieras som standard för alla användare yttranden.
3. I fältet ”diskvalificera berättigar”, skriver du ”builtin-datetimev2”.
4. Klicka på knappen ”Skapa”.

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>Träna modellen

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ange ”hello”.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”vad är datumet”?
5. På panelen chatt, där det står ”Type meddelandet...”, ange ”dag”
    - Idag uttryck identifieras automatiskt av förtränade modeller i LUIS.
    - Hovra över värdena för Pre-Trained entiteter visas ytterligare data som tillhandahålls av LUIS.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Entiteten matchare](./09-entity-resolvers.md)

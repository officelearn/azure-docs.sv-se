---
title: Lägga till Pre-Trained entiteter i en konversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du lägger till Pre-trained entiteter i en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: dade442cb04f79ef75e65d6eb29128b105e72b40
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565418"
---
# <a name="how-to-add-pre-trained-entities"></a>Hur du lägger till Pre-trained entiteter
Den här självstudien visar hur du lägger till Pre-Trained entiteter i konversationen Learner-modellen.

## <a name="video"></a>Video

[![Förtränade entiteter självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudien Bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Förtränade entiteter identifiera vanliga typer av entiteter, till exempel tal, datum, penningbelopp och andra.  De fungerar ”out-of the box”, kräver inte någon utbildning och deras beteende kan inte ändras till skillnad från anpassade entiteter.  Som standard Pre-Trained entiteter är flera värden kan sparas alla identifierade förekomster av entiteten.

## <a name="steps"></a>Steg

Starta på startsidan i Webbgränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **nya modellen**.
2. Ange **PretrainedEntities** för **namn**.
3. Välj **Skapa**.

### <a name="entity-creation"></a>Skapa en entitet

1. Välj **entiteter** i den vänstra panelen, sedan **ny entitet**.
2. Välj **Förproduktions-Trained/datetimeV2** för **entitetstypen**.
3. Kontrollera **med flera värden** för att aktivera entiteten ackumuleras ett eller flera värden. Observera att Pre-Trained entiteter kan inte vara negeras.
4. Välj **Skapa**.

![](../media/T08_entity_create.png)

1. Välj **åtgärder** i den vänstra panelen, sedan **ny åtgärd**.
2. Ange **datumet är $builtin-datetimev2** för **Robotens svar...** .
3. Välj **Skapa**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Välj **åtgärder** i den vänstra panelen, sedan **ny åtgärd**.
2. Ange **datum?** för **Robotens svar...** . Förtränade entiteter kan inte vara **krävs entiteter** när de identifieras som standard för alla yttranden.
3. Ange **builtin datetimev2** för **diskvalificera entiteter**.
4. Välj **Skapa**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Träna modellen

1. Välj **Train-dialogrutor** i den vänstra panelen, sedan **dialogrutan Ny träna**.
2. Ange **hello** för användarens uttryck i den vänstra chat-panelen.
3. Välj **poäng åtgärder**.
4. Välj **datum?** från åtgärdslistan
5. Ange **idag** för användarens uttryck i den vänstra chat-panelen.
    - Den **idag** uttryck identifieras automatiskt av förtränade modeller i LUIS.
    - Hovra över värdena för Pre-Trained entiteter visas ytterligare data som tillhandahålls av LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Entiteten matchare](./09-entity-resolvers.md)

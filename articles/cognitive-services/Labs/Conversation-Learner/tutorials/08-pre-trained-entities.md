---
title: Så här lägger du till förtränade entiteter i en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du lägger till förtränade entiteter i en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: fceca459b80725e1a9c8b7dbc6a4387ea98dbb7b
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704127"
---
# <a name="how-to-add-pre-trained-entities"></a>Så här lägger du till förtränade entiteter
I den här självstudien visas hur du lägger till förtränade entiteter i Conversation Learners modellen.

## <a name="video"></a>Video

[![Förtränade entiteter självstudie för hands version](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information

Förtränade entiteter identifierar vanliga typer av entiteter, till exempel siffror, datum, penning belopp och andra.  De fungerar i "direkt-The-Box", behöver inte någon utbildning och deras beteende kan inte ändras till skillnad från anpassade entiteter.  Förtränade entiteter är som standard flera värden och ackumulerar varje identifierad instans av entiteten.

## <a name="steps"></a>Steg

Starta på Start sidan i webb gränssnittet.

### <a name="create-the-model"></a>Skapa modellen

1. Välj **ny modell**.
2. Ange **PretrainedEntities** som **namn**.
3. Välj **Skapa**.

### <a name="entity-creation"></a>Skapa entitet

1. Välj **entiteter** i den vänstra panelen och sedan **ny entitet**.
2. Välj **Förtränad/datetimeV2** för **entitetstyp**.
3. Kontrol lera **multi-Value** för att enheten ska kunna ackumulera ett eller flera värden. Observera att förtränade entiteter inte kan vara negationer.
4. Välj **Skapa**.

![](../media/T08_entity_create.png)

1. Välj **åtgärder** i den vänstra panelen och sedan **ny åtgärd**.
2. Ange **datumet är $Builtin-datetimev2** för **robotens svar...** .
3. Välj **Skapa**.

![](../media/T08_action_create_1.png)

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Välj **åtgärder** i den vänstra panelen och sedan **ny åtgärd**.
2. Ange **datumet** för **robotens svar..** .. Förtränade entiteter kan inte vara **nödvändiga enheter** eftersom de känns igen som standard för alla yttranden.
3. Ange **Builtin-datetimev2** för diskvalificerande **entiteter**.
4. Välj **Skapa**.

![](../media/T08_action_create_2.png)

### <a name="train-the-model"></a>Träna modellen

1. Välj **träna dialog rutor** i den vänstra panelen och sedan **ny träna-dialog ruta**.
2. Ange **Hej** för användarens uttryck i den vänstra Chat-panelen.
3. Välj **Poäng åtgärder**.
4. Välj **Vad är datumet?** i åtgärds listan
5. Ange **idag** för användarens uttryck i den vänstra panelen i chat.
    - **Idag** uttryck identifieras automatiskt av förtränade modeller i Luis.
    - Hovring över värdena för förtränade entiteter visar ytterligare data från LUIS.

![](../media/T08_training.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Enhets matchare](./09-entity-resolvers.md)

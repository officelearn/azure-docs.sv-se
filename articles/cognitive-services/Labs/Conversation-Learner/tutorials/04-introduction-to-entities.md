---
title: Hur du använder enheter med en modell för konversationen Learner – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder enheter med en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a42a2ec36d9ce4fb9c139dfddcde0fe0c188c888
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55210361"
---
# <a name="introduction-to-entities"></a>Introduktion till entiteter

Den här självstudien innehåller entiteter, diskvalificera entiteter, krävs entiteter och deras användning i Konversationsdeltagare.

## <a name="video"></a>Video

[![Introduktion till entiteter självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Krav

Den här självstudien krävs att Allmänt självstudien Bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Entiteter avbilda typer av information som roboten behöver för att utföra sitt uppdrag genom extrahering från användaren yttranden eller tilldelning av anpassad kod. Entiteter själva kan också begränsa tillgänglighet genom att uttryckligen som klassificeras som ”krävs” eller ”diskvalificera”.

- Nödvändiga entiteter måste finnas i modellens minne för att åtgärden ska vara tillgängliga
- Diskvalificera entiteter måste *inte* i modellens minnet för att åtgärden ska vara tillgängliga

Den här självstudien fokuserar på anpassade entiteter. Före tränas introduceras flera värden, negeras entiteter och programmässig entiteter i andra självstudier.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på ”ny modell”.
2. Skriv ”IntroToEntities” i fältet ”namn” och tryck på RETUR.
3. Klicka på knappen ”Skapa”.

### <a name="entity-creation"></a>Skapa en entitet

1. Klicka på ”entiteter” och knappen ”ny entitet” på den vänstra panelen.
2. Välj ”anpassade tränas” för ”entitetstypen”.
3. Skriv ”stad” för ”entitetsnamnet”.
4. Klicka på knappen ”Skapa”.

> [!NOTE]
> Den anpassade tränade entitetstypen innebär att den här entiteten kan tränas, till skillnad från andra typer av enheter.

### <a name="create-the-actions"></a>Skapa åtgärder

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”Jag vet inte vilken stad som du vill”.
3. I fältet ”diskvalificera entiteter”, skriver du ”stad”.
4. Klicka på knappen ”Skapa”.

> [!NOTE]
> Att lägga till ”stad” enhet ”diskvalificera entiteter” skulle diskvalificera den här åtgärden från Robotens räkningen när ”stad” entitet definieras i Robotens minne.

Nu skapa en andra åtgärd.

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”vädret i $city är förmodligen vackert”.
3. Klicka på knappen ”Skapa”.

> [!NOTE]
> ”Stad” entitet har lagts automatiskt i listan över entiteter som krävs av referens i svaret.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Träna modellen

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ange ”hello”.
    - Det här simulerar användarens sida för konversationen.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”jag vet inte vilken stad som du vill”.
5. Som användare, svarar med ”Seattle”.
6. Klicka på knappen ”poäng åtgärder”.
7. Välj svar ”väder i $city är förmodligen vackert”.
8. Klicka på knappen ”Spara”.

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förväntade entiteten](./05-expected-entity.md)

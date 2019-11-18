---
title: Descriptor-LUIS
titleSuffix: Azure Cognitive Services
description: Använd Språkförståelse (LUIS) att lägga till appfunktioner som kan förbättra identifiering eller förutsägelser av avsikter och entiteter som kategorier och mönster
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: diberry
ms.openlocfilehash: 2b5046bb61dcafbba0b0540935e08777fbd747a5
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2019
ms.locfileid: "74123152"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Använd beskrivningar för att förstärka signalen av ord listan

Du kan lägga till funktioner till din LUIS-app för att förbättra dess noggrannhet. Funktionerna bidrar LUIS genom att tillhandahålla tips att vissa ord och fraser är en del av en app domän ordförråd. 

En [Beskrivning](luis-concept-feature.md) (fras lista) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och som måste behandlas på liknande sätt (t. ex. namn på städer eller produkter). Vad LUIS lär sig om en av dem tillämpas automatiskt på alla andra program. Den här listan är inte samma sak som en [list-entitet](reference-entity-list.md) (exakta text matchningar) med matchade ord.

En beskrivning läggs till i program domänens vokabulär som en andra signal för att LUIS om orden.

Granska [funktions koncept](luis-concept-feature.md) för att förstå när och varför du ska använda en beskrivning. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Lägg till beskrivning

1. Öppna din app genom att klicka på namnet på sidan **Mina appar** och klicka sedan på **skapa**och sedan på **beskrivningar** i appens vänstra panel. 

1. Klicka på **+ Lägg till beskrivning**på sidan **beskrivningar** . 
 
1. Ange ett namn som `Cities` för beskrivningen i dialog rutan **Skapa ny fras List Beskrivning** . I rutan **värde** anger du värdena för beskrivarna, t. ex. `Seattle`. Du kan ange ett värde i taget eller en uppsättning värden, avgränsade med kommatecken och tryck sedan på **RETUR**.

    > [!div class="mx-imgBorder"]
    > ![lägga till beskrivnings orter](./media/luis-add-features/add-phrase-list-cities.png)

    När du har angett tillräckligt många värden för LUIS visas förslag. Du kan **lägga till alla** föreslagna värden eller välja enskilda villkor.

1. Behåll **dessa värden är utbytbara** om de ytterligare beskrivningarna är alternativ som kan användas utbytbara.

1. Välj **Done** (Klar). Den nya beskrivningen läggs till på sidan **beskrivningar** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Du kan ta bort eller inaktivera en beskrivning från kontext verktygsfältet på sidan **beskrivningar** .

## <a name="next-steps"></a>Nästa steg

När du har lagt till, redigerat, tagit bort eller inaktiverat en beskrivning, [tränar och testar du appen](luis-interactive-test.md) igen för att se om prestandan förbättras.

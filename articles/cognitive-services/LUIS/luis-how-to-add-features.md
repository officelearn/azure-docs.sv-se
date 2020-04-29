---
title: Descriptor-LUIS
titleSuffix: Azure Cognitive Services
description: Använd Language Understanding (LUIS) för att lägga till app-funktioner som kan förbättra identifieringen eller förutsägelsen av avsikter och entiteter som kategorier och mönster
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: diberry
ms.openlocfilehash: 7560fdcbfc77ea2655e8af641794478ead4c11c7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "80631451"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Använd beskrivningar för att förstärka signalen av ord listan

Du kan lägga till funktioner i LUIS-appen för att förbättra noggrannheten. Funktioner hjälper LUIS genom att tillhandahålla tips om att vissa ord och fraser är en del av en app Domain-vokabulär.

En [Beskrivning](luis-concept-feature.md) (fras lista) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och som måste behandlas på liknande sätt (t. ex. namn på städer eller produkter). Vad LUIS lär sig automatiskt för de andra också. Den här listan är inte samma sak som en [list-entitet](reference-entity-list.md) (exakta text matchningar) med matchade ord.

En beskrivning läggs till i program domänens vokabulär som en andra signal för att LUIS om orden.

Granska [funktions koncept](luis-concept-feature.md) för att förstå när och varför du ska använda en beskrivning.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Lägg till beskrivning

1. Öppna din app genom att klicka på namnet på sidan **Mina appar** och klicka sedan på **skapa**och sedan på **beskrivningar** i appens vänstra panel.

1. Klicka på **+ Lägg till beskrivning**på sidan **beskrivningar** .

1. Ange ett namn `Cities` för beskrivningen i dialog rutan **Skapa ny fras List Beskrivning** . I rutan **värde** anger du värdena för beskrivarna, till exempel `Seattle`. Du kan ange ett värde i taget eller en uppsättning värden avgränsade med kommatecken och sedan trycka på **RETUR**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till beskrivnings orter](./media/luis-add-features/add-phrase-list-cities.png)

    När du har angett tillräckligt många värden för LUIS visas förslag. Du kan **lägga till alla** föreslagna värden eller välja enskilda villkor.

1. Behåll **dessa värden är utbytbara** om de ytterligare beskrivningarna är alternativ som kan användas utbytbara.

1. Fras listan kan tillämpas på hela appen med den **globala** inställningen eller till en speciell modell (avsikt eller entitet). Om du skapar fras listan, som en _Beskrivning_ från ett avsikt eller en entitet, är växlingen inställd på inte global. I det här fallet är syftet med växlingen att beskrivningen är en funktion enbart till den modellen, vilket innebär att den _inte är global_ till programmet.

1. Välj **Klar**. Den nya beskrivningen läggs till på sidan **beskrivningar** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Du kan ta bort eller inaktivera en beskrivning från kontext verktygsfältet på sidan **beskrivningar** .

## <a name="next-steps"></a>Nästa steg

När du har lagt till, redigerat, tagit bort eller inaktiverat en beskrivning, [tränar och testar du appen](luis-interactive-test.md) igen för att se om prestandan förbättras.

---
title: Beskrivningar - LUIS
titleSuffix: Azure Cognitive Services
description: Använd SPRÅKFÖRSTÅELSE (LUIS) för att lägga till appfunktioner som kan förbättra identifiering eller förutsägelse av avsikter och entiteter som kategorier och mönster
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
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631451"
---
# <a name="use-descriptors-to-boost-signal-of-word-list"></a>Använd deskriptorer för att öka signalen för ordlistan

Du kan lägga till funktioner i LUIS-appen för att förbättra dess noggrannhet. Funktioner hjälper LUIS genom att ge tips om att vissa ord och fraser är en del av en app domän ordförråd.

En [deskriptor](luis-concept-feature.md) (fraslista) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och måste behandlas på samma sätt (till exempel namn på städer eller produkter). Vad LUIS lär sig om en av dem tillämpas automatiskt på de andra också. Den här listan är inte samma sak som en [listentitet](reference-entity-list.md) (exakt text matchar) av matchade ord.

En deskriptor lägger till appdomänens ordförråd som en andra signal till LUIS om dessa ord.

Granska [funktionsbegrepp](luis-concept-feature.md) för att förstå när och varför du ska använda en deskriptor.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="add-descriptor"></a>Lägg till deskriptor

1. Öppna appen genom att klicka på namnet på sidan **Mina appar** och klicka sedan på **Skapa**och klicka sedan på **Beskrivningar** på appens vänstra panel.

1. Klicka på + Lägg **till deskriptor**på sidan **Beskrivning.**

1. I dialogrutan **Skapa ny fraslistasbeskrivning anger** du `Cities` ett namn som för beskrivningen. Skriv värdena för beskrivningarna i rutan **Värde,** till exempel `Seattle`. Du kan skriva ett värde i taget eller en uppsättning värden avgränsade med kommatecken och sedan trycka på **Retur**.

    > [!div class="mx-imgBorder"]
    > ![Lägg till beskrivningsstäder](./media/luis-add-features/add-phrase-list-cities.png)

    När du har angett tillräckligt med värden för LUIS visas förslag. Du kan **+ Lägg till alla** föreslagna värden eller välja enskilda termer.

1. Behåll **Dessa värden är utbytbara** kontrolleras om de tillagda deskriptorvärdena är alternativ som kan användas omväxlande.

1. Fraslistan kan tillämpas på hela appen med den **globala** inställningen eller på en viss modell (avsikt eller entitet). Om du skapar fraslistan, som en _deskriptor_ från en avsikt eller entitet, är växlingsknappen inställd på inte global. I det här fallet är innebörden av växlingsknappen att beskrivningen är en funktion för endast den modellen, därför _inte global_ till programmet.

1. Välj **Done** (Klar). Den nya beskrivningen läggs till på sidan **Beskrivningar.**

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Du kan ta bort eller inaktivera en deskriptor från det kontextuella verktygsfältet på sidan **Beskrivningar.**

## <a name="next-steps"></a>Nästa steg

När du har lagt till, redigerat, tagit bort eller inaktiverat en deskriptor [tränar och testar du appen](luis-interactive-test.md) igen för att se om prestanda förbättras.

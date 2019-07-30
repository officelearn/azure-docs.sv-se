---
title: Fras listor – LUIS
titleSuffix: Azure Cognitive Services
description: Använd Språkförståelse (LUIS) att lägga till appfunktioner som kan förbättra identifiering eller förutsägelser av avsikter och entiteter som kategorier och mönster
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 0ce1fb182320b04aebb45b0f848d8c8f6c965fd9
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638161"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Använd frasen visar att boost signaler med ordlistan

Du kan lägga till funktioner till din LUIS-app för att förbättra dess noggrannhet. Funktionerna bidrar LUIS genom att tillhandahålla tips att vissa ord och fraser är en del av en app domän ordförråd. 

En [frasen lista](luis-concept-feature.md) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och måste behandlas på samma sätt (t.ex, namn på städer eller produkter). Vad LUIS lär sig om en av dem tillämpas automatiskt på alla andra program. Den här listan är inte samma sak som en [list-entitet](reference-entity-list.md) (exakta text matchningar) med matchade ord.

En lista med frasen lägger till vokabulär app-domänen som en signal för andra att LUIS om dessa ord.

## <a name="add-phrase-list"></a>Lägg till frasen lista

LUIS tillåter upp till 10 fras listor per app. 

1. Öppna din app genom att klicka på namnet på **Mina appar** , och klicka sedan på **skapa**, klicka sedan på **fras listor** i vänster panel i din app. 

1. På den **fras listor** klickar du på **Skapa ny frasen lista**. 
 
1. Skriv`Cities` som namn på fras listan i dialog rutan **Lägg till fras lista** . I den **värdet** skriver värdena i listan med fraser. Du kan ange ett värde i taget eller en uppsättning värden, avgränsade med kommatecken och tryck sedan på **RETUR**.

    ![Lägg till frasen lista städer](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS kan föreslå relaterade värden för att lägga till i listan fras. Klicka på **rekommenderar** att hämta en grupp med föreslagna värden från relaterade till added value(s). Du kan klicka på någon av de föreslagna värdena eller klicka på **Lägg till alla** att lägga till dem alla.

    ![Föreslagna värden för fras lista – Lägg till alla](./media/luis-add-features/related-values.png)

1. Klicka på **dessa värden är utbytbara** om listvärden har lagts till frasen finns alternativ som är utbytbara.

    ![Föreslagna värden för fras lista – Välj utbytbar ruta](./media/luis-add-features/interchangeable.png)

1. Klicka på **Klar**. Listan med frasen ”städer” har lagts till i den **fras listor** sidan.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Du kan ta bort eller inaktivera en fras lista från verktygsfältet sammanhangsberoende på den **fras listor** sidan.

## <a name="next-steps"></a>Nästa steg

Efter att lägga till, redigera, ta bort eller inaktivera en fras lista [träna och testa appen](luis-interactive-test.md) igen för att se om prestanda förbättras.

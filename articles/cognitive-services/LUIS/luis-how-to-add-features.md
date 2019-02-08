---
title: Fraslistor
titleSuffix: Language Understanding - Azure Cognitive Services
description: Använd Språkförståelse (LUIS) att lägga till appfunktioner som kan förbättra identifiering eller förutsägelser av avsikter och entiteter som kategorier och mönster
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/16/2019
ms.author: diberry
ms.openlocfilehash: f3376b8077302eb6bd9a96710f92d692b641cac4
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55866788"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Använd frasen visar att boost signaler med ordlistan

Du kan lägga till funktioner till din LUIS-app för att förbättra dess noggrannhet. Funktionerna bidrar LUIS genom att tillhandahålla tips att vissa ord och fraser är en del av en app domän ordförråd. 

En [frasen lista](luis-concept-feature.md) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och måste behandlas på samma sätt (t.ex, namn på städer eller produkter). Vad LUIS lär sig om en av dem tillämpas automatiskt på alla andra program. Den här listan är inte en stängd lista entitet (exakt denna matchar) av ord som matchade.

En lista med frasen lägger till vokabulär app-domänen som en signal för andra att LUIS om dessa ord.

## <a name="add-phrase-list"></a>Lägg till frasen lista

LUIS kan upp till 10 frasen listor per app. 

1. Öppna din app genom att klicka på namnet på **Mina appar** , och klicka sedan på **skapa**, klicka sedan på **fras listor** i vänster panel i din app. 

2. På den **fras listor** klickar du på **Skapa ny frasen lista**. 
 
3. I den **lägga till frasen lista** dialogrutan skriver du ”städer” som namn på listan med fraser. I den **värdet** skriver värdena i listan med fraser. Du kan ange ett värde i taget eller en uppsättning värden, avgränsade med kommatecken och tryck sedan på **RETUR**.

    ![Lägg till frasen lista städer](./media/luis-add-features/add-phrase-list-cities.png)

4. LUIS kan föreslå relaterade värden för att lägga till i listan fras. Klicka på **rekommenderar** att hämta en grupp med föreslagna värden från relaterade till added value(s). Du kan klicka på någon av de föreslagna värdena eller klicka på **Lägg till alla** att lägga till dem alla.

    ![Fras föreslagen listvärden – Lägg till alla](./media/luis-add-features/related-values.png)

5. Klicka på **dessa värden är utbytbara** om listvärden har lagts till frasen finns alternativ som är utbytbara.

    ![Fras föreslagen listvärden – utbytbara kryssrutan ](./media/luis-add-features/interchangeable.png)

6. Klicka på **Spara**. Listan med frasen ”städer” har lagts till i den **fras listor** sidan.

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Du kan ta bort eller inaktivera en fras lista från verktygsfältet sammanhangsberoende på den **fras listor** sidan.

## <a name="next-steps"></a>Nästa steg

Efter att lägga till, redigera, ta bort eller inaktivera en fras lista [träna och testa appen](luis-interactive-test.md) igen för att se om prestanda förbättras.

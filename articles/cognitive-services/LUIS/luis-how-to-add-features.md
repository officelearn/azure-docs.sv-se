---
title: Fras listor – LUIS
titleSuffix: Azure Cognitive Services
description: Använd Language Understanding (LUIS) för att lägga till app-funktioner som kan förbättra identifieringen eller förutsägelsen av avsikter och entiteter som kategorier och mönster
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: diberry
ms.openlocfilehash: 0e3e4226eaaa0505eea96d8b3aca820f2327349e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467610"
---
# <a name="use-phrase-lists-to-boost-signal-of-word-list"></a>Använd fras listor för att förstärka signalen av ord listan

Du kan lägga till funktioner i LUIS-appen för att förbättra noggrannheten. Funktioner hjälper LUIS genom att tillhandahålla tips om att vissa ord och fraser är en del av en app Domain-vokabulär. 

En [fras lista](luis-concept-feature.md) innehåller en grupp med värden (ord eller fraser) som tillhör samma klass och som måste behandlas på samma sätt (t. ex. namn på städer eller produkter). Vad LUIS lär sig automatiskt för de andra också. Den här listan är inte samma sak som en [list-entitet](reference-entity-list.md) (exakta text matchningar) med matchade ord.

En fras lista lägger till i program domänens vokabulär som en andra signal för att LUIS om orden.

Granska [funktions koncept](luis-concept-feature.md) för att förstå när och varför du ska använda en fras lista. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-phrase-list"></a>Lägg till fras lista

LUIS tillåter upp till 10 fras listor per app. 

1. Öppna appen genom att klicka på namnet på sidan **Mina appar** och klicka sedan på **skapa**och sedan på **fras listor** i appens vänstra panel. 

1. På sidan **fras lista** klickar du på **Skapa ny fras lista**. 
 
1. Skriv `Cities` som namn på fras listan i dialog rutan **Lägg till fras lista** . Skriv värdena för fras listan i rutan **värde** . Du kan ange ett värde i taget eller en uppsättning värden avgränsade med kommatecken och sedan trycka på **RETUR**.

    ![Lägg till fras lista städer](./media/luis-add-features/add-phrase-list-cities.png)

1. LUIS kan föreslå relaterade värden som ska läggas till i din fras lista. Klicka på **rekommendera** för att hämta en grupp med föreslagna värden som är semantiskt relaterade till de tillagda värdena. Du kan klicka på något av de föreslagna värdena eller klicka på **Lägg till alla** för att lägga till dem.

    ![Föreslagna värden för fras lista – Lägg till alla](./media/luis-add-features/related-values.png)

1. Klicka på **de här värdena är utbytbara** om värdena för den tillagda fras listan är alternativ som kan användas interoförändrade.

    ![Föreslagna värden för fras lista – Välj utbytbar ruta](./media/luis-add-features/interchangeable.png)

1. Klicka på **Klar**. Fras listan "städer" har lagts till på sidan med **fras listor** .

<a name="edit-phrase-list"></a>
<a name="delete-phrase-list"></a>
<a name="deactivate-phrase-list"></a>

> [!Note]
> Du kan ta bort eller inaktivera en fras lista från kontext verktygsfältet på sidan med **frasen** .

## <a name="next-steps"></a>Nästa steg

När du har lagt till, redigerat, tagit bort eller inaktiverat en fras lista, [tränar du och testar appen](luis-interactive-test.md) igen för att se om prestandan förbättras.

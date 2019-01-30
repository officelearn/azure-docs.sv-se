---
title: Använda alternativa indata med Konversationsdeltagare - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder alternativa indata med Konversationsdeltagare.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c60dc2ca93547b93ce2ee457393570479069c899
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55216276"
---
# <a name="how-to-use-alternative-inputs"></a>Hur du använder alternativa indata

Den här självstudiekursen visar hur du använder fältet alternativt indata för användare yttranden i gränssnittet undervisning.

## <a name="video"></a>Video

[![Alternativa indata självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_AlternativeInputs_Preview)](https://aka.ms/cl_Tutorial_v3_AlternativeInputs)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information
Alternativa indata är alternativ, betyda samma sak användaren yttranden som användaren kan ha dessa vid en viss i en dialogruta för utbildning. Dessa alternativ indata kan du ange mer compactly varianter av yttranden utan att behöva åtgärda varje variation i separata tränings-dialogrutor.

## <a name="steps"></a>Steg

### <a name="create-the-model"></a>Skapa modellen

1. I Webbgränssnittet, klickar du på ”ny modell”.
2. Skriv ”AlternativeInputs” i fältet ”namn” och tryck på RETUR.
3. Klicka på knappen ”Skapa”.

### <a name="entity-creation"></a>Skapa en entitet

1. Klicka på ”entiteter” och knappen ”ny entitet” på den vänstra panelen.
2. Välj ”anpassade tränas” för ”entitetstypen”.
3. Skriv ”stad” för ”entitetsnamnet”.
4. Klicka på knappen ”Skapa”.

Nu ska vi skapa tre åtgärder.

### <a name="create-the-first-action"></a>Skapa den första åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”vilken stad”?
3. I fältet ”förväntade entiteten i användarens svar...”, skriver du ”stad”.
4. I fältet ”diskvalificera berättigar”, skriver du ”stad”.
5. Klicka på knappen ”Skapa”.

### <a name="create-the-second-action"></a>Skapa den andra åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”vädret i $city är förmodligen vackert”.
3. Klicka på knappen ”Skapa”.

### <a name="create-the-third-action"></a>Skapa den tredje åtgärden

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Fältet i ”Robotens svaret...”, skriver du ”prova efterfrågar vädret”.
3. I fältet ”diskvalificera berättigar”, skriver du ”stad”.
4. Klicka på knappen ”Skapa”.

Nu har du tre åtgärder.

### <a name="train-the-model"></a>Träna modellen

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”vad är vädret”?
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”vilken stad”?
5. På panelen chatt står det ”Skriv meddelandet...”, ange ”Denver”
6. Klicka på knappen ”poäng åtgärder”.
7. Välj svar ”vädret i Denver är förmodligen vackert”.
8. Klicka på knappen ”Spara”.

Nu ska vi träna modellen mer genom att skapa en annan train-dialogruta.

### <a name="second-model-train-dialog"></a>Andra modellen träna dialogrutan

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”vad kan du göra”?
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar, ”prova ber om väder”.
5. På panelen chatt står det ”Skriv meddelandet...”, typ i ”vad är vädret i Seattle”?
6. Klicka på ”Seattle” och sedan på ”stad” från entitetslistan.
7. Klicka på knappen ”poäng åtgärder”.
8. Välj svar ”vädret i Seattle är förmodligen vackert”.
9. Klicka på knappen ”Spara”.

### <a name="third-model-train-dialog-using-alternative-input"></a>Tredje modellen träna dialogruta med alternativ som indata

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ”hjälp” ange
3. Klicka på knappen ”poäng åtgärder”.
    - Modellen är osäker till det bästa alternativet så att det skulle välja den högsta: e percentilen som standard.
4. Klicka på ”Avbryt Teaching”-knappen och sedan ”Confirm”-knappen.

![](../media/tutorial8_closescores.png)

Nu ska vi bättre finjustera systemet med alternativa indata. Du kan lägga till alternativa indata medan undervisning eller senare.

5. I den vänstra rutan klickar du på ”Train-dialogrutor” och välj sedan ”vad kan du göra”? i listan över Train-dialogrutor.
6. Klicka på ”vad kan du göra”? uttryck i panelen chatt.
7. I den ”Lägg till alternativa indata...” RETUR fält, typ ”hjälp” och tryck på.
8. Klicka på knappen ”Spara ändringar”.

![](../media/tutorial8_helpalternates.png)

Vi lägger till ett annat alternativ indata för att hantera Houston (USA).

9. Klicka på ”vad är vädret i Seattle”? uttryck i panelen chatt.
10. I fältet ”Lägg till alternativa indata...” typ ”prognos för Houston (USA)” och tryck på RETUR.
    - Fel meddelande viktiga fakta alternativa indata måste ha samma sak och innehålla samma entiteter som den ursprungliga uttryck; inte bara samma värde för entiteter. Förekomst av samma entiteter är obligatoriskt.
11. Klicka på ”Houston (USA)” och välj ”stad” från listan över entiteter.
12. I fältet ”Lägg till alternativa indata...” typ ”prognos för Seattle” och tryck på RETUR.
13. Klicka på ”Seattle” och välj ”stad” från listan över entiteter.
14. Klicka på knappen ”Spara ändringar”.
15. Klicka på knappen ”Spara redigera”.

### <a name="testing-the-model"></a>Testa modellen

1. På den vänstra rutan klickar du på ”Log-dialogrutor” och ”Log dialogrutan Ny”.
2. På panelen chatt står det ”Skriv meddelandet...”, ”Hjälp mig” ange
3. På panelen chatt står det ”Skriv meddelandet...”, ange ”prognosen för Denver”

![](../media/tutorial8_altcities.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Log-dialogrutor](./11-log-dialogs.md)

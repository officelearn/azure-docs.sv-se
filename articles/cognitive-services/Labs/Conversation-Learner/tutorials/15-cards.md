---
title: Hur du använder kort med en Konversationsdeltagare modell, del 1 – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder kort med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a2db74e4c5688c0a2a3975ff828caf20c3cf7904
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60406384"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Hur du använder kort (del 1 av 2)

Den här självstudiekursen visar hur du lägger till och använda ett enkelt kort i din robot.

> [!NOTE]
> Konversationsdeltagare är för närvarande förväntar sig ditt kort definitionsfiler finnas i en katalog med namnet ”kort” som finns i katalogen där roboten har startats.

## <a name="video"></a>Video

[![Kort självstudie förhandsversion](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Kort är UI-element som tillåter användare att välja ett alternativ i konversationen. 

### <a name="open-the-demo"></a>Öppna demon

Klicka på ”Importera Tutorials” och välja modellen med namnet ”självstudien-15-kort” i webbgränssnittet.

### <a name="the-card"></a>Kortet

Kort-definitionen är på följande plats: C:\<installedpath\>\src\cards\prompt.json.

Systemet förväntar sig att hitta kort definitionerna i den här katalogen ”kort”.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Observera brödtext skriver ”tecknens” och ”{{fråga}}”-platshållare i textfältet.
> Det finns två skicka knappar och texten som hämtar skickade för var och en.

### <a name="actions"></a>Åtgärder

Vi har skapat tre åtgärder. Som du ser nedan är den första åtgärden ett kort.

![](../media/tutorial13_actions.PNG)

Nu ska vi se hur typen av kort har skapats:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Kortet innehåller tre olika parametrar - fråga som indata, 1 och 2. Dessa element är mallen referenser i kortet där du anger i frågor och svar. Du kan också referera till och Använd entiteter eller en blandning av text och entiteter.

På ögonikonen visar hur kortet ser ut.

### <a name="practicing-creating-card-actions"></a>Använder skapa kort åtgärder

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Välj ”kort” för ”åtgärdstypen”.
3. Välj ”fråga” i listan ”mall”.
4. Skriv i fältet ”fråga”, ”gå till vänster eller höger”
5. I fältet ”button1”, skriver du ”vänster”
6. I fältet ”button2”, skriver du ”höger”
7. Klicka på ”Avbryt”.

### <a name="train-dialog-using-an-adaptive-card"></a>Träna dialogrutan med hjälp av ett Adaptivt kort

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Hej”
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”, meddelande: fråga: Gå till vänster eller höger ”?
    - På ögonikonen kan användas för att förhandsgranska kortet
5. Klicka på knappen ”Left” renderade prompten på panelen chatt.
6. Klicka på knappen ”poäng åtgärder”.
7. Välj svar ”vänster”
8. Klicka på knappen ”Spara”.
9. Välj svar ”, meddelande: fråga: Gå till vänster eller höger ”?
10. Klicka på knappen ”höger” renderade prompten på panelen chatt.
11. Klicka på knappen ”poäng åtgärder”.
12. Välj svar ”höger”

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hybrid Bots](./16-hybrid-bots.md)

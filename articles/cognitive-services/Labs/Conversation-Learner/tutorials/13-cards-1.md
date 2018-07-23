---
title: Hur du använder kort med en Konversationsdeltagare modell, del 1 – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder kort med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 988a2433f098f41bca4796299825293efd4de44b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171149"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Hur du använder kort (del 1 av 2)

Den här självstudiekursen visar hur du lägger till och använda ett enkelt kort i din robot.

> [!NOTE]
> För närvarande Konversationsdeltagare förväntar sig ditt kort definitionsfiler finnas i en katalog med namnet ”kort” som finns i katalogen där roboten har startats. Vi kommer att göra detta kan konfigureras i framtiden.

## <a name="video"></a>Video

[![Förhandsversion av självstudien 13](http://aka.ms/cl-tutorial-13-preview)](http://aka.ms/blis-tutorial-13)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Kort är UI-element som tillåter användare att välja ett alternativ i konversationen. 

### <a name="open-the-demo"></a>Öppna demon

I listan över webbgränssnittet modellen, klickar du på självstudien-13-kort-1. 

### <a name="the-card"></a>Kortet

Kort-definitionen är på följande plats: C:\<installedpath\>\src\cards\prompt.json.

Systemet förväntar sig att hitta kort definitionerna i den här katalogen för kort.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Lägg märke till brödtexten `TextBlock` och `{{question}}` platshållare i textfältet.
> Det finns två skicka knappar och texten som hämtar skickade för var och en.

### <a name="actions"></a>Åtgärder

Vi har skapat tre åtgärder. Som du ser nedan är den första åtgärden ett kort.

![](../media/tutorial13_actions.PNG)

Nu ska vi se hur typen av kort har skapats:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Fråga indata och knappar 1 och 2. Här är mallen referenser i kortet där du anger i frågor och svar. Du kan också referera till och Använd entiteter eller en blandning av text och entiteter.

På ögonikonen visar hur kortet ser ut.

### <a name="train-dialog"></a>Träna dialogrutan

Låt oss gå igenom en undervisning dialogruta.

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka på ”fråga go vänster eller höger”.
    - Klicka på ”vänster” eller ”höger” motsvarar användare att skriva ”vänster” eller ”höger” respektive. 
4. Klicka på poäng åtgärder.
4. Klicka på Välj vänster. Detta är en icke-wait-åtgärden.
6. Klicka på ”fråga go vänster eller höger”.
4. Klicka på ”höger”.
5. Klicka på poäng åtgärder.
3. Klicka för att välja ”höger”
6. Klicka på ”fråga go vänster eller höger”.
4. Klicka på klar testning.

Du har nu sett hur kort fungerar. De definieras i katalogen kort som json-mallar. Mallarna visar i Användargränssnittet som du kan fylla i med hjälp av en sträng eller en enhet eller en blandning av båda.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kort del 2](./14-cards-2.md)

---
title: Hur du använder kort med ett samtal deltagaren program, del 1 - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig använda kort i en konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e90ccd42b21eea6139c402937be7e20513d73c84
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353982"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Hur du använder kort (del 1 av 2)

Den här kursen visar hur du lägger till och använder en enkel kort i din bot.

Observera att konversationen deltagaren förväntas kort definitionsfilerna ska placeras i en katalog med namnet ”kort” som finns i katalogen där bot har startats.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Kort är UI-element som tillåter användaren att välja ett alternativ i konversationen. 

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på kursen-13-kort-1 i App-lista över webbgränssnittet. 

### <a name="the-card"></a>Kortet

Kort definitionen är på följande plats: C:\<installedpath\>\src\cards\prompt.json.

Systemet förväntar sig att hitta kort definitionerna i den här katalogen för kort.

![](../media/tutorial13_prompt.PNG)

- Observera tecknens och mallen för frågan.
- Det finns två skicka knappar och texten som hämtar in för varje.

### <a name="actions"></a>Åtgärder

Vi har skapat tre åtgärder. Nedan visas är den första åtgärden ett kort.

![](../media/tutorial13_actions.PNG)

Nu ska vi se hur åtgärd korttyp skapades:

![](../media/tutorial13_cardaction.PNG)

Observera fråga indata och knappar 1 och 2. De är mallen referenser i kort där du anger frågan och respektive svar. Du kan också referera till och använda entiteter eller en blandning av text och enheter.

Ikonen Öga visar hur kortet ser ut.

### <a name="train-dialog"></a>Train dialogrutan

Låt oss gå igenom en lärare dialogruta.

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka på 'Fråga gå åt vänster eller höger'.
    - Klicka på 'vänster' eller '' motsvarar användare att skriva till-vänster- eller höger-respektive. 
4. Klicka på poäng åtgärder.
4. Klicka på Välj vänster. Detta är en icke-wait-åtgärd.
6. Klicka på 'Fråga gå åt vänster eller höger'.
4. Klicka på ”höger”.
5. Klicka på poäng åtgärder.
3. Markera ”höger”
6. Klicka på 'Fråga gå åt vänster eller höger'.
4. Klicka på klart testning.

Nu har du sett hur kort fungerar. De definieras i katalogen kort som json-mallarna. Mallarna kommer yta i Användargränssnittet som du kan fylla i med hjälp av en sträng eller en enhet eller en blandning av båda.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kort del 2](./14-cards-2.md)

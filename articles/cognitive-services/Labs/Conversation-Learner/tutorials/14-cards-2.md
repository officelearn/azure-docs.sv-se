---
title: Hur du använder kort med en Konversationsdeltagare modell, del 2 – Microsoft Cognitive Services | Microsoft Docs
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
ms.openlocfilehash: 1c7c88742c69041594006add76f7e3c642c64dec
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170580"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Hur du använder kort (del 1 av 2)
Den här självstudiekursen visar hur du lägger till ett kort ifyllbara din robot. Den visar hur formulärfält flytta till entiteter.

Konversationsdeltagare förväntar sig ditt kort definitionsfiler finnas i en katalog med namnet ”kort”, som finns i katalogen där roboten har startats.

## <a name="video"></a>Video

[![Förhandsversion av självstudien 14](http://aka.ms/cl-tutorial-14-preview)](http://aka.ms/blis-tutorial-14)

## <a name="requirements"></a>Krav
Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Kort är UI-element som tillåter användare att välja ett alternativ i konversationen. 

### <a name="open-the-demo"></a>Öppna demon

I listan över webbgränssnittet modellen, klickar du på självstudien-14-kort-2. 

### <a name="the-card"></a>Kortet

Kort-definitionen är på följande plats: C:\<installedpath\>\src\cards\shippingAddress.json.

Det här kortet samlar in tre fält för leveransadressen: ort, gata och tillstånd.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Åtgärder

Vi har skapat tre åtgärder. Som du ser nedan är den första åtgärden ett kort.

![](../media/tutorial14_actions.PNG)

Nu ska vi se hur typen av kort har skapats:

- Lägg märke till adress-gatuadress, där typen är på Input.text och dess-ID.
- Det finns på samma sätt adress stad och en listruta med ID: T för adress tillstånd.

ID: N är viktiga eftersom när fälten fylls i och Skickat, de är entitetsnamn som tar emot dessa värden i roboten.

## <a name="entities"></a>Entiteter
Vi har definierat tre enheter som matchar kortet som vi såg ovan.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Åtgärder

Vi har definierat två åtgärder.

![](../media/tutorial14_actions.PNG)

- Först är endash adresskortet där åtgärdstypen är kort och mallen är markerad i listrutan som shippingAddress.
- Andra är en enkel åtgärd för att spela upp leveransadressen.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Träna dialogrutan

Låt oss gå igenom en undervisning dialogruta.

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka på ”leveransadress'.
4. Fyll i kortet och skicka.
    - Observera att dessa värden har nu flyttats till entiteten-minne. Ingen parsning krävs eftersom formuläret redan har partitionerats indata.
5. Klicka på poäng åtgärder.
3. Klicka för att markera ”på väg till $Address...”.
4. Klicka på klar testning.

![](../media/tutorial14_train_dialog.PNG)

Du har nu sett hur att hämta värden från kortet som har ifyllbara fält och listrutor, och att avbilda och samla dem i bot entiteter.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Branchning och ångra](./15-branching-and-undo.md)

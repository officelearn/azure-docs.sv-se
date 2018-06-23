---
title: Hur du använder kort med ett samtal deltagaren program, del 2 - kognitiva Microsoft-tjänster | Microsoft Docs
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
ms.openlocfilehash: 254f0953fd3e281a35857e69d9795e3decebf45d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353964"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Hur du använder kort (del 1 av 2)
Den här kursen visar hur du lägger till ett kort ifyllbara din bot. Hur formulärfält flytta till entiteter visas.

Konversationen deltagaren förväntas kort definitionsfilerna ska placeras i en katalog med namnet ”kort”, som finns i katalogen där bot har startats.

## <a name="requirements"></a>Krav
Den här kursen kräver att den allmänna självstudiekursen bot körs

    npm run tutorial-general

## <a name="details"></a>Information

Kort är UI-element som tillåter användaren att välja ett alternativ i konversationen. 

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på kursen-14-kort-2 i App-lista över webbgränssnittet. 

### <a name="the-card"></a>Kortet

Kort definitionen är på följande plats: C:\<installedpath\>\src\cards\shippingAddress.json.

Det här kortet samlar in tre fält för leveransadressen: ort, gata och tillstånd.

![](../media/tutorial14_card.PNG)

### <a name="actions"></a>Åtgärder

Vi har skapat tre åtgärder. Nedan visas är den första åtgärden ett kort.

![](../media/tutorial14_actions.PNG)

Nu ska vi se hur åtgärd korttyp skapades:

- Meddelande-gata, där typen är Input.text och dess-ID.
- Det finns på samma sätt-ort och en listruta med ID: T för adress tillstånd.

ID: N är viktiga eftersom när fälten ifyllda och har skickats, de entitetsnamn som tar emot dessa värden i bot.

## <a name="entities"></a>Entiteter
Vi har definierat tre enheter matchar kortet som vi såg ovan.

![](../media/tutorial14_entities.PNG)

## <a name="actions"></a>Åtgärder

Vi har definierat två åtgärder.

![](../media/tutorial14_actions.PNG)

- Först är leverans adress-kort där åtgärdstypen är kort och mallen är markerad i listrutan som shippingAddress.
- Andra är en enkel åtgärd att läsa tillbaka leveransadressen.

![](../media/tutorial14_sa_card.PNG)

### <a name="train-dialog"></a>Train dialogrutan

Låt oss gå igenom en lärare dialogruta.

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange ”Hej”.
2. Klicka på poäng åtgärd.
3. Klicka på 'Leveransadress'.
4. Fyll i kortet och skicka.
    - Observera att dessa värden har nu flyttats till entiteten minne. Ingen parsning krävs eftersom formuläret redan har partitionerats indata.
5. Klicka på poäng åtgärder.
3. Klicka om du vill markera som levereras till $Address... ”.
4. Klicka på klart testning.

![](../media/tutorial14_train_dialog.PNG)

Nu har du sett att hämta värden från kortet som har ifyllbara fält och nedrullningsbara listorna, och för att avbilda och samla dem i bot entiteter.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förgrening och ångra](./15-branching-and-undo.md)

---
title: Använda kort med en Conversation Learner modell, del 1 – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder kort med en Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 426d7c8de29abeb88833e94962a7291a641702ac
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703754"
---
# <a name="how-to-use-cards-part-1-of-2"></a>Använda kort (del 1 av 2)

I den här självstudien visas hur du lägger till och använder ett enkelt kort i din robot.

> [!NOTE]
> Conversation Learner för närvarande förväntar sig att dina kort definitions filer ska finnas i en katalog med namnet "kort" som finns i katalogen där bot har startats.

## <a name="video"></a>Video

[![Förhands granskning av kort självstudie](https://aka.ms/cl_Tutorial_v3_Cards_Preview)](https://aka.ms/cl_Tutorial_v3_Cards)

## <a name="requirements"></a>Krav
Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

## <a name="details"></a>Information

Kort är GRÄNSSNITTs element som gör att användaren kan välja ett alternativ i konversationen. 

### <a name="open-the-demo"></a>Öppna demon

I webb gränssnittet klickar du på "Importera självstudier" och väljer modellen "självstudie-15-kort".

### <a name="the-card"></a>Kortet

Kort definitionen finns på följande plats: C:\<installedpath\>\src\cards\prompt.JSON.

Systemet förväntar sig att hitta dina kort definitioner i den här "kort"-katalogen.

![](../media/tutorial13_prompt.PNG)

> [!NOTE]
> Observera text typen "TextBlock" och plats hållaren "{{Body}}" i textfältet.
> Det finns två skicka-knappar och den text som skickas för var och en.

### <a name="actions"></a>Åtgärder

Vi har skapat tre åtgärder. Som du ser nedan är den första åtgärden ett kort.

![](../media/tutorial13_actions.PNG)

Nu ska vi se hur kort åtgärds typen skapades:

![](../media/tutorial13_cardaction.PNG)

> [!NOTE]
> Kortet innehåller tre olika parametrar – frågans Indatatyp, knapp 1 och knapp 2. De här elementen är mall referenser på kortet där du anger frågan och respektive svar. Du kan också referera till och använda entiteter eller en blandning av text och entiteter.

Ögon ikonen visar hur kortet ser ut.

### <a name="practicing-creating-card-actions"></a>Öva på att skapa kort åtgärder

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. Välj "kort" som "åtgärds typ".
3. Välj "prompt" från listan "mall".
4. Skriv "gå till vänster eller höger" i fältet "fråga"
5. Skriv "left" i fältet "Button1"
6. Skriv "höger" i fältet "button2"
7. Klicka på Avbryt.

### <a name="train-dialog-using-an-adaptive-card"></a>Träna dialog med ett anpassningsbart kort

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Hej"
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret, "fråga: fråga: Går du till vänster eller höger? "
    - Ögon ikonen kan användas för att förhandsgranska kortet
5. I Chat-panelen klickar du på knappen "vänster" i den återgivna prompten.
6. Klicka på knappen "Poäng åtgärder".
7. Välj svaret, "left"
8. Klicka på knappen Spara.
9. Välj svaret, "fråga: fråga: Går du till vänster eller höger? "
10. I Chat-panelen klickar du på knappen "höger" i den återgivna prompten.
11. Klicka på knappen "Poäng åtgärder".
12. Välj svaret, "höger"

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hybrid robotar](./16-hybrid-bots.md)

---
title: Hur du använder session återanrop med en modell för Konversationsdeltagare – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder session återanrop med en Konversationsdeltagare-modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 5458d83fcc9fad50a2a52273ea6b5573ed90a97e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796838"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Hur du använder session återanrop med en modell för Konversationsdeltagare

Den här självstudien introducerar sessioner, hur de ska hanteras och konversationen elevens onSessionStart och onSessionEnd återanrop.

## <a name="video"></a>Video

[![Sessionen återanrop självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Krav
Den här självstudien krävs att ”tutorialSessionCallbacks” bot körs.

    npm run tutorial-session-callbacks

## <a name="details"></a>Information
Den här självstudien Beskriver konceptet med en session, hur sessioner hanteras som standard och hur du kan åsidosätta detta beteende.

I Konversationsdeltagare representerar en session, utan avbrott interaktiva exchange med roboten. Sessioner kan ha flera aktiverar, men programmässigt avslutades på grund av inaktivitet om det är större än trettio minuter har uppnåtts.  Finns på hjälpsidan på ”gränser” information om hur du ändrar den här standard sessionens tidsgräns längd.

Den här lång tids inaktivitet genereras robotar kan skapa en ny session och återställa det återkommande neurala nätverket till det ursprungliga tillståndet. Som standard rensas alla entitetsvärden. Det här standardbeteendet för att rensa entitetsvärden kan ändras enligt nedan.

### <a name="load-the-demo-model"></a>Läsa in modellen Demo

Klicka på ”Importera Tutorials” i webbläsaren, och välj modellen med namnet ”självstudien-13-SessionCallbacks”.

### <a name="code-for-the-callbacks"></a>Kod för återanropen

Exempelkoden för den här modellen två återanrop finns i: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: den här metoden anger BotName entiteten.
- OnSessionEndCallback: du kan ange vad du vill behålla. Detta tar bort alla enheter utom användarnamn och telefonnummer för användaren.

Varje återanrop är valfritt.

### <a name="actions"></a>Åtgärder

Fyra åtgärder har definierats i modellen. Befintliga åtgärder som visas i rutnätsvyn för ”åtgärder”

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Skapa en slutpunkt Session åtgärd (för motringning anrop)

1. Klicka på ”åtgärder” och knappen ”ny åtgärd” på den vänstra panelen.
2. Välj ”ENDSESSION” för ”entitetstypen”.
3. I fältet ”Data...”, skriver du ”klar”
4. Klicka på knappen ”Skapa”.

### <a name="edit-an-existing-action"></a>Redigera en befintlig åtgärd

1. Välj den ”så $UserName, du är i $UserLocation” åtgärd från rutnätsvyn.
2. Avmarkera kryssrutan ”Vänta för svar”.
3. Klicka på knappen ”Spara”.

### <a name="chaining-actions"></a>Länkning åtgärder

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Hej”.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar, ”Hej, jag heter Botty. Vad heter du ”?
5. På panelen chatt står det ”Skriv meddelandet...”, ange ”Dell”
6. Välj svar, ”Hej Dell. Vad är ditt telefonnummer ”?
7. På panelen chatt står det ”Skriv meddelandet...”, ange ”555-555-5555”
8. Klicka på knappen ”poäng åtgärder”.
9. Välj svar ”kan du ge Botty din plats, Dell”?
10. På panelen chatt står det ”Skriv meddelandet...”, ange ”Seattle”
11. Klicka på knappen ”poäng åtgärder”.
12. Välj svar, ”så, Dell, du är i Seattle”
13. Välj svar ”klar”
14. Klicka på knappen ”Spara”.

### <a name="testing-the-model"></a>Testa modellen

1. Klicka på ”Log-dialogrutor” och knappen ”Ny Log dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, typ i ”Hej”
3. På panelen chatt står det ”Skriv meddelandet...”, ange ”Dell”
4. På panelen chatt står det ”Skriv meddelandet...”, ange ”555-555-5555”
5. På panelen chatt står det ”Skriv meddelandet...”, ange ”Seattle”
    - Nu ska alla entitetsvärden exklusive plats sparas.
6. På panelen chatt står det ”Skriv meddelandet...”, ange ”hello”
7. På panelen chatt står det ”Skriv meddelandet...”, ange ”Detroit”
8. Klicka på knappen ”tidsgräns för Session”.
    - Den här knappen utövar robotens svar för att långa perioder av inaktivitet
9. Klicka på ”OK”.
10. Klicka på knappen ”klar testning”.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API-anrop](./14-api-calls.md)

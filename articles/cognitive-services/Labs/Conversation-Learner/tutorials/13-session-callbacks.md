---
title: Använda återanrop från sessioner med en Conversation Learner modell – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder återanrop från sessioner med en Conversation Learner-modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 656d028082d56b8f094e83363e5189b163581c53
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703954"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Använda återanrop från sessioner med en Conversation Learner modell

I den här självstudien introduceras sessioner, hur de hanteras och Conversation Learner onSessionStart-och onSessionEnd-återanrop.

## <a name="video"></a>Video

[![Förhands granskning av självstudie för sessioner](https://aka.ms/cl_Tutorial_v3_SessionCallbacks_Preview)](https://aka.ms/cl_Tutorial_v3_SessionCallbacks)

## <a name="requirements"></a>Krav
I den här självstudien krävs att roboten "tutorialSessionCallbacks" körs.

    npm run tutorial-session-callbacks

## <a name="details"></a>Information
I den här självstudien beskrivs konceptet med en session, hur sessioner hanteras som standard och hur du kan åsidosätta beteendet.

I Conversation Learner en session representerar en, oavbruten interaktiv Exchange med bot. Sessioner kan ha flera varv, men program mässigt avslutas på grund av inaktivitet om det är mer än 30 minuter.  Information om hur du ändrar timeout-tiden för Standardsessionen finns på hjälp sidan begränsningar.

Den här långa tids perioden kommer att orsaka att bot skapar en ny session och återställer det omaktuella neurala-nätverket till sitt ursprungliga tillstånd. Som standard rensas alla enhets värden. Det här standard beteendet för att rensa enhets värden kan ändras på det sätt som visas nedan.

### <a name="load-the-demo-model"></a>Läs in demo modellen

I webb gränssnittet klickar du på "Importera självstudier" och väljer modellen med namnet "självstudie-13-SessionCallbacks".

### <a name="code-for-the-callbacks"></a>Kod för återanrop

Exempel kod för den här modellens två återanrop hittar du i: `c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts`.

![](../media/tutorial11_code.PNG)

- OnSessionStartCallback: den här metoden anger entiteten BotName.
- OnSessionEndCallback: du kan ange vad du vill behålla. Detta raderar alla entiteter förutom användar namn och användarens telefonnummer.

Varje motringning är valfritt.

### <a name="actions"></a>Åtgärder

Fyra åtgärder definieras i modellen. De befintliga åtgärderna visas i diagramvyn för "åtgärder"

![](../media/tutorial11_actions.PNG)

### <a name="creating-an-end-session-action-for-callback-invocation"></a>Skapa en åtgärd för att avsluta sessionen (för återanrops anrop)

1. Klicka på "åtgärder" i den vänstra panelen, sedan på knappen "ny åtgärd".
2. Välj "ENDSESSION" som enhets typ.
3. I "data..." anger du "utfört"
4. Klicka på knappen "skapa".

### <a name="edit-an-existing-action"></a>Redigera en befintlig åtgärd

1. Markera kryss rutan "So, $UserName, du befinner dig i $UserLocation" i diagramvyn.
2. Avmarkera kryss rutan "vänta på svar".
3. Klicka på knappen Spara.

### <a name="chaining-actions"></a>Kedje åtgärder

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Hej".
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret "Hej, jag botty. Vad är ditt namn? "
5. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Lars"
6. Välj svaret "Hi-Lars. Vad är ditt telefonnummer? "
7. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "555-555-5555"
8. Klicka på knappen "Poäng åtgärder".
9. Välj svaret, "kan du se botty din plats, Lars?"
10. I panelen chat, där det står "Skriv ditt meddelande...", skriver du i "Seattle"
11. Klicka på knappen "Poäng åtgärder".
12. Välj svaret "So, Lars, du är i Seattle"
13. Välj svaret, "färdig"
14. Klicka på knappen Spara.

### <a name="testing-the-model"></a>Testa modellen

1. Klicka på "Logga dialog rutor" i den vänstra panelen, sedan på knappen "ny logg dialog ruta".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Hej"
3. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Lars"
4. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "555-555-5555"
5. I panelen chat, där det står "Skriv ditt meddelande...", skriver du i "Seattle"
    - I det här läget bör alla enhets värden exklusive platsen ha bevarats.
6. I panelen chat, där det står "Skriv ditt meddelande...", skriver du i "Hej"
7. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Detroit"
8. Klicka på knappen tids gräns för session.
    - Genom att klicka på den här knappen informerar du robotens svar på långa perioder av inaktivitet
9. Klicka på knappen OK.
10. Klicka på knappen "Slutför testning".

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API-anrop](./14-api-calls.md)

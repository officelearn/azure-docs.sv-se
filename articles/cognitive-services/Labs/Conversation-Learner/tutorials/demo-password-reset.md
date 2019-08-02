---
title: Demo Conversation Learners modell, lösen ords återställning-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demonstrations Conversation Learner modell.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 51eab34f32a20a86445da0ac44d94a31d6694b40
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703395"
---
# <a name="demo-password-reset"></a>Demo: Återställ lösenord
I den här självstudien demonstreras en enkel teknisk support-robot som kan hjälpa dig med lösen ords återställning som drivs av Conversation Learner. Robotens modell kan lära sig icke-triviala dialog flöden och multi-turn sekvenser, inklusive en out-of-Domain-klasser. Uppgiften kan utföras utan kod eller entiteter.

## <a name="video"></a>Video

[![Förhands granskning av demo lösen ord](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Krav
Den här självstudien kräver att bot för lösen ords återställning körs

    npm run demo-password

### <a name="open-the-demo"></a>Öppna demon

I listan modell i webb gränssnittet klickar du på självstudier lösen ords återställning. 

### <a name="actions"></a>Åtgärder

Modellen innehåller en uppsättning åtgärder som är utformade för att hjälpa användarna att lösa vanliga lösen ords problem.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Tränings dialog rutor

Modellen innehåller också flera träna-dialog rutor, inklusive vissa som demonstrerar träning av domän klass. Till exempel användare som kan begäras som vägbeskrivning. Exempel roboten har tränats av ett fåtal i demonstrations syfte och svarar bara genom att ge det "inte hjälp med det". Listan över befintliga träna-dialog rutor finns under "träna dialoger" i den vänstra panelen.

![](../media/tutorial_pw_reset_entities.PNG)

1. På den vänstra panelen klickar du på "träna dialoger", sedan på knappen "ny träna dialog".
2. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "Jag har förlorat mitt lösen ord".
3. Klicka på knappen "Poäng åtgärder".
4. Välj svaret "är det för ditt lokala konto eller din Microsoft-konto?"
5. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "lokalt konto"
6. Klicka på knappen "Poäng åtgärder".
7. Välj svaret "vilken version av Windows har du?"
8. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Windows XP"
9. Klicka på knappen "Poäng åtgärder".
10. Klicka på knappen "+ åtgärd".
11. I "robotens svar..." i fältet skriver du "lösning: Så här återställer du lösen ordet i Windows XP. "
12. Klicka på knappen "skapa".

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Tränings dialog rutor för scenarier med direkt domän

1. Klicka på "träna dialoger" i den vänstra panelen, sedan på den befintliga träna-dialog rutan i "leksaks butiker".
2. I panelen Chat klickar du på uttryck "leksaks lager".
3. I avsnittet "Lägg till alternativa ingångar..." anger du "Webbs ökning" och trycker på RETUR.
4. I avsnittet "Lägg till alternativa ingångar..." skriver du "flyg bokning" och trycker på RETUR.
5. Klicka på knappen "Spara ändringar".
6. Klicka på knappen "Spara redigering".
7. Klicka på "Logga dialog rutor" i den vänstra panelen, sedan på knappen "ny logg dialog ruta".
8. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "Jag kan inte hitta mitt lösen ord"
9. I panelen chat, där det står "Skriv ditt meddelande...", skriver du in "Microsoft-konto"
10. I panelen chat, där det står "Skriv ditt meddelande...", skriver du "tacka"
11. Klicka på knappen "Slutför testning".
12. Klicka på logg dialog rutan jag kan inte hitta mitt lösen ord i diagramvyn.
13. Klicka på den felaktigt renderade "lösningen på panelen Chat: Så här återställer du ett Microsoft-kontos lösen ord "Response.
14. Klicka på knappen "+ åtgärd".
15. I "robotens svar..." fält skriver du "du är välkommen"
16. Klicka på knappen "skapa".
17. Klicka på knappen "Spara som träna".

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo – pizza order](./demo-pizza-order.md)

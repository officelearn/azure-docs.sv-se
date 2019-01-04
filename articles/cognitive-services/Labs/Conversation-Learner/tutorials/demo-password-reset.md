---
title: Demo Konversationsdeltagare modell för lösenordsåterställning – Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demo Konversationsdeltagare modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 409647da146a2844384204cb03de5028d45e5763
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53792398"
---
# <a name="demo-password-reset"></a>Demo: Lösenordsåterställning
Den här självstudien visar en enkel teknisk support-robot som kan hjälpa dig med lösenordsåterställning som drivs av Konversationsdeltagare. Robotens modellen kan lära dig flöden för icke-trivialt dialogrutan och flera aktiverar sekvenser, inklusive en out-för-domain-klasser. Uppgiften kan utföras utan kod eller entiteter.

## <a name="video"></a>Video

[![Demo lösenord förhandsversion](https://aka.ms/cl_Tutorial_v3_DemoPassword_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPassword)

## <a name="requirements"></a>Krav
Den här självstudien krävs att lösenordsåterställning bot körs

    npm run demo-password

### <a name="open-the-demo"></a>Öppna demon

Klicka på självstudien Demo för lösenordsåterställning i listan modell av webbgränssnittet. 

### <a name="actions"></a>Åtgärder

Modellen innehåller en uppsättning åtgärder som har utformats för att hjälpa användarna att lösa vanliga lösenordsproblem med.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning

Modellen innehåller också flera Train-dialogrutor, inklusive några som visar utanför domänen klass utbildning. Till exempel som användare kan begära vägbeskrivning. Exempel-roboten har tränats på några exempel och bara svarar som om det ”det går inte att hjälpa dig med som”. Listan över befintliga Train-dialogrutor finns under ”Train-dialogrutor” i den vänstra panelen.

![](../media/tutorial_pw_reset_entities.PNG)

1. Klicka på ”Train-dialogrutor” och knappen ”Ny träna dialogruta” på den vänstra panelen.
2. På panelen chatt står det ”Skriv meddelandet...”, ange ”i förlorat mitt lösenord”.
3. Klicka på knappen ”poäng åtgärder”.
4. Välj svar ”är som för din lokalt konto eller ett Microsoft-konto”?
5. På panelen chatt står det ”Skriv meddelandet...”, skriver i ”lokala konto”.
6. Klicka på knappen ”poäng åtgärder”.
7. Välj svar ”vilken version av Windows har du”?
8. På panelen chatt står det ”Skriv meddelandet...”, typ i ”windows xp”
9. Klicka på knappen ”poäng åtgärder”.
10. Klicka på ”+ åtgärd” knappen.
11. I ”Robotens svaret...” i fältet ”lösningen: Hur du återställer lösenord på Windows XP... ”
12. Klicka på knappen ”Skapa”.

### <a name="training-dialogs-for-out-of-domain-scenarios"></a>Utbildning dialogrutor för Out-för-Domain-scenarier

1. På den vänstra rutan klickar du på ”Train-dialogrutor” och sedan den befintliga ”Leksakståg butiker” träna dialogrutan.
2. Klicka på ”Sonens butiker”-uttryck i panelen chatt.
3. I fältet ”Lägg till alternativa indata...” RETUR typ ”webbsökning” och tryck på.
4. I fältet ”Lägg till alternativa indata...” RETUR typ ”flygning bokning” och tryck på.
5. Klicka på knappen ”Spara ändringar”.
6. Klicka på knappen ”Spara redigera”.
7. Klicka på ”Log-dialogrutor” och knappen ”Ny Log dialogruta” på den vänstra panelen.
8. På panelen chatt står det ”Skriv meddelandet...”, ange ”i det går inte att hitta mitt lösenord”
9. På panelen chatt står det ”Skriv meddelandet...”, ange ”Microsoft-konto”
10. På panelen chatt står det ”Skriv meddelandet...”, ange ”tack”
11. Klicka på knappen ”klar testning”.
12. Klicka på ”i det går inte att hitta mitt lösenord” log dialogrutan från rutnätsvyn.
13. I panelen chatt klickar du på felaktigt återgivna ”lösningen: Hur du återställer lösenordet för ett Microsoft-konto ”-svar.
14. Klicka på ”+ åtgärd” knappen.
15. Fältet i ”Robotens svaret...”, skriver du ”du är Välkommen”
16. Klicka på knappen ”Skapa”.
17. Klicka på knappen ”Spara som träna dialogrutan”.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo – pizza ordning](./demo-pizza-order.md)

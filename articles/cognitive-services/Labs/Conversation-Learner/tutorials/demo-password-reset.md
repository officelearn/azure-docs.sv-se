---
title: Demonstrera konversation deltagaren program, lösenordsåterställning - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en demonstration konversation deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 24d61787a79ee1a1a9737c417aa966cc8fd75930
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353994"
---
# <a name="demo-password-reset"></a>Demo: Återställning av lösenord
Den här demon illustrerar en enkel teknisk support bot som kan hjälpa dig med återställning av lösenord. 

Den visar hur konversation deltagaren kan lära dig icke-trivial dialogrutan flöden, flera Stäng sekvenser, inklusive en out av domän-klass. Den här demonstrationen använder inte någon kod eller enheter.

## <a name="requirements"></a>Krav
Den här kursen kräver att lösenord Återställ bot körs

    npm run demo-password

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på kursen Demo för återställning av lösenord i App-lista över webbgränssnittet. 

### <a name="actions"></a>Åtgärder

Vi har skapat uppsättning åtgärder, där användaren är behöver hjälp med sitt lösenord inklusive lösningar.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning

Det finns ett antal utbildning dialogrutor. Det finns också demonstrationer av en out-of domän klassen--exempelvis användarförfrågningar som ”kör instruktionerna' ligger utanför domänen. bot har tilldelats exempel på några utanför domänen begäranden och svara med 'Jag kan hjälpa dig med som ”.

![](../media/tutorial_pw_reset_entities.PNG)

T.ex, prova med en lärare session.

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange 'Gick förlorad mitt lösenord'.
2. Klicka på poäng åtgärd.
3. Markera 'Är att för lokalt konto eller Microsoft-konto ”?
4. Ange 'Lokala account'.
5. Klicka på poäng åtgärder.
3. Markera 'vilken version av Windows har du ”?
4. Ange ' Windows 8'.
5. Klicka på poäng åtgärder.
6. Välj ' lösning: hur du återställer lösenordet för Windows 8 ”.
4. Klicka på klar lärare.

Nu ska vi prova hur bot kan lära dig en out av domän-klass.

1. Klicka på tåget dialogrutor, sedan nya Train dialogrutan.
1. Ange 'webbsökning'.
    - Detta är ett exempel på out av domän-klassen. 
2. Klicka på poäng åtgärd.
3. Markera 'Tyvärr jag inte kan hjälpa med som ”.
    - Lägg märke till resultatet för det här alternativet för närvarande är låg. Men efter lite mer lärare poängsättningen får högre.
4. Klicka på klar lärare.

Nu har du sett hur du skapar en grundläggande teknisk support demo och hur det kan lära dig att tillhandahålla lösningar och hanterar även utanför exempelfrågor.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo - pizza ordning](./demo-pizza-order.md)

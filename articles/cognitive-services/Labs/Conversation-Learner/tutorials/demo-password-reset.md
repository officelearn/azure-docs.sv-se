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
ms.openlocfilehash: f633dd375d690a1c3e66a2a6e02ae69665dbe960
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170641"
---
# <a name="demo-password-reset"></a>Demo: Återställning av lösenord
Den här demonstrationen visar en enkel teknisk support-robot som kan hjälpa dig med antalet återställningar av lösenord. 

Den visar hur Konversationsdeltagare kan lära dig flöden för icke-trivialt dialogrutan, flera aktivera sekvenser, inklusive en out-för-domain-klass. Den här demonstrationen använder inte någon kod eller entiteter.

## <a name="video"></a>Video

[![Demo lösenord förhandsversion](http://aka.ms/cl-demo-password-preview)](http://aka.ms/blis-demo-password)

## <a name="requirements"></a>Krav
Den här självstudien krävs att lösenordsåterställning bot körs

    npm run demo-password

### <a name="open-the-demo"></a>Öppna demon

Klicka på självstudien Demo för lösenordsåterställning i listan modell av webbgränssnittet. 

### <a name="actions"></a>Åtgärder

Vi har skapat uppsättning åtgärder där användaren är behöver hjälp med sina lösenord, inklusive lösningar.

![](../media/tutorial_pw_reset_actions.PNG)

### <a name="training-dialogs"></a>Dialogrutor för utbildning

Det finns ett antal utbildning dialogrutor. Det finns även demonstrationer av en out of domän klass – till exempel användarförfrågningar som ”vägbeskrivning' ligger utanför domänen. Roboten har tilldelats exempel på några utanför domänen begäranden och svara med ”jag kan inte hjälpa dig med som”.

![](../media/tutorial_pw_reset_entities.PNG)

Till exempel nu ska vi prova en undervisning-session.

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”försvann mitt lösenord”.
2. Klicka på poäng åtgärd.
3. Klicka för att välja ”är att för din lokalt konto eller ett Microsoft-konto”?
4. Ange ”lokalt konto”.
5. Klicka på poäng åtgärder.
3. Klicka för att välja ”vilken version av Windows har du”?
4. Ange ”Windows 8'.
5. Klicka på poäng åtgärder.
6. Välj ”lösningen: hur du återställer lösenord på Windows 8”.
4. Klicka på klar undervisning.

Nu ska vi prova hur roboten kan lära dig en out-för-domain-klass.

1. Klicka på träna dialogrutor, därefter nytt träna dialogrutan.
1. Ange ”webbsökning'.
    - Det här är ett exempel på domännivå för out-klass. 
2. Klicka på poäng åtgärd.
3. Klicka för att välja ”tyvärr jag inte kan hjälpa med som”.
    - Observera att resultatet för det här alternativet för närvarande är låg. Men efter lite mer teaching poängen får högre.
4. Klicka på klar undervisning.

Du har nu sett hur du skapar en grundläggande teknisk support-demonstration och hur det kan lära dig att tillhandahålla lösningar och hanterar även utanför exempelfrågor.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Demo – pizza ordning](./demo-pizza-order.md)

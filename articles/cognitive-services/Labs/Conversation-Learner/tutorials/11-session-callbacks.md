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
ms.openlocfilehash: 0f51b232470e4e4da3f25d40d025dd3b09dd1204
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171923"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Hur du använder session återanrop med en modell för Konversationsdeltagare

Den här självstudien visar onSessionStart och onSessionEnd återanrop.

## <a name="video"></a>Video

[![Förhandsversion av självstudien 11](http://aka.ms/cl-tutorial-11-preview)](http://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>Krav
Den här självstudien kräver att den `tutorialSessionCallbacks` bot körs.

    npm run tutorial-session-callbacks

## <a name="details"></a>Information
Den här självstudien Beskriver konceptet med en session, hur sessioner hanteras som standard och hur du kan åsidosätta detta beteende.

En session är en konversation med roboten. Det kan ha flera aktiverar, men det finns inga långa avbrott i konversationen (till exempel 30 minuter).  Finns på hjälpsidan på ”gränser” för standard sessionens tidsgräns längd.

Om det blir långa avbrott, gå roboten till dess nästa session.  Startar en ny session placerar återkommande neuralt nätverk i det ursprungliga tillståndet.  Som standard tas också bort alla entitetsvärden, även om det här beteendet kan ändras (illustrerade nedan).

### <a name="open-the-demo"></a>Öppna demon

Klicka på självstudien-11-SessionCallbacks i listan över modellen. 

### <a name="entities"></a>Entiteter

Fyra entiteter har definierats i modellen.

![](../media/tutorial11_entities.PNG)

En sak att tänka på är att BotName är en programmatisk entitet.  Den här entiteten kommer anges boten på starttid för session.

### <a name="actions"></a>Åtgärder

Fyra åtgärder har definierats i modellen.

![](../media/tutorial11_actions.PNG)

Först visar den här självstudien hur du styr entitetsvärden i början av sessionen – till exempel ställa in entiteten BotName innan du säger något.

Andra: den här självstudien visar hur du bevarar värden från en session till nästa.  I den här självstudien förutsätter vi att användarens namn och telefonnummer ändras från en session till nästa, utan att ändra deras plats.  Vi kan därför spara namn och telefonnummer mellan sessioner, men avmarkera Användarplats.

### <a name="train-dialog"></a>Träna dialogrutan

Här är en exempel-dialogruta. Detta är en session – det vill säga det blir inga långt avbrott i den här dialogrutan.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Kod för återanropen

Koden för callback-metoder som finns i filen: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Båda dessa metoder är valfria.

- OnSessionStartCallback: den här metoden anger BotName entiteten.
- OnSessionEndCallback: du kan ange vad du vill behålla. Detta tar bort alla enheter utom användarnamn och telefonnummer för användaren.

### <a name="try-the-bot"></a>Försök roboten

Växla till Webbgränssnittet och klicka på logg-dialogrutor.

1. Ange ”hello”.
2. System ”: Hej, jag heter Botty. Vad heter du ”? som har det namnet Botty kommer från OnSessionStartCallback.
3. Ange ”jason'.
4. System ”: Hej jason. Vad är ditt telefonnummer ”?
5. Ange ' 555-555-5555'.
6. System: ”kan du berätta Botty din plats, jason”?
7. Skriv ”Redmond”.

Det här är en session. Vi behöver avsluta sessionen för att starta en ny session. 

1. Klicka på tidsgräns för Session. Det här flyttar du till nästa session.
    - Knappen ”tidsgräns för Session” har angetts för felsökning.  I en faktisk session måste en paus ske av cirka 30 minuter.  Finns på hjälpsidan på ”gränser” för standard sessionens tidsgräns längd.
1. Ange ”Hej”.
2. System: ”kan du berätta Botty din plats, jason”?
    - Systemet har kommer ihåg namn och telefonnummer.
2. Ange en ny plats: ”Seattle”.
3. System: 'därför jason du är i Seattle ”.
4. Klicka på klar testning.

Nu ska vi gå tillbaka till Log-dialogrutor. Lägg märke till de senaste konversationen har delats upp i två eftersom varje log-dialogrutan motsvarar en session.  

![](../media/tutorial11_splitdialogs.PNG)

- I den första interaktionen Botty har angetts, men namn och telefonnummer finns inte.
- Den andra interaktionen visar namn och telefonnummer.

Du har nu sett hur sessioner hanteras som standard och hur du kan åsidosätta standardbeteendet. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API-anrop](./12-api-calls.md)

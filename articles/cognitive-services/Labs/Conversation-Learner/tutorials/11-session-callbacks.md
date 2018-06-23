---
title: Hur du använder återanrop session med program i en konversation deltagaren - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig använda session återanrop med ett samtal deltagaren program.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f8970620c1f0f87ccae13d031092a048144ffb19
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354018"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-application"></a>Hur du använder session återanrop med ett samtal deltagaren program

Den här kursen visar onSessionStart och onSessionEnd återanrop.

## <a name="requirements"></a>Krav
Den här kursen kräver att ”tutorialSessionCallbacks.ts” bot körs.

    npm run tutorial-session-callbacks

## <a name="details"></a>Information
Den här självstudiekursen Beskriver konceptet med en session, hur sessioner hanteras som standard och hur du kan åsidosätta detta beteende.

En session är en konversation med bot. Den kan ha flera aktiverar, men det finns inga långa avbrott i konversationen (till exempel 30 minuter).  Se hjälpsidan på ”begränsningar” för standard session timeout-varaktighet.

Om det finns långa avbrott, kommer bot gå till nästa sessionen.  Startar en ny session placerar återkommande neurala nätverket i det ursprungliga tillståndet.  Som standard tas också bort alla entitet värden, även om det här beteendet kan ändras (illustrerade nedan).

### <a name="open-the-demo"></a>Öppna demonstrationen

Klicka på kursen-11-SessionCallbacks i listan över appar. 

### <a name="entities"></a>Entiteter

Vi har definierat fyra entiteter i programmet.

![](../media/tutorial11_entities.PNG)

Observera är att BotName programmässiga entitet.  Detta anges av bot på starttid för session.

### <a name="actions"></a>Åtgärder

Vi har skapat fyra åtgärder. 

![](../media/tutorial11_actions.PNG)

Först visar den här kursen hur du styr entitet värden i början av session--till exempel ange entiteten BotName innan användaren säger ingenting.

Andra, kommer den här kursen visar hur du bevara värden från en session till nästa.  I den här självstudiekursen förutsätter vi att användarnamn och telefonnummer ändras från en session till nästa, men som kan ändra deras plats.  Vi därför beständig namn och telefonnummer i alla sessioner, men Rensa Användarplats.

### <a name="train-dialog"></a>Train dialogrutan

Här är en exempel-dialogruta. Det här är en session - finns det ingen långa avbrott i den här dialogrutan.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Koden för återanrop

Koden för callback-metoder som finns i filen: c:\<installedpath > \src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Båda dessa metoder är valfria.

- OnSessionStartCallback: den här metoden anger BotName entiteten.
- OnSessionEndCallback: du kan ange vad du vill rensa. Detta tar bort alla enheter utom användarnamn och telefonnummer för användaren.

### <a name="try-the-bot"></a>Försök i bot

Växla till Webbgränssnittet och klicka på Logga dialogrutor.

1. Ange ”hello”.
2. System ”: Hej jag Botty. Vad heter ”? som har namnet Botty kommer från OnSessionStartCallback.
3. Ange 'jason'.
4. System: ' Hi jason. Vad är ditt telefonnummer ”?
5. Ange ' 555-555-5555'.
6. System: 'kan du avgöra Botty plats, jason ”?
7. Skriv ”Redmond”.

Det här är en session. Vi måste avslutas sessionen för att starta en ny session. 

1. Klicka på Sessionstidsgräns. Det här flyttar du till nästa session.
    - Knappen ”sessionstidsgränsen” anges för felsökning.  I en verklig session måste en paus inträffa cirka 30 minuter.  Se hjälpsidan på ”begränsningar” för standard session timeout-varaktighet.
1. Ange ”Hej”.
2. System: 'kan du avgöra Botty plats, jason ”?
    - Systemet har lagrade namn och telefonnummer.
2. Ange en ny plats: ”Seattle”.
3. System: 'så jason du befinner dig i Seattle'.
4. Klicka på klart testning.

Nu ska vi gå tillbaka till loggen dialogrutor. Observera den sista konversationen har delats upp i två eftersom varje logg dialogrutan motsvarar en session.  

![](../media/tutorial11_splitdialogs.PNG)

- I den första interaktionen Botty har angetts men namn och telefonnummer är inte.
- Den andra interaktionen visar namn och telefonnummer.

Nu har du sett hur sessioner hanteras som standard och hur du kan åsidosätta standardbeteendet. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [API-anrop](./12-api-calls.md)

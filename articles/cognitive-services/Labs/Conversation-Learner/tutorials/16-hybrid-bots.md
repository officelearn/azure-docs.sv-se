---
title: Använda Conversation Learner med andra tekniker för att skapa bot-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder Conversation Learner med andra metoder för att bygga bot.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: c964c62c34f952a547d077e93e7bb4d0eb7b192d
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703681"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Använda Conversation Learner med andra metoder för bot-skapande

Den här självstudien beskriver hur du använder Conversation Learner med andra funktioner för bot-teknik och hur minne (eller tillstånd) kan delas mellan dessa tekniker. 

## <a name="video"></a>Video

[![För hands version av hybrid robotar-självstudier](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Krav
I den här självstudien krävs att du använder bot-emulatorn för att skapa logg dialog rutor, inte webb gränssnittet för logg dialog rutan. Mer information om hur du konfigurerar bot Framework-emulatorn finns [här](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

I den här självstudien krävs att roboten hybrid självstudie körs:

    npm run tutorial-hybrid

## <a name="details"></a>Information

Medan Conversation Learner är i kontroll, måste alla tillstånd som är relativa till Conversation Learner-sessionen lagras i Conversation Learner minnes hanteraren. Detta är nödvändigt eftersom Machine Learning använder stadiet för att avgöra hur konversationen ska drivas. Extern status kan skickas till Conversation Learner i OnSessionStartCallback som anropas när sessionen börjar. Internt tillstånd kan returneras av OnSessionEndCallback när sessionen avslutas.

Du kan nästan tänka på Conversation Learner som ett funktions anrop som tar lite inledande tillstånd och returnerar värden.

I det här exemplet ska du skapa en hybrid robot med två olika system:
1. En Conversation Learner modell <br/>
    Använder konversations elev modell för att fastställa nästa åtgärd för roboten baserat på den aktuella sessionen. Den här delen av roboten tar ett initialt tillstånd `isOpen` (som anger om en butik är öppen eller stängd) och returnerar ett annat tillstånd `purchaseItem` (namnet på ett objekt som användaren köper).

2. Text matchning <br />
    Titta bara på inkommande text för vissa strängar och svara. Den här delen av roboten hanterar robotar andra lagrings metoder och ansvarar för att starta en CL-session. Särskilt hanterar den tre variabler: `usingConversationLearner`, `storeIsOpen`och `purchaseItem`.

Vi börjar med att ta en titt på modellen som används i den här demon.

### <a name="open-the-demo"></a>Öppna demon

I webb gränssnittet klickar du på "Importera självstudier" och väljer modellen med namnet "självstudie-16-HybridBot".

## <a name="entities"></a>Entiteter

Öppna sidan entiteter och Lägg märke till två `isOpen` entiteter: och`purchaseItem`

Om du vill förstå hur dessa entiteter används öppnar du filen `C:\<installedpath>\src\demos\tutorialHybrid.ts` : för att se återanropen.

Observera att koden i `OnSessionStartCallback` kopierar `storeIsOpen` värdet från BotBuilder konversation Storage `isOpen` som värdet för entiteten, så att den är tillgänglig för Conversation Learner. Se följande kod:

![](../media/tutorial17_sessionstart.PNG)

På samma sätt kopieras koden `OnSessionEndCallback` i (om sessionen avslutades på grund av en inlärd aktivitet och inte bara en tids gräns) som kopierar `purchaseItem` värdet för entity ut `purchaseItem`till BotBuilder-lagring. Se följande kod:

![](../media/tutorial17_sessionend.PNG)

Nu ska vi titta på åtgärderna.

## <a name="actions"></a>Åtgärder

Observera att modellen har fyra åtgärder.

De avsedda reglerna för åtgärderna är följande:

- `isOpen` Om entiteten har angetts kommer roboten att fråga "Vad vill du köpa?" och lagra det på `puchaseItem` plats.
- Om `isOpen` inte är inställt, kommer roboten att säga "Jag är förlått vi är stängt".
- De andra två åtgärderna är av typen `END_SESSION`.
- END_SESSION-åtgärden indikerar att ConversationLearner konversationen har slutförts.

### <a name="overall-bot-logic"></a>Allmän robot logik

Först ser du att om `usingConversationLearner` flaggan för bot-tillstånd har angetts, så skickas kontrollen till Conversation Learner. Annars skickar vi kontroll till något annat.  I det här exemplet visar vi enkel text matchning, men det kan vara en annan robot teknik, inklusive LUIS, QnA Maker och till och med en annan instans av Conversation Learner.

Vi behöver ett sätt för användaren att öppna och stänga butiken, så vi gör en sträng för att jämföra med "öppna Store" och "Stäng Store" och ange flaggan "storeIsOpen".

Sedan behöver vi ett sätt att utlösa hand kontroll över till vår Conversation Learners modell. När vi matchar "Shop"-strängen gör du följande:
- `usingConversationLearner` Ange flaggan i robotens minne.
- Anropa metoden "StartSession" i vår Conversation Learner-modell.  Detta utlöser "onSessionStartCallback" som initierar `isOpen` enhet svärdet

Se nedan:

![](../media/tutorial17_useConversationLearner.PNG)

Vi gör också en text matchning till "Historik" som visar det sista inköps objektet.
Slutligen, om något annat anges, visar vi tillgängliga användar kommandon

## <a name="train-dialog"></a>Träna dialog

I den här självstudien är modellen redan förtränad.  Vi kommer att testa hela bot för att se resultatet av återanropen från början och slutet av sessionen.

## <a name="testing-the-bot"></a>Testa roboten

Till skillnad från en robotar för en konversations modell kan du inte testa detta i Conversation Learner gränssnittet eftersom det bara kan visa vad som hanteras av Conversation Learners modellen.

### <a name="install-the-bot-framework-emulator"></a>Installera bot Framework-emulatorn

- Gå till [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Ladda ned och installera emulatorn.

### <a name="configure-the-emulator"></a>Konfigurera emulatorn

- Öppna emulatorn och kontrol lera att webb adressen är riktad mot samma port som roboten körs på. Förmoda`http://localhost:3978/api/messages`

### <a name="test"></a>Testa 

#### <a name="scenario-1-store-is-closed"></a>Scenario 1: Butiken är stängd
1. Ange "Shop". Detta hanteras av text matchningen och ger kontrollen till Conversation Learners modellen.
2. Ange Hello.  Eftersom `isOpen` värdet inte är inställt, kommer roboten att säga "Jag är stängt" och avsluta sessionen.

#### <a name="scenario-2-store-is-open"></a>Scenario 2: Butiken är öppen
1. Ange "Open Store".  Detta anger `isOpen` värdet sant.
1. Ange "Shop".
1. Ange Hello.  Eftersom `isOpen` värdet är inställt på Sant kommer roboten säga "Vad vill du köpa?"
1. Ange "stol". "stol" kommer att sparas i CL-minnet som entiteten `purchaseItem`. Anropet till slut sessionen anropas som kopierar det här värdet till konversations arkivet.
1. Ange "Historik".  Roboten säger att "du har köpt stolen" som detta var `purchaseItem`din sista.

## <a name="conclusion"></a>Sammanfattning

När du har lärt dig ovan bör du kunna kombinera Conversation Learner med andra typer av bot-teknik.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Förgrening och ångra](./17-branch-undo.md)

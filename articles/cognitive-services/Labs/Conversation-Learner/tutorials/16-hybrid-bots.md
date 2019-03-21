---
title: Hur du använder Konversationsdeltagare med andra bot bygger teknik - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du använder Konversationsdeltagare med andra bot bygger teknik.
services: cognitive-services
author: mattm
manager: larsliden
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 07/13/2018
ms.author: v-jaswel
ms.openlocfilehash: b90fc42aa56bfc813ec464670336dea75cff0f0e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117446"
---
# <a name="how-to-use-conversation-learner-with-other-bot-building-technologies"></a>Hur du använder Konversationsdeltagare med andra bot bygger teknik

Den här självstudien beskriver hur du använder Konversationsdeltagare med andra robot att skapa tekniker och hur minne (eller tillstånd) kan delas mellan dessa tekniker. 

## <a name="video"></a>Video

[![Hybrid robotar självstudiekursen förhandsversion](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications_Preview)](https://aka.ms/cl_Tutorial_v3_Hybrid_Applications)

## <a name="requirements"></a>Krav
Den här självstudien kräver bot-emulatorn för att skapa log dialogrutor, inte Log dialogrutan Webbgränssnittet. Mer information om hur du konfigurerar Bot Framework emulatorn finns [här](https://docs.microsoft.com/azure/bot-service/bot-service-debug-emulator?view=azure-bot-service-4.0). 

Den här självstudien krävs att hybrid självstudiekursen bot körs:

    npm run tutorial-hybrid

## <a name="details"></a>Information

Konversationsdeltagare är kontrollen, måste alla tillstånd i förhållande till konversationen Learner sessionen lagras i den Konversationsdeltagare minneshanteraren. Detta är nödvändigt eftersom den machine learning använder tillståndet för att avgöra hur du kan implementera konversationen. Externa tillstånd kan skickas till Konversationsdeltagare i OnSessionStartCallback som anropas när sessionen börjar. Interna tillståndet kan returneras ut av OnSessionEndCallback när sessionen avslutas.

Du kan nästan Se Konversationsdeltagare som ett funktionsanrop som använder vissa starttillståndet och returnerar värden.

I det här exemplet skapar du en hybrid-robot som använder två olika system:
1. En konversation Learner modell <br/>
    Använder konversationen learner modellen för att avgöra nästa åtgärd för robotens baserat på den aktuella sessionen. Den här delen av roboten tar en del av ursprungliga tillståndsdata `isOpen` (som anger om en butik är öppna eller stängda) och returnerar en annan typ av tillstånd `purchaseItem` (namnet på ett objekt som du köper).

2. Text som matchar <br />
    Bara tittar på inkommande text efter specifika strängar och svarar. Den här delen av roboten hanterar robotar andra mekanismer som lagring och ansvarar för att starta CL-sessionen. Mer specifikt den hanterar här tre variablerna: `usingConversationLearner`, `storeIsOpen`, och `purchaseItem`.

Låt oss börja med att ta en titt på modellen som används i den här demon.

### <a name="open-the-demo"></a>Öppna demon

Klicka på ”Importera Tutorials” och välja modellen med namnet ”självstudien-16-HybridBot” i webbgränssnittet.

## <a name="entities"></a>Entiteter

Öppna sidan entiteter och Lägg märke till två entiteter: `isOpen` och `purchaseItem`

För att förstå hur de här entiteterna används, öppna filen: `C:\<installedpath>\src\demos\tutorialHybrid.ts` att titta på återanrop.

Observera att koden i `OnSessionStartCallback` kopierar värdet för `storeIsOpen` från BotBuilder konversationen storage som värde för den `isOpen` entiteten så att den är tillgänglig för Konversationsdeltagare. Se följande kod:

![](../media/tutorial17_sessionstart.PNG)

På samma sätt kan koden i `OnSessionEndCallback` (om sessionen avslutades på grund av en inlärda aktivitet och inte bara en tidsgräns) kopierar du värdet för entiteten `purchaseItem` ut till BotBuilder storage `purchaseItem`. Se följande kod:

![](../media/tutorial17_sessionend.PNG)

Nu ska vi titta på åtgärderna.

## <a name="actions"></a>Åtgärder

Observera modellen har fyra åtgärder.

Avsedda regler för åtgärderna som är följande:

- Om den `isOpen` entiteten har angetts måste roboten frågar ”vad du vill köpa”? och lagra det i den `puchaseItem` fack.
- Om `isOpen` inte ange roboten säger ”jag har vi är stängd”.
- De övriga två åtgärderna är av typen `END_SESSION`.
- Åtgärden END_SESSION anger ConversationLearner att konversationen har slutförts.

### <a name="overall-bot-logic"></a>Övergripande Bot-logik

Först du ser att om tillståndet Bot `usingConversationLearner` flagga har angetts, vi skicka kontroll Konversationsdeltagare. Om inte, vi skicka kontroll till något annat.  I det här exemplet vi visar enkla text som matchar, men det kan vara någon annan Bot-teknik, inklusive LUIS, QnA maker och även en annan instans av Konversationsdeltagare.

Vi behöver ett sätt för användare att öppna och Stäng arkivet, så vi gör en sträng Jämför med ”öppna store” och ”close store” och ställa in flaggan ”storeIsOpen”.

Därefter behöver vi ett sätt att utlösa överlåtelse kontroll över vår konversationen Learner modell. När vi matchar strängen ”köp” gör vi följande:
- Ange den `usingConversationLearner` flaggan i Robotens minne.
- Anropa metoden ”StartSession” på vår konversationen Learner modell.  Detta utlöser den ”onSessionStartCallback” som initierar den `isOpen` entiteten värde

Nedan följer:

![](../media/tutorial17_useConversationLearner.PNG)

Vi kan också göra en matchning med texten ”historik” som visar det sista köp-objektet.
Slutligen, om något annat skrivs vi visa tillgängliga kommandon

## <a name="train-dialog"></a>Träna dialogrutan

Modellen är redan förtränade i den här självstudien.  Vi kommer att testa fullständig roboten för att se effekten av början och avsluta sessionen återanrop i praktiken.

## <a name="testing-the-bot"></a>Testa roboten

Till skillnad från enkel konversationen Leaner modellen robotar du inte testa det här i Användargränssnittet för Learner konversationen eftersom det kan bara visa vad hanteras av konversationen Learner modellen.

### <a name="install-the-bot-framework-emulator"></a>Installera Bot framework-emulatorn

- Gå till [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Ladda ned och installerar sedan emulatorn.

### <a name="configure-the-emulator"></a>Konfigurera emulatorn

- Öppna emulatorn och kontrollera URL: en är inställd på samma port din bot körs på. Troligt: `http://localhost:3978/api/messages`

### <a name="test"></a>Testa 

#### <a name="scenario-1-store-is-closed"></a>Scenario 1: Store är stängd
1. Ange ”köp”. Detta hanteras av den text som matchar och ger åtkomstkontroll till modellen Konversationsdeltagare.
2. Ange ”hello”.  Eftersom `isOpen` värdet inte anges, roboten säger ”jag har vi är stängd” och avsluta sessionen.

#### <a name="scenario-2-store-is-open"></a>Scenario 2: Store är öppen
1. Ange ”öppna store”.  Detta anger den `isOpen` till true.
1. Ange ”köp”.
1. Ange ”hello”.  Eftersom `isOpen` värdet anges till SANT, roboten står ”vad du vill köpa”?
1. Ange ”leda'. ”chair” kommer att sparas i minnet CL som entiteten `purchaseItem`. End-session återanrop har anropats som kopierar det här värdet till arkivet för konversationen.
1. Ange ”historik”.  Roboten säger ”du har köpt chair” eftersom detta är sist `purchaseItem`.

## <a name="conclusion"></a>Sammanfattning

Med det du har lärt dig ovan bör du kunna kombinera Konversationsdeltagare med andra robot som bygger teknik.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Branchning och ångra](./17-branch-undo.md)

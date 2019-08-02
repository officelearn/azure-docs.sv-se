---
title: Så här skapar du en Conversation Learner modell med Node. js-Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en Conversation Learner modell med Node. js.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 7ab32fb421a2c0db72652d1bbf12d312bffd5d1e
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68706535"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Skapa en Conversation Learner modell med Node. js

Conversation Learner minskar komplexiteten med att skapa robotar. Det möjliggör ett hybrid utvecklings arbets flöde som tillåter handskriven kod och maskin inlärning för att minska den mängd kod som krävs för att skriva robotar. Vissa fasta delar av din modell, till exempel att kontrol lera om användaren är inloggad eller göra en API-begäran för att kontrol lera lager inventeringen, kan fortfarande kodas. Andra ändringar i tillstånds-och åtgärds urvalet kan dock läsas från exempel dialog rutor som ges av domän experten eller utvecklaren.

## <a name="invitation-required"></a>Inbjudan krävs

*En inbjudan krävs för att få åtkomst till Project Conversation Learner.*

Project Conversation Learner består av ett SDK som du lägger till i din robot och en moln tjänst som SDK-åtkomsten till för Machine Learning.  För närvarande krävs en inbjudan för att få åtkomst till projekt konversationens moln tjänst.  Om du inte redan har bjudit in [den begär du en inbjudan](https://aka.ms/conversation-learner-request-invite).  Om du inte har fått någon inbjudan kan du inte komma åt moln-API: et.

## <a name="prerequisites"></a>Förutsättningar

- Node 8.5.0 eller högre och NPM 5.3.0 eller högre. Installera från [https://nodejs.org](https://nodejs.org).
  
- LUIS redigerings nyckel:

  1. Logga in [https://www.luis.ai](https://www.luis.ai)på.

  2. Klicka på ditt namn längst upp till höger, sedan på "Inställningar"

  3. Redigerings nyckeln visas på den resulterande sidan

  (Din LUIS redigerings nyckel har 2 roller.  Först fungerar det som Conversation Learner redigerings nyckel.  För det andra använder Conversation Learner LUIS för enhets extrahering. LUIS Authoring-nyckeln används för att skapa LUIS-modeller för din räkning)

- Google Chrome-webbläsare. Installera från [https://www.google.com/chrome/index.html](https://www.google.com/chrome/index.html).

- git. Installera från [https://git-scm.com/downloads](https://git-scm.com/downloads).

- VSCode. Installera från [https://code.visualstudio.com/](https://code.visualstudio.com/). OBS! detta rekommenderas inte, vilket inte krävs.

## <a name="quick-start"></a>Snabbstart 

1. Installera och bygg:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Under `npm install`kan du ignorera det här felet om det inträffar:`gyp ERR! stack Error: Can't find Python executable`

2. Konfigurera:

   Skapa en fil som `.env` heter i katalogen `cl-bot-01`.  Filens innehåll ska vara:

   ```
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Starta bot:

    ```
    npm start
    ```

    Detta kör den generiska tomma roboten `cl-bot-01/src/app.ts`i.

3. Öppna webbläsare för att`http://localhost:3978`

Du använder nu Conversation Learner och kan skapa och lära en Conversation Learner modell.  

> [!NOTE]
> Vid lanseringen är Project Conversation Learner tillgängligt via inbjudan.  Om `http://localhost:3978/ui` visar ett http `403` -fel innebär det att ditt konto inte har bjudits in.  [Be om en inbjudan](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Självstudier, demonstrationer och växlar mellan robotar

Anvisningarna ovan startade den generiska tomma roboten.  För att köra en självstudie eller demo-robot i stället:

1. Om du har öppnat Conversation Learner webb gränssnittet går du tillbaka till listan över modeller i `http://localhost:3978/ui/home`.
    
2. Om en annan robot körs (som `npm start` eller `npm run demo-pizza`) stoppar du den.  Du behöver inte stoppa UI-processen eller stänga webbläsaren.

3. Kör en demo-robot från kommando raden (steg 2 ovan).  I demonstrationer ingår:

   ```bash
   npm run tutorial-general
   npm run tutorial-entity-detection
   npm run tutorial-session-callbacks
   npm run tutorial-api-calls
   npm run tutorial-hybrid
   npm run demo-password
   npm run demo-pizza
   npm run demo-storage
   ```

4. Om du inte redan har gjort det växlar du till Conversation Learner webb gränssnitt i Chrome genom `http://localhost:3978/ui/home`att läsa in. 

5. Klicka på "Importera självstudier" och välj demonstrations modellen i Conversation Learner gränssnittet som motsvarar den demo som du startade.

Källfiler för demonstrationerna finns i`cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Skapa en robot som innehåller backend-kod

1. Om du har öppnat Conversation Learner webb gränssnittet går du tillbaka till listan över modeller i `http://localhost:3978/ui/home`.
    
2. Stoppa den om en robot körs ( `npm run demo-pizza`som).  Du behöver inte stoppa UI-processen eller stänga webbläsaren.

3. Om du vill kan du redigera `cl-bot-01/src/app.ts`kod i.

4. Återskapa och starta om bot:

    ```bash    
    npm run build
    npm start
    ```

5. Om du inte redan har gjort det växlar du till Conversation Learner webb gränssnitt i Chrome genom `http://localhost:3978/ui/home`att läsa in. 

6. Skapa en ny Conversation Learner modell i användar gränssnittet och börja undervisningen.

7. Om du vill göra kod `cl-bot-01/src/app.ts`ändringar i upprepar du stegen ovan, från steg 2.

## <a name="vscode"></a>VSCode

I VSCode körs konfigurationer för varje demo och för "Tom robot" i `cl-bot-01/src/app.ts`.  `cl-bot-01` Öppna mappen i VSCode.

## <a name="advanced-configuration"></a>Avancerad konfiguration

Det finns en mallfil `.env.example` som visar vilka miljövariabler som du kan konfigurera för att konfigurera exemplen.

Du kan justera dessa portar för att undvika konflikter mellan andra tjänster som körs på datorn genom att `.env` lägga till en fil i roten för projektet:

```bash
cp .env.example .env
```

Detta använder standard konfigurationen, som gör att du kan köra din robot lokalt och börja använda Conversation Learner.  (Senare om du vill distribuera din robot till bot-ramverket krävs vissa ändringar i den här filen.)

## <a name="support"></a>Support

- Tagga frågor på [Stack Overflow](https://stackoverflow.com) med "Microsoft kognitivt"
- Begär en funktion på vår [användares röst sida](https://aka.ms/conversation-learner-uservoice)
- Öppna ett ärende på vår [GitHub-lagrings platsen](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Bidrag

Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Läs [Vanliga frågor och svar om uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta [opencode@microsoft.com](mailto:opencode@microsoft.com) om du har några andra frågor eller kommentarer.

## <a name="source-repositories"></a>Käll databaser

- [conversationlearner – exempel](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-UI](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hej världen](./tutorials/01-hello-world.md)

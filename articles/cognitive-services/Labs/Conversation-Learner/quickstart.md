---
title: Så här skapar du en konversation deltagaren-program med hjälp av Node.js - kognitiva Microsoft-tjänster | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en konversation deltagaren-program med hjälp av Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a3a51aa86a30b060c8dc4113da69462904d7df54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355086"
---
# <a name="create-a-conversation-learner-application-using-nodejs"></a>Skapa en konversation deltagaren-program med Node.js

Konversationen deltagaren minskar den för att skapa robotar. Den gör en hybrid utveckling-arbetsflöde handskriven koden och maskininlärning att minska den kod som krävs för att skriva robotar tillåts. Vissa fast delar av programmet, t.ex kontrollera om användaren är inloggad eller göra en API-begäran att kontrollera Lagerhantering, kan fortfarande kodas. Andra ändringar i tillstånd och åtgärder kan dock erfarenheterna från exempel dialogrutor expert på domänen eller utvecklare.

## <a name="invitation-required"></a>Inbjudan som krävs

*En inbjudan krävs för att få åtkomst till projektet konversation deltagaren.*

Projektet konversation deltagaren består av ett SDK som du lägger till i din bot och en molnbaserad tjänst som har åtkomst till för machine learning SDK.  För närvarande kräver åtkomst till projektet konversation Leaner Molntjänsten en inbjudan.  Om du redan inte har bjudits [begära en inbjudan](https://aka.ms/conversation-learner-request-invite).  Om du inte har fått en inbjudan du inte åtkomst till molnet API.

## <a name="prerequisites"></a>Förutsättningar

- Noden 8.5.0 eller högre och npm 5.3.0 eller högre. Installera från [ https://nodejs.org ](https://nodejs.org).
  
- THOMAS redigering nyckel:

  1. Logga in på [ http://www.luis.ai ](http://www.luis.ai).

  2. Klicka på ditt namn i det övre högra hörnet på ”inställningar”

  3. Redigera nyckel visas på sidan resulterande

  (Din THOMAS redigering nyckel fungerar 2 roller.  Först, fungerar den som din konversation deltagaren redigering av nyckeln.  Andra använder konversation deltagaren THOMAS för entiteten extrahering; THOMAS redigering nyckel används för att skapa THOMAS modeller för din räkning)

- Google Chrome webbläsare. Installera från [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- Git. Installera från [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Installera från [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Observera att detta rekommenderas inte krävs.

## <a name="quick-start"></a>Snabbstart 

1. Installera och skapa:

    ```bash    
    git clone https://github.com/Microsoft/ConversationLearner-Samples cl-bot-01
    cd cl-bot-01
    npm install
    npm run build
    ```

    > [!NOTE]
    > Under `npm install`, du kan ignorera det här felet om det inträffar: `gyp ERR! stack Error: Can't find Python executable`

2. Konfigurera:

   Skapa en fil med namnet `.env` i katalogen `cl-bot-01`.  Innehållet i filen bör vara:

   ```
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Starta bot:

    ```
    npm start
    ```

    Detta kör den allmänna tom bot i `cl-bot-01/src/app.ts`.

3. Kör konversation deltagaren UI:

    ```bash
    [open second command prompt window]
    cd cl-bot-01
    npm run ui
    ```

4. Öppna webbläsaren http://localhost:5050 

Du kan använder nu konversation deltagaren och skapa och utbilda en konversation deltagaren modell.  

> [!NOTE]
> Vid start finns projektet konversation deltagaren som inbjudan.  Om http://localhost:5050 visar HTTP `403` fel, det innebär att ditt konto inte har bjudits in.  Kontrollera [begära en inbjudan](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Självstudier, demonstrationer och växla mellan robotar

Anvisningarna ovan igång generiska tom bot.  Att köra en självstudiekurs eller demonstration bot i stället:

1. Om du har konversation deltagaren webben UI öppna återgå till listan över appar i http://localhost:5050/home.
    
2. Om en annan bot körs (t.ex. `npm start` eller `npm run demo-pizza`), stoppa den.  Du behöver inte att stoppa processen Användargränssnittet eller stänger webbläsaren.

3. Kör en demo bot från kommandoraden (steg 2 ovan).  Demonstrationer inkluderar:

  ```bash
  npm run tutorial-general
  npm run tutorial-entity-detection
  npm run tutorial-session-callbacks
  npm run tutorial-api-calls
  npm run demo-password
  npm run demo-pizza
  npm run demo-storage
  npm run demo-vrapp
  ```

4. Om du inte redan kan växla till konversationen deltagaren webbgränssnittet i Chrome genom att läsa in http://localhost:5050/home. 

5. Klicka på ”Importera självstudier” (behöver bara göras en gång).  Detta tar ungefär en minut och kopierar konversation deltagaren modeller för alla kurser till ditt konto för konversationen deltagaren.

6. Klicka på demo-modellen i Användargränssnittet för konversationen deltagaren som motsvarar demo som du startade.

Källfilerna för dessa demonstrationer finns i `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Skapa en bot som innehåller backend-kod

1. Om du har konversation deltagaren webben UI öppna återgå till listan över appar i http://localhost:5050/home.
    
2. Om en bot körs (t.ex. `npm run demo-pizza`), stoppa den.  Du behöver inte att stoppa processen Användargränssnittet eller stänger webbläsaren.

3. Om du vill redigera koden i `cl-bot-01/src/app.ts`.

4. Återskapa och starta bot:

    ```bash    
    npm run build
    npm start
    ```

5. Om du inte redan kan växla till konversationen deltagaren webbgränssnittet i Chrome genom att läsa in http://localhost:5050/home. 

6. Skapa ett nytt program för konversationen deltagaren i Användargränssnittet och starta lärare.

7. Att ändra koden i `cl-bot-01/src/app.ts`, upprepa stegen ovan startar från steg 2.

## <a name="vscode"></a>VSCode

I VSCode, det körs konfigurationer för varje demo och ”tom bot” i `cl-bot-01/src/app.ts`.  Öppna den `cl-bot-01` mapp i VSCode.

## <a name="advanced-configuration"></a>Avancerad konfiguration

Det finns en mall `.env.example` filen visar vilken miljö variabler som du kan ange för att konfigurera exemplen.

Du kan justera de här portarna för att undvika konflikter mellan andra tjänster som körs på datorn genom att lägga till en `.env` filen till roten av projektet:

```bash
cp .env.example .env
```

Här används i standardkonfigurationen som låter dig köra din bot lokalt och börja använda konversation deltagaren.  (Vid ett senare tillfälle om du vill distribuera dina bot Bot Framework vissa ändringar i den här filen krävs.)

## <a name="support"></a>Support

- Tagga frågorna [Stack Overflow](https://stackoverflow.com) med ”microsoft kognitiva”
- Förfrågan om en funktion på vår [User Voice-sida](https://aka.ms/conversation-learner-uservoice)
- Öppna ett problem på vår [github-repo](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Bidra

Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Mer information finns i [genomföra FAQ kod](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta [ opencode@microsoft.com ](mailto:opencode@microsoft.com) med några andra frågor eller kommentarer.

## <a name="source-repositories"></a>Käll-databaser

- [conversationlearner-exempel](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner modeller](https://github.com/Microsoft/ConversationLearner-Models)
- [ui-conversationlearner](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hej världen](./tutorials/1-hello-world.md)

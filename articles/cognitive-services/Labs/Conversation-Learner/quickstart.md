---
title: Så här skapar du en Konversationsdeltagare modell med hjälp av Node.js - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig hur du skapar en Konversationsdeltagare modell med hjälp av Node.js.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 82738d6d97dc2c14c83ed483c4a1d76604d0cd91
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57545344"
---
# <a name="create-a-conversation-learner-model-using-nodejs"></a>Skapa en Konversationsdeltagare modell med hjälp av Node.js

Konversationsdeltagare minskar komplexiteten med att skapa robotar. Det gör att en hybrid utveckling-arbetsflödet för att tillåta handskrivna kod och maskininlärning att minska mängden kod som krävs för att skriva robotar. Vissa fast delar av din modell, till exempel kontrollerar om användaren är inloggad eller gör en API-begäran för att kontrollera Lagerhantering, kan fortfarande kodas. Andra ändringar i val av tillstånd och åtgärder kan dock läras in från exempel-dialogrutor som anges av domänexpert eller utvecklare.

## <a name="invitation-required"></a>Inbjudan krävs

*En inbjudan krävs för att komma åt Konversationsdeltagare för projektet.*

Projektet Konversationsdeltagare består av ett SDK som du lägger till att din robot, och en molntjänst som SDK: N har åtkomst till för machine learning.  För närvarande kräver åtkomst till Molntjänsten projekt konversationen Leaner inbjudan.  Om du inte har blivit inbjuden redan [begära en inbjudan](https://aka.ms/conversation-learner-request-invite).  Om du inte har fått en inbjudan kan du inte få tillgång till molnet API.

## <a name="prerequisites"></a>Förutsättningar

- Noden 8.5.0 eller senare och npm 5.3.0 eller senare. Installera från [ https://nodejs.org ](https://nodejs.org).
  
- LUIS redigering nyckel:

  1. Logga in på [ https://www.luis.ai ](https://www.luis.ai).

  2. Klicka på ditt namn i det övre högra hörnet på ”inställningar”

  3. Redigera nyckel visas på sidan resulterande

  (Din LUIS redigering nyckel fungerar 2-roller.  Först, fungerar den som din Konversationsdeltagare redigering av nyckeln.  Andra använder Konversationsdeltagare LUIS för entitetextrahering; LUIS redigering nyckel används för att skapa LUIS modeller för din räkning)

- Webbläsaren Google Chrome. Installera från [ https://www.google.com/chrome/index.html ](https://www.google.com/chrome/index.html).

- Git. Installera från [ https://git-scm.com/downloads ](https://git-scm.com/downloads).

- VSCode. Installera från [ https://code.visualstudio.com/ ](https://code.visualstudio.com/). Observera rekommenderas det inte krävs.

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
   NODE_ENV=development
   LUIS_AUTHORING_KEY=<your LUIS authoring key>
   ```

3. Starta bot:

    ```
    npm start
    ```

    Den här raden kör allmän tom roboten `cl-bot-01/src/app.ts`.

3. Öppna webbläsaren till http://localhost:3978

Du kan använder nu Konversationsdeltagare och skapa och undervisar en Konversationsdeltagare-modell.  

> [!NOTE]
> Projektet Konversationsdeltagare är tillgängligt genom inbjudan via vid start.  Om http://localhost:3978/ui visar HTTP `403` fel, det innebär att ditt konto inte har blivit inbjudet.  . [Begära en inbjudan](https://aka.ms/conversation-learner-request-invite).

## <a name="tutorials-demos-and-switching-between-bots"></a>Självstudier, demonstrationer och växla mellan robotar

Anvisningarna ovan igång allmän tom roboten.  Att köra en självstudiekurs eller demonstrera bot i stället:

1. Om du har Konversationsdeltagare webben öppna Användargränssnittet kan gå tillbaka till listan över modeller i http://localhost:3978/ui/home.
    
2. Om en annan bot körs (t.ex. `npm start` eller `npm run demo-pizza`), stoppa den.  Du behöver inte att stoppa processen Användargränssnittet eller stänger webbläsaren.

3. Kör en demo-robot från kommandoraden (steg 2 ovan).  Demonstrationer är:

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

4. Om du inte redan, växla till Konversationsdeltagare webbgränssnittet i Chrome genom att läsa in http://localhost:3978/ui/home. 

5. Klicka på ”Importera tutorials” och välj demo-modell i Användargränssnittet för konversationen Learner som motsvarar demon du igång.

Källfilerna för demonstrationerna finns i `cl-bot-01/src/demos`

## <a name="create-a-bot-which-includes-back-end-code"></a>Skapa en robot som innehåller backend-koden

1. Om du har Konversationsdeltagare webben öppna Användargränssnittet kan gå tillbaka till listan över modeller i http://localhost:3978/ui/home.
    
2. Om en bot körs (t.ex. `npm run demo-pizza`), stoppa den.  Du behöver inte att stoppa processen Användargränssnittet eller stänger webbläsaren.

3. Om du vill kan du redigera koden i `cl-bot-01/src/app.ts`.

4. Återskapa och starta bot:

    ```bash    
    npm run build
    npm start
    ```

5. Om du inte redan, växla till Konversationsdeltagare webbgränssnittet i Chrome genom att läsa in http://localhost:3978/ui/home. 

6. Skapa en ny modell för Konversationsdeltagare i Användargränssnittet och starta undervisning.

7. Att göra kodändringar i `cl-bot-01/src/app.ts`, upprepa stegen ovan, med början från steg 2.

## <a name="vscode"></a>VSCode

I VSCode, det körs konfigurationer för varje demo och för ”tom roboten” i `cl-bot-01/src/app.ts`.  Öppna den `cl-bot-01` mapp i VSCode.

## <a name="advanced-configuration"></a>Avancerad konfiguration

Det finns en mall `.env.example` filen visar vilken miljö variabler som du kan ställa in för att konfigurera exemplen.

Du kan justera de här portarna för att undvika konflikter mellan andra tjänster som körs på datorn genom att lägga till en `.env` filen till roten av projektet:

```bash
cp .env.example .env
```

Här används i standardkonfigurationen som låter dig köra din robot lokalt och börja använda Konversationsdeltagare.  (Vid ett senare tillfälle för att distribuera din robot i Bot Framework, vissa ändringar i den här filen krävs.)

## <a name="support"></a>Support

- Märk dina frågor [Stack Overflow](https://stackoverflow.com) med ”microsoft cognitive”
- Begär en funktion på vår [User Voice-sidan](https://aka.ms/conversation-learner-uservoice)
- Skapa ett problem på vår [GitHub-lagringsplatsen](https://github.com/Microsoft/ConversationLearner-Samples)

## <a name="contributing"></a>Bidra

Det här projektet använder sig av [Microsofts uppförandekod för öppen källkod](https://opensource.microsoft.com/codeofconduct/). Läs [Vanliga frågor och svar om uppförandekoden](https://opensource.microsoft.com/codeofconduct/faq/) eller kontakta [opencode@microsoft.com](mailto:opencode@microsoft.com) om du har några andra frågor eller kommentarer.

## <a name="source-repositories"></a>Lagringsplatser för källkod

- [conversationlearner-exempel](https://github.com/Microsoft/ConversationLearner-Samples)
- [conversationlearner-sdk](https://github.com/Microsoft/ConversationLearner-SDK)
- [conversationlearner-models](https://github.com/Microsoft/ConversationLearner-Models)
- [conversationlearner-ui](https://github.com/Microsoft/ConversationLearner-UI)
- [conversationlearner-webchat](https://github.com/Microsoft/ConversationLearner-WebChat)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hej världen](./tutorials/01-hello-world.md)

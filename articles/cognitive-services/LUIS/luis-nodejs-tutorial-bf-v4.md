---
title: Language Understanding Bot Node.js v4
titleSuffix: Azure Cognitive Services
description: Använd Node.js och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här chattroboten använder appen Personalfrågor för att snabbt implementera en robotlösning. Roboten skapas med Bot Framework version 4 och webbappsroboten i Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/15/2019
ms.author: diberry
ms.openlocfilehash: 832a62c5cc5440d81f4b92d2463a563f5bb884a3
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150827"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Självstudier: Använd en Web App-robot som aktiverats med Language Understanding i Node.js 

Använd Node.js för att skapa en chattrobot som är integrerad med språkförståelse (LUIS). Roboten har skapats med Azure [Web app-robot](https://docs.microsoft.com/azure/bot-service/) resurs och [Bot Framework-version](https://github.com/Microsoft/botbuilder-dotnet) V4.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Ladda ned bot-projekt som skapats av bot webbtjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Skapa en web app-robot resurs

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

1. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

1. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-nodejs-bot-resource-group`.|
    |Location|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|
    |App service-plan/plats|Ändra inte från angivna standardvärdet.|
    |Application Insights|Ändra inte från angivna standardvärdet.|
    |Microsoft App-ID och lösenord|Ändra inte från angivna standardvärdet.|

1. I den **Bot mallen**, väljer du följande och välj sedan den **Välj** knappen under de här inställningarna:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**Node.js**|
    |Bot|Typ av bot|**Basic bot** (Grundläggande robot)|
    
1. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-nodejs-bot-XXXX`. Det här namnet är baserad på appnamnet /Azure Bot Service.

    [![Skapa en webbappsrobot](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Vänta tills bot-tjänst skapas innan du fortsätter.

## <a name="the-bot-has-a-language-understanding-model"></a>Roboten har en Luis-modell

Bot serviceprocessen skapar också en ny LUIS-app med avsikter och exempel yttranden. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter: 

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Bok flygning|`Travel to Paris`|
|Avbryt|`bye`|
|Ingen|Vad som helst utanför appens domän.|

## <a name="test-the-bot-in-web-chat"></a>Testa roboten i Web Chat

1. När du fortfarande i Azure-portalen för den nya roboten, Välj **Test i Web Chat**. 
1. I den **Skriv meddelandet** textrutan Ange texten `hello`. Roboten svarar med information om bot framework, samt Exempelfrågor för specifika LUIS-modellen, till exempel en flygning till Paris för bokning. 

    ![Skärmbild av Azure-portalen anger du texten ”hello”.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Du kan använda funktionen test för att snabbt testa din robot. För mer Slutför testning, inklusive felsökning, ladda ned bot-koden och använder Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Ladda ned källkoden för web app-robot
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn. 

1. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen. 

1. Välj **Download Bot source code** (Ladda ned robotens källkod). 

    [![Ladda ned källkoden för webbappsroboten för grundläggande robot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. När popup frågan **omfattar appinställningar i den hämta zip-filen?** väljer **Ja**.

1. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken. 

1. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektet med Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Granska koden för att skicka uttryck till LUIS och få svar

1. Öppna den **dialogrutor -> luisHelper.js** fil. Det är här som användaruttrycket som angetts i roboten skickas till LUIS. Svaret från LUIS returneras från metoden som en **bookDetails** JSON-objekt. När du skapar dina egna bot, bör du också skapa egna objekt för att returnera information från LUIS. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

1. Öppna **dialogrutor -> bookingDialog.js** att förstå hur BookingDetails objektet används för att hantera flödet konversationen. Resor information uppmanas i steg och hela bokningen bekräftas och slutligen upprepas tillbaka till användaren. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Installera beroenden och starta bot-kod i Visual Studio

1. I VSCode, från den integrerade terminalen installerar beroenden med kommandot `npm install`.
1. Också från den integrerade terminalen starta roboten med kommandot `npm start`. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>Använda bot-emulatorn för att testa roboten

1. Börja Bot-emulatorn och välj **öppna Bot**.
1. I den **öppna en robot** standardarbetsytan i dialogrutan, ange din bot-URL: en, till exempel `http://localhost:3978/api/messages`. Den `/api/messages` vägen är webbadressen för roboten.
1. Ange den **Microsoft App-ID** och **lösenord för Microsoft App**, som finns i den **.env** filen i roten för bot-koden som du laddade ned.

    Alternativt kan du skapa en ny robot konfiguration och kopiera den `MicrosoftAppId` och `MicrosoftAppPassword` från den **.env** filen i Visual Studio-projektet för roboten. Namnet på konfigurationsfilen bot bör vara samma som namnet på bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. Ange i emulatorn bot `Hello` och få samma svar för den grundläggande bot som du fick i det **Test i Web Chat**.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Fråga roboten för boken flygning avsikten

1. Boka en flygning i bot-emulatorn genom att ange följande uttryck: 

    ```bot
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Bot-emulatorn ombeds att bekräfta. 

1. Välj **Ja**. Roboten svarar med en sammanfattning av dess åtgärder. 
1. Loggen bot-emulatorns, Välj raden som innehåller `Luis Trace`. Detta visar JSON-svar från LUIS för avsikt och entiteter av uttryck.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

## <a name="learn-more-about-the-web-app-bot-and-framework"></a>Läs mer om Web App-robot och ramverk

Azure Bot Service använder Bot Framework SDK. Lär dig mer om SDK och Bot Framework:

* Dokumentation om [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Bot Builder-exempel](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder Node.js SDK](https://github.com/Microsoft/botbuilder-js)
* [Bot Builder-verktyg](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Nästa steg

Se fler [exempel](https://github.com/microsoft/botframework-solutions) med konversationsrobotar. 

> [!div class="nextstepaction"]
> [Skapa en Luis-app med en anpassad certifikatmottagare-domän](luis-quickstart-intents-only.md)
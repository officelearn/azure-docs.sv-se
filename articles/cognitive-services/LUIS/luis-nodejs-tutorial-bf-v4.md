---
title: 'Självstudie: Language Understanding bot Node.js v4'
description: Använd den här självstudien för att bygga en chatt-robot integrerad med språk förståelse (LUIS). Den här chattroboten använder appen Personalfrågor för att snabbt implementera en robotlösning.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/30/2020
ms.custom: devx-track-js
ms.openlocfilehash: dd5f97f2b1c2ad51dda3446412cd936e45c5982e
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96436903"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Självstudie: använda en Web App-robot som är aktive rad med Language Understanding i Node.js

Använd Node.js för att bygga en chatt-robot integrerad med språk förståelse (LUIS). Roboten är byggd med Azure [Web App bot-](/azure/bot-service/) resursen och [bot Framework version](https://github.com/Microsoft/botbuilder-dotnet) v4.

**I den här guiden får du lära dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Hämta bot-projektet som skapats av Web bot-tjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Förutsättningar

* [Bot Framework-emulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

## <a name="create-a-web-app-bot-resource"></a>Skapa en bot-resurs för webb program

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

1. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

1. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robot referens|Resursnamn|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-nodejs-bot-resource-group`.|
    |Plats|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|
    |App Service-plan/plats|Ändra inte från angivet standardvärde.|
    |Application Insights|Ändra inte från angivet standardvärde.|
    |Microsoft app-ID och lösen ord|Ändra inte från angivet standardvärde.|

1. I **robot-mallen** väljer du följande och väljer sedan knappen **Välj** under följande inställningar:

    |Inställning|Syfte|Urval|
    |--|--|--|
    |SDK-språk|Programmeringsspråk för robot|**Node.js**|
    |Bot|Typ av bot|**Basic bot** (Grundläggande robot)|

1. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-nodejs-bot-XXXX`. Namnet baseras på/Azure bot service-appens namn.

    > [!div class="mx-imgBorder"]
    > [![Skapa webb programs bot](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Vänta tills bot-tjänsten har skapats innan du fortsätter.

1. Välj `Go to resource` i meddelandet för att gå till din webb programs robot sida.

## <a name="the-bot-has-a-language-understanding-model"></a>Roboten har en Language Understanding modell

Processen för att skapa bot-tjänsten skapar också en ny LUIS-app med avsikter och exempel yttranden. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter:

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Book Flight|`Travel to Paris`|
|Avbryt|`bye`|
|GetWeather|`what's the weather like?`|
|Inget|Vad som helst utanför appens domän.|

## <a name="test-the-bot-in-web-chat"></a>Testa roboten i Web Chat

1. Medan du fortfarande beAzure Portal för den nya bot väljer du **test i Web Chat**.
1. Skriv texten i text rutan **Skriv ditt meddelande** `Book a flight from Seattle to Berlin tomorrow` . Roboten svarar med verifiering som du vill boka en flygning på.

    ![Skärm bild av Azure Portal anger du texten Hej.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Du kan använda test funktionerna för att snabbt testa din robot. Om du vill ha mer fullständig testning, inklusive fel sökning, laddar du ned bot koden och använder Visual Studio Code.

## <a name="download-the-web-app-bot-source-code"></a>Ladda ned käll koden för webb programs bot
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn.

1. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen.

1. Välj **Download Bot source code** (Ladda ned robotens källkod).

    [![Ladda ned webb program bot-källkod för grundläggande robot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. När popup-dialogrutan frågar **innehåller appinställningar i den hämtade ZIP-filen?** väljer du **Ja**. Detta ger LUIS-inställningarna.

1. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken.

1. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektmappen med Visual Studio Code.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Granska koden för att skicka uttryck till LUIS och hämta svar

1. Om du vill skicka användar uttryck till LUIS förutsägelse slut punkten öppnar du **dialog rutorna-> flightBookingRecognizer.js** filen. Det är här som användaruttrycket som angetts i roboten skickas till LUIS. Svaret från LUIS returneras från **executeLuisQuery** -metoden.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                // Set the recognizer options depending on which endpoint version you want to use e.g v2 or v3.
                // More details can be found in https://docs.microsoft.com/en-gb/azure/cognitive-services/luis/luis-migration-api-v3
                const recognizerOptions = {
                    apiVersion: 'v3'
                };

                this.recognizer = new LuisRecognizer(config, recognizerOptions);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ```

1. **Dialog rutorna – > mainDialog** fångar uttryck och skickar dem till ExecuteLuisQuery i actStep-metoden.

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { MessageFactory, InputHints } = require('botbuilder');
    const { LuisRecognizer } = require('botbuilder-ai');
    const { ComponentDialog, DialogSet, DialogTurnStatus, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');

    const MAIN_WATERFALL_DIALOG = 'mainWaterfallDialog';

    class MainDialog extends ComponentDialog {
        constructor(luisRecognizer, bookingDialog) {
            super('MainDialog');

            if (!luisRecognizer) throw new Error('[MainDialog]: Missing parameter \'luisRecognizer\' is required');
            this.luisRecognizer = luisRecognizer;

            if (!bookingDialog) throw new Error('[MainDialog]: Missing parameter \'bookingDialog\' is required');

            // Define the main dialog and its related components.
            // This is a sample "book a flight" dialog.
            this.addDialog(new TextPrompt('TextPrompt'))
                .addDialog(bookingDialog)
                .addDialog(new WaterfallDialog(MAIN_WATERFALL_DIALOG, [
                    this.introStep.bind(this),
                    this.actStep.bind(this),
                    this.finalStep.bind(this)
                ]));

            this.initialDialogId = MAIN_WATERFALL_DIALOG;
        }

        /**
         * The run method handles the incoming activity (in the form of a TurnContext) and passes it through the dialog system.
         * If no dialog is active, it will start the default dialog.
         * @param {*} turnContext
         * @param {*} accessor
         */
        async run(turnContext, accessor) {
            const dialogSet = new DialogSet(accessor);
            dialogSet.add(this);

            const dialogContext = await dialogSet.createContext(turnContext);
            const results = await dialogContext.continueDialog();
            if (results.status === DialogTurnStatus.empty) {
                await dialogContext.beginDialog(this.id);
            }
        }

        /**
         * First step in the waterfall dialog. Prompts the user for a command.
         * Currently, this expects a booking request, like "book me a flight from Paris to Berlin on march 22"
         * Note that the sample LUIS model will only recognize Paris, Berlin, New York and London as airport cities.
         */
        async introStep(stepContext) {
            if (!this.luisRecognizer.isConfigured) {
                const messageText = 'NOTE: LUIS is not configured. To enable all capabilities, add `LuisAppId`, `LuisAPIKey` and `LuisAPIHostName` to the .env file.';
                await stepContext.context.sendActivity(messageText, null, InputHints.IgnoringInput);
                return await stepContext.next();
            }

            const messageText = stepContext.options.restartMsg ? stepContext.options.restartMsg : 'What can I help you with today?\nSay something like "Book a flight from Paris to Berlin on March 22, 2020"';
            const promptMessage = MessageFactory.text(messageText, messageText, InputHints.ExpectingInput);
            return await stepContext.prompt('TextPrompt', { prompt: promptMessage });
        }

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {
            const bookingDetails = {};

            if (!this.luisRecognizer.isConfigured) {
                // LUIS is not configured, we just run the BookingDialog path.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt)
            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);
            switch (LuisRecognizer.topIntent(luisResult)) {
            case 'BookFlight': {
                // Extract the values for the composite entities from the LUIS result.
                const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                const toEntities = this.luisRecognizer.getToEntities(luisResult);

                // Show a warning for Origin and Destination if we can't resolve them.
                await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);

                // Initialize BookingDetails with any entities we may have found in the response.
                bookingDetails.destination = toEntities.airport;
                bookingDetails.origin = fromEntities.airport;
                bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));

                // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                return await stepContext.beginDialog('bookingDialog', bookingDetails);
            }

            case 'GetWeather': {
                // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                const getWeatherMessageText = 'TODO: get weather flow here';
                await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                break;
            }

            default: {
                // Catch all for unhandled intents
                const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
            }
            }

            return await stepContext.next();
        }

        /**
         * Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
         * In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
         * will be empty if those entity values can't be mapped to a canonical item in the Airport.
         */
        async showWarningForUnsupportedCities(context, fromEntities, toEntities) {
            const unsupportedCities = [];
            if (fromEntities.from && !fromEntities.airport) {
                unsupportedCities.push(fromEntities.from);
            }

            if (toEntities.to && !toEntities.airport) {
                unsupportedCities.push(toEntities.to);
            }

            if (unsupportedCities.length) {
                const messageText = `Sorry but the following airports are not supported: ${ unsupportedCities.join(', ') }`;
                await context.sendActivity(messageText, messageText, InputHints.IgnoringInput);
            }
        }

        /**
         * This is the final step in the main waterfall dialog.
         * It wraps up the sample "book a flight" interaction with a simple confirmation.
         */
        async finalStep(stepContext) {
            // If the child dialog ("bookingDialog") was cancelled or the user failed to confirm, the Result here will be null.
            if (stepContext.result) {
                const result = stepContext.result;
                // Now we have all the booking details.

                // This is where calls to the booking AOU service or database would go.

                // If the call to the booking service was successful tell the user.
                const timeProperty = new TimexProperty(result.travelDate);
                const travelDateMsg = timeProperty.toNaturalLanguage(new Date(Date.now()));
                const msg = `I have you booked to ${ result.destination } from ${ result.origin } on ${ travelDateMsg }.`;
                await stepContext.context.sendActivity(msg, msg, InputHints.IgnoringInput);
            }

            // Restart the main dialog with a different message the second time around
            return await stepContext.replaceDialog(this.initialDialogId, { restartMsg: 'What else can I do for you?' });
        }
    }

    module.exports.MainDialog = MainDialog;
    ```

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="start-the-bot-code"></a>Starta bot-koden

1. Öppna en Windows-konsol eller Linux-eller macOS-Terminal.

1. Ändra till katalogen med bot koden och ange följande kommando för att installera dotenv-beroendet:

    ```console
    npm install dotenv --save
    ```

1. Ange följande kommando för att starta bot-roboten:

    ```console
    node index.js
    ```

Ett webbläsarfönster öppnas med webbappsrobotens webbplats på `http://localhost:3978/`. En start sida visas med information om din robot.

![En start sida visas med information om din robot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-framework-emulator-to-test-the-bot"></a>Använd bot Framework-emulatorn för att testa bot

Ställ en fråga på roboten för att ställa en fråga om bok flyg avsikten.

1. Starta bot Framework-emulatorn och välj **Öppna bot**.
1. Ange din robot-URL i dialog rutan **öppna en robot** -pop, till exempel `http://localhost:3978/api/messages` . `/api/messages`Vägen är webb adressen för bot.
1. Ange **Microsoft app-ID** och **Microsoft app-lösenordet**, som finns i **. kuvert** -filen i roten av den robot kod som du laddade ned.

1. I bot Framework-emulatorn anger `Book a flight from Seattle to Berlin tomorrow` du och får samma svar för den grundläggande roboten som du fick i **testet i Web Chat**.

    [![Skärm bild som visar bot Framework-emulatorn.](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Välj **Ja**. Roboten svarar med en sammanfattning av dess åtgärder.
1. Från loggen för bot Framework-emulatorn väljer du den rad som innehåller `<- trace LuisV3 Trace` . Detta visar JSON-svaret från LUIS för avsikten och entiteten i uttryck.

    [![Skärm bild som visar bot Framework-emulatorn med panelen till höger som innehåller JSON-kod.](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Nästa steg

Se fler [exempel](https://github.com/microsoft/botframework-solutions) med konversationsrobotar.

> [!div class="nextstepaction"]
> [Bygg en Language Understanding-app med en anpassad ämnes domän](./tutorial-intents-only.md)
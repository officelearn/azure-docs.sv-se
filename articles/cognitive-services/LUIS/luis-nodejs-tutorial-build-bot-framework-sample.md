---
title: Integrera THOMAS med en bot med Bot Builder SDK för Node.js i Azure | Microsoft Docs
description: Skapa en bot integrerad med en THOMAS-program med hjälp av ramverket Bot.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/06/2018
ms.author: v-geberr
ms.openlocfilehash: 5d9b78977457f818b964adb16ebb5e9e5872aa2c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264981"
---
# <a name="integrate-luis-with-a-bot-using-the-bot-builder-sdk-for-nodejs"></a>Integrera THOMAS med en bot med Bot Builder SDK för Node.js

Den här självstudiekursen vägleder dig genom att skapa en bot med den [Bot Framework] [ BotFramework] som är integrerad med en THOMAS app.

## <a name="prerequisite"></a>Krav

Innan du skapar bot följer du stegen i [skapa en app](./luis-get-started-create-app.md) att bygga appen THOMAS som används.

Bot svarar avsikter från domänen HomeAutomation i appen THOMAS. För var och en av dessa avsikter ger appen THOMAS syftet som mappar till den. Bot visar en dialogruta som hanterar avsikten THOMAS identifierar.

| Avsikten | Exempel utterance | Bot funktioner |
|:----:|:----------:|---|
| HomeAutomation.TurnOn | Aktivera indikeringar. | Bot anropar den `TurnOnDialog` när den `HomeAutomation.TurnOn` har identifierats. Den här dialogrutan är där du vill anropa en IoT-tjänst för att aktivera en enhet och meddela användaren som enheten har aktiverats. |
| HomeAutomation.TurnOff | Inaktivera sovrum indikeringar. | Bot anropar den `TurnOffDialog` när den `HomeAutomation.TurnOff` har identifierats. Den här dialogrutan där du vill anropa en IoT-tjänsten för att stänga av en enhet och meddela användaren som enheten har inaktiverats. |


## <a name="create-a-language-understanding-bot-with-bot-service"></a>Skapa en språk förstå bot med Bot Service

1. I den [Azure-portalen](https://portal.azure.com)väljer **Skapa ny resurs** i menyn bladet och välj **se alla**.

    ![Skapa ny resurs](./media/luis-tutorial-node-bot/bot-service-creation.png)

2. I sökrutan söker du efter **Web App Bot**. 

    ![Skapa ny resurs](./media/luis-tutorial-node-bot/bot-service-selection.png)

3. I den **Bot Service** bladet, ange nödvändig information och välj **skapa**. Detta skapar och distribuerar bot service och THOMAS app till Azure. Om du vill använda [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming), granska [region krav](luis-resources-faq.md#what-luis-regions-support-bot-framework-speech-priming) innan du skapar din bot. 
    * Ange **appnamn** till din bot namn. Namnet används som underdomänen när din bot distribueras till molnet (exempelvis mynotesbot.azurewebsites.net). <!-- This name is also used as the name of the LUIS app associated with your bot. Copy it to use later, to find the LUIS app associated with the bot. -->
    * Välj prenumerationen [resursgruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview), apptjänstplan, och [plats](https://azure.microsoft.com/regions/).
    * Välj den **språk förstå (Node.js)** mall för den **Bot mallen** fältet.
    * Välj den **THOMAS platsen**. Detta är redigeringen [region] [ LUIS] appen skapas i.
    * Markera kryssrutan bekräftelse för juridiskt meddelande. Villkoren i juridiskt meddelande är lägre än kryssrutan.

    ![Bot Service bladet](./media/luis-tutorial-node-bot/bot-service-setting-callout-template.png)


4. Bekräfta att tjänsten bot har distribuerats.
    * Välj meddelanden (klockikonen finns längs överkanten av Azure portal). Meddelandet kommer att ändras från **distributionen har startat** till **distributionen lyckades**.
    * När meddelandet ändras till **distributionen lyckades**väljer **finns resurs** på detta meddelande.

## <a name="try-the-default-bot"></a>Försök standard bot

Bekräfta att bot har distribuerats genom att kontrollera den **meddelanden**. Meddelanden kommer att ändras från **distribution pågår...**  till **distributionen lyckades**. Välj **finns resurs** knappen för att öppna den bot resurser bladet.

<!-- this step isn't supposed to be necessary -->
## <a name="install-npm-resources"></a>Installera NPM-resurser
Installera NPM-paket med följande steg:

1. Välj **skapa** från den **Bot Management** avsnitt i Web App Bot. 

2. En ny, andra webbläsare öppnas. Välj **öppna online redigerare**.

3. I det övre navigeringsfältet välja bot webbprogramnamnet `homeautomationluisbot`. 

4. I den nedrullningsbara listan **öppna Kudu-konsolen**.

5. Ett nytt fönster i webbläsaren öppnas. Ange följande kommando i konsolen:

    ```
    cd site\wwwroot && npm install
    ```

    Vänta på att installationen ska slutföras. Gå tillbaka till det första webbläsarfönstret. 

## <a name="test-in-web-chat"></a>Testa i webbchatt
När bot registreras, välja **Test i Web chatta** att öppna fönstret Web chatta. Skriv ”hello” i Web chatta.

  ![Testa bot i Web chatt](./media/luis-tutorial-node-bot/bot-service-web-chat.png)

Bot svarar genom att säga ”du har nått hälsning. Du SA: hello ”. Det här bekräftar att bot har tagit emot meddelandet och skickas till en standard THOMAS app som skapats. Den här standardinställningen THOMAS app har upptäckt en hälsning avsikt. I nästa steg ansluts bot till THOMAS appen som du skapade tidigare i stället för default THOMAS app.

## <a name="connect-your-luis-app-to-the-bot"></a>Anslut appen THOMAS till bot

Öppna **programinställningar** i den första webbläsarfönster och redigera den **LuisAppId** fältet ska innehålla program-ID för appen THOMAS.

  ![Uppdatera THOMAS app-ID i Azure](./media/luis-tutorial-node-bot/bot-service-app-id.png)

Om du inte har THOMAS app-ID, logga in på den [THOMAS](luis-reference-regions.md) webbplats som använder samma konto som du använder för att logga in på Azure. Välj på **Mina appar**. 

1. Hitta THOMAS appen du skapade tidigare, som innehåller avsikter och entiteter från domänen HomeAutomation.

2. I den **inställningar** THOMAS appens sida, hitta och kopiera app-ID.

3. Om du inte har tränats appen, Välj den **träna** knappen i det övre högra hörnet för att träna din app.

4. Om du inte har publicerat appen, Välj **publicera** i det övre navigeringsfältet att öppna den **publicera** sidan. Välj produktionsplatsen och **publicera** knappen.

## <a name="modify-the-bot-code"></a>Ändra bot koden

Gå till andra webbläsarfönstret om det är fortfarande öppen eller i det första webbläsarfönstret väljer **skapa** och välj sedan **öppna online redigerare**.

   ![Öppna online redigerare](./media/luis-tutorial-node-bot/bot-service-build.png)

Öppna i Redigeraren för koden `app.js`. Det innehåller följande kod:

```javascript
/*-----------------------------------------------------------------------------
A simple Language Understanding (LUIS) bot for the Microsoft Bot Framework. 
-----------------------------------------------------------------------------*/

var restify = require('restify');
var builder = require('botbuilder');
var botbuilder_azure = require("botbuilder-azure");

// Setup Restify Server
var server = restify.createServer();
server.listen(process.env.port || process.env.PORT || 3978, function () {
   console.log('%s listening to %s', server.name, server.url); 
});
  
// Create chat connector for communicating with the Bot Framework Service
var connector = new builder.ChatConnector({
    appId: process.env.MicrosoftAppId,
    appPassword: process.env.MicrosoftAppPassword,
    openIdMetadata: process.env.BotOpenIdMetadata 
});

// Listen for messages from users 
server.post('/api/messages', connector.listen());

/*----------------------------------------------------------------------------------------
* Bot Storage: This is a great spot to register the private state storage for your bot. 
* We provide adapters for Azure Table, CosmosDb, SQL Azure, or you can implement your own!
* For samples and documentation, see: https://github.com/Microsoft/BotBuilder-Azure
* ---------------------------------------------------------------------------------------- */

var tableName = 'botdata';
var azureTableClient = new botbuilder_azure.AzureTableClient(tableName, process.env['AzureWebJobsStorage']);
var tableStorage = new botbuilder_azure.AzureBotStorage({ gzipData: false }, azureTableClient);

// Create your bot with a function to receive messages from the user
// This default message handler is invoked if the user's utterance doesn't
// match any intents handled by other dialogs.
var bot = new builder.UniversalBot(connector, function (session, args) {
    session.send('You reached the default message handler. You said \'%s\'.', session.message.text);
});

bot.set('storage', tableStorage);

// Make sure you add code to validate these fields
var luisAppId = process.env.LuisAppId;
var luisAPIKey = process.env.LuisAPIKey;
var luisAPIHostName = process.env.LuisAPIHostName || 'westus.api.cognitive.microsoft.com';

const LuisModelUrl = 'https://' + luisAPIHostName + '/luis/v2.0/apps/' + luisAppId + '?subscription-key=' + luisAPIKey;

// Create a recognizer that gets intents from LUIS, and add it to the bot
var recognizer = new builder.LuisRecognizer(LuisModelUrl);
bot.recognizer(recognizer);

// Add a dialog for each intent that the LUIS app recognizes.
// See https://docs.microsoft.com/bot-framework/nodejs/bot-builder-nodejs-recognize-intent-luis 
bot.dialog('GreetingDialog',
    (session) => {
        session.send('You reached the Greeting intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Greeting'
})

bot.dialog('HelpDialog',
    (session) => {
        session.send('You reached the Help intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Help'
})

bot.dialog('CancelDialog',
    (session) => {
        session.send('You reached the Cancel intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'Cancel'
})
```

De befintliga avsikter i app.js ignoreras. Du kan lämna dem. 

## <a name="add-a-dialog-that-matches-homeautomationturnon"></a>Lägg till en dialogruta som matchar HomeAutomation.TurnOn

Kopiera följande kod och lägger till den i `app.js`.

```javascript
bot.dialog('TurnOn',
    (session) => {
        session.send('You reached the TurnOn intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOn'
})
```

Den [matchar] [ matches] alternativet på den [triggerAction] [ triggerAction] kopplade till dialogrutan anger namnet på avsikten. Tolken körs varje gång bot tar emot en utterance från användaren. Om högsta bedömningsprofil avsikten som upptäcks matchar en `triggerAction` bunden till en dialogruta, bot anropar den dialogrutan.

## <a name="add-a-dialog-that-matches-homeautomationturnoff"></a>Lägg till en dialogruta som matchar HomeAutomation.TurnOff

Kopiera följande kod och lägger till den i `app.js`.

```javascript
bot.dialog('TurnOff',
    (session) => {
        session.send('You reached the TurnOff intent. You said \'%s\'.', session.message.text);
        session.endDialog();
    }
).triggerAction({
    matches: 'HomeAutomation.TurnOff'
})
```
## <a name="test-the-bot"></a>Testa bot

I Azure Portal väljer på **testa i Web chatta** att testa bot. Försök skriva meddelanden like ”aktivera indikeringar” och ”inaktivera min värmare” att anropa avsikter som du lade till den.
   ![Testa HomeAutomation bot i Web chatt](./media/luis-tutorial-node-bot/bot-service-chat-results.png)

> [!TIP]
> Om du tycker att din bot inte alltid kan identifiera rätt avsikt eller enheter kan förbättra THOMAS appens prestanda med flera exempel utterances för att träna den. Du kan träna om appen THOMAS utan några andra ändringar till din bot kod. Se [lägger du till exempel utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/add-example-utterances) och [träna och testa appen THOMAS](https://docs.microsoft.com/azure/cognitive-services/LUIS/interactive-test).

## <a name="learn-more-about-bot-framework"></a>Mer information om Bot Framework
Lär dig mer om [Bot Framework](https://dev.botframework.com/) och [3.x](https://github.com/Microsoft/BotBuilder) och [4.x](https://github.com/Microsoft/botbuilder-js) SDK: er.

## <a name="next-steps"></a>Nästa steg

<!-- From trying the bot, you can see that the recognizer can trigger interruption of the currently active dialog. Allowing and handling interruptions is a flexible design that accounts for what users really do. Learn more about the various actions you can associate with a recognized intent.--> Du kan försöka att lägga till andra avsikter som hjälp, Avbryt och hälsning, THOMAS appen. Sedan lägga till dialogrutor för nya avsikter och testa dem med hjälp av bot. 

<!-- 
> [!NOTE] 
> The default LUIS app that the template created contains example utterances for Cancel, Greeting, and Help intents. In the list of apps, find the app that begins with the name specified in **App name** in the **Bot Service** blade when you created the Bot Service. -->

> [!div class="nextstepaction"]
> [Lägg till avsikter](./luis-how-to-add-intents.md)
> [tal promotor](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming)


[intentDialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html

[intentDialog_matches]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.intentdialog.html#matches 

[NotesSample]: https://github.com/Microsoft/BotFramework-Samples/tree/master/docs-samples/Node/basics-naturalLanguage

[triggerAction]: https://docs.botframework.com/en-us/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html#triggeraction

[confirmPrompt]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#confirmprompt

[waterfall]: bot-builder-nodejs-dialog-manage-conversation-flow.md#manage-conversation-flow-with-a-waterfall

[session_userData]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.session.html#userdata

[EntityRecognizer_findEntity]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.entityrecognizer.html#findentity

[matches]: https://docs.botframework.com/en-us/node/builder/chat-reference/interfaces/_botbuilder_d_.itriggeractionoptions.html#matches

[LUISAzureDocs]: https://docs.microsoft.com/azure/cognitive-services/LUIS/Home

[Dialog]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.dialog.html

[IntentRecognizerSetOptions]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.iintentrecognizersetoptions.html

[LuisRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.luisrecognizer

[LUISConcepts]: https://docs.botframework.com/node/builder/guides/understanding-natural-language/

[DisambiguationSample]: https://github.com/Microsoft/BotBuilder/tree/master/Node/examples/feature-onDisambiguateRoute

[IDisambiguateRouteHandler]: https://docs.botframework.com/node/builder/chat-reference/interfaces/_botbuilder_d_.idisambiguateroutehandler.html

[RegExpRecognizer]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.regexprecognizer.html

[AlarmBot]: https://github.com/Microsoft/BotBuilder/blob/master/Node/examples/basics-naturalLanguage/app.js

[LUISBotSample]: https://github.com/Microsoft/BotBuilder-Samples/tree/master/Node/intelligence-LUIS

[UniversalBot]: https://docs.botframework.com/node/builder/chat-reference/classes/_botbuilder_d_.universalbot.html


<!-- Old Links -->
[Github-BotFramework-Emulator-Download]: https://aka.ms/bot-framework-emulator
[Github-LUIS-Samples]: https://github.com/Microsoft/LUIS-Samples
[Github-LUIS-Samples-node-hotel-bot]: https://github.com/Microsoft/LUIS-Samples/tree/master/bot-integration-samples/hotel-finder/nodejs
[NodeJs]: https://nodejs.org/
[BFPortal]: https://dev.botframework.com/
[RegisterInstructions]: https://docs.microsoft.com/bot-framework/portal-register-bot
[BotFramework]: https://docs.microsoft.com/bot-framework/
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions


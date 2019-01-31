---
title: Bot – Node.js – v4
titleSuffix: Azure Cognitive Services
description: Använd Node.js och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här chattroboten använder appen Personalfrågor för att snabbt implementera en robotlösning. Roboten skapas med Bot Framework version 4 och webbappsroboten i Azure.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 292bcf5974f8c51f99a676786c66316e9cde0748
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215925"
---
# <a name="tutorial-luis-bot-in-nodejs-with-the-bot-framework-4x-and-the-azure-web-app-bot"></a>Självstudier: LUIS-bot i Node.js med Bot Framework 4.x och Azure Web App-robot
Med Node.js kan du skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här roboten använder HomeAutomation-appen för att implementera en robotlösning. Roboten bygger på Azures [webbappsrobot](https://docs.microsoft.com/azure/bot-service/) med [Bot Framework version](https://github.com/Microsoft/botbuilder-js) v4.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Lägga till en fördefinierad domän till den nya LUIS-modellen
> * Ladda ned projektet som har skapats av webbrobottjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Ändra robotkoden för nya LUIS-avsikter
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Nödvändiga komponenter

* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-web-app-bot"></a>Skapa en webbappsrobot

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

2. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

3. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-nodejs-bot-resource-group`.|
    |Plats|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, till exempel `luis-nodejs-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|

4. I **Bot template settings** (Inställningar för robotmall) väljer du följande och sedan knappen **Välj** under dessa inställningar:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**Node.js**|
    |Echo/Grundläggande robot|Typ av bot|**Basic bot** (Grundläggande robot)|
    
5. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-nodejs-bot-XXXX`. Det här namnet baseras på robotens och appens namn i föregående avsnitt.

    [ ![Skapa en webbappsrobot](./media/bfv4-nodejs/create-web-app-service.png) ](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

6. Lämna den här webbläsarfliken öppen. Öppna en ny webbläsarflik för steg som utförs på LUIS-portalen. Fortsätt till nästa avsnitt när den nya robottjänsten har distribuerats.

## <a name="add-prebuilt-domain-to-model"></a>Lägga till en fördefinierad domän till modellen
En del av distributionen för robottjänsten skapar en ny LUIS-app med avsikter och exempeluttryck. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter: 

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Avbryt|`stop`|
|Hälsning|`hello`|
|Hjälp|`help`|
|Ingen|Vad som helst utanför appens domän.|

Lägg till den fördefinierade HomeAutomation-appen till modellen för att hantera uttryck som: `Turn off the living room lights`

1. Gå till [LUIS](https://www.luis.ai)-portalen och logga in.
2. På sidan **Mina appar** väljer du kolumnen **Skapad** för att sortera efter det datum då appen skapades. Azure Bot Service skapade en ny app i föregående avsnitt. Appens namn är `luis-nodejs-bot-` + `<your-name>` + 4 slumpmässiga tecken.
3. Öppna appen och välj avsnittet **Skapa** i det övre navigeringsfältet.
4. I det vänstra navigeringsfönstret väljer du **Prebuilt Domains** (Fördefinierade domäner).
5. Välj domänen **HomeAutomation** genom att välja **Lägg till domän** på dess kort.
6. Välj **Träna** på menyn längst upp till höger.
7. Välj **Publicera** på menyn längst upp till höger. 

    Nu har appen som skapats av Azure Bot Service nya avsikter:

    |Nya avsikter för grundläggande robot|Exempeluttryck|
    |--|--|
    |HomeAutomation.TurnOn|`turn the fan to high`
    |HomeAutomation.TurnOff|`turn off ac please`|

## <a name="download-the-web-app-bot"></a>Ladda ned webbappsroboten 
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn. 

1. Välj **Programinställningar** på resurssidan för webbappsroboten på Azure-portalen och kopiera värdena för **botFilePath** och **botFileSecret**. Du måste lägga till dem i en miljöfil senare. 

2. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen. 

3. Välj **Download Bot source code** (Ladda ned robotens källkod). 

    [ ![Ladda ned källkoden för webbappsroboten för grundläggande robot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png) ](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

4. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken. 

5. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektet. 

6. Öppna filen bot.js och leta efter `const results = await this.luisRecognizer.recognize(context);`. Det är här som användaruttrycket som angetts i roboten skickas till LUIS.

    ```nodejs
    /**
     * Driver code that does one of the following:
     * 1. Display a welcome card upon startup
     * 2. Use LUIS to recognize intents
     * 3. Start a greeting dialog
     * 4. Optionally handle Cancel or Help interruptions
     *
     * @param {Context} context turn context from the adapter
     */
    async onTurn(context) {
        // Create a dialog context
        const dc = await this.dialogs.createContext(context);

        if(context.activity.type === ActivityTypes.Message) {
            // Perform a call to LUIS to retrieve results for the current activity message.
            const results = await this.luisRecognizer.recognize(context);
            
            const topIntent = LuisRecognizer.topIntent(results);

            // handle conversation interrupts first
            const interrupted = await this.isTurnInterrupted(dc, results);
            if(interrupted) {
                return;
            }

            // Continue the current dialog
            const dialogResult = await dc.continue();

            switch(dialogResult.status) {
                case DialogTurnStatus.empty:
                    switch (topIntent) {
                        case GREETING_INTENT:
                            await dc.begin(GREETING_DIALOG);
                            break;

                        case NONE_INTENT:
                        default:
                            // help or no intent identified, either way, let's provide some help
                            // to the user
                            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
                            break;
                    }

                case DialogTurnStatus.waiting:
                    // The active dialog is waiting for a response from the user, so do nothing
                break;

                case DialogTurnStatus.complete:
                    await dc.end();
                    break;

                default:
                    await dc.cancelAll();
                    break;

            }

        } else if (context.activity.type === 'conversationUpdate' && context.activity.membersAdded[0].name === 'Bot') {
            // When activity type is "conversationUpdate" and the member joining the conversation is the bot
            // we will send our Welcome Adaptive Card.  This will only be sent once, when the Bot joins conversation
            // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
            const welcomeCard = CardFactory.adaptiveCard(WelcomeCard);
            await context.sendActivity({ attachments: [welcomeCard] });
        }
    }
    ```

    Roboten skickar användarens uttryck till LUIS och hämtar resultaten. Den främsta avsikten avgör konversationsflödet. 


## <a name="start-the-bot"></a>Starta roboten
Kontrollera att roboten fungerar innan du ändrar koden eller några inställningar. 

1. Öppna ett terminalfönster i Visual Studio Code. 

2. Installera npm-beroendena för den här roboten. 

    ```bash
    npm install
    ```
3. Skapa en fil som ska innehålla miljövariablerna som robotkoden söker efter. Ge filen namnet `.env`. Lägg till följande miljövariabler:

    <!--there is no code language that represents an .env file correctly-->
    ```env
    botFilePath=
    botFileSecret=
    ```

    Ange värdena för miljövariablerna till de värden som du kopierade från Programinställningar i Azure Bot Service i steg 1 i avsnittet **[Ladda ned webbappsroboten](#download-the-web-app-bot)**.

4. Starta roboten i bevakningsläge. De ändringar du gör i koden från och med nu leder till en automatisk omstart av appen.

    ```bash
    npm run watch
    ```

5. När roboten startas visar terminalfönstret den lokala porten som roboten körs på:

    ```console
    > basic-bot@0.1.0 start C:\Users\pattiowens\repos\BFv4\luis-nodejs-bot-src
    > node ./index.js NODE_ENV=development

    restify listening to http://[::]:3978
    
    Get the Emulator: https://aka.ms/botframework-emulator
    
    To talk to your bot, open the luis-nodejs-bot-pattiowens.bot file in the Emulator
    ```

## <a name="start-the-emulator"></a>Starta emulatorn

1. Starta robotemulatorn. 

2. I robotemulatorn väljer du *.bot-filen i roten av projektet. Den här `.bot`-filen innehåller robotens URL-slutpunkt för meddelanden:

    [ ![Robotemulatorn v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Ange den robothemlighet som du kopierade från Programinställningar i Azure Bot Service i steg 1 i avsnittet **[Ladda ned webbappsroboten](#download-the-web-app-bot)**. På så sätt kan emulatorn få åtkomst till alla krypterade fält i .bot-filen.

    ![Hemlighet för robotemulatorn v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)


4. Ange `Hello` i robotemulatorn och få lämpligt svar för den grundläggande roboten.

    [ ![Svar från grundläggande robot i emulatorn](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Ändra robotkoden 

Lägg till kod i `bot.js`-filen för att hantera de nya avsikterna. 

1. Leta upp avsnittet **Supported LUIS Intents** (LUIS-avsikter som stöds) längst upp i filen och lägg till konstanter för HomeAutomation-avsikterna:

    ```nodejs
    // Supported LUIS Intents
    const GREETING_INTENT = 'Greeting';
    const CANCEL_INTENT = 'Cancel';
    const HELP_INTENT = 'Help';
    const NONE_INTENT = 'None';
    const TURNON_INTENT = 'HomeAutomation_TurnOn'; // new intent
    const TURNOFF_INTENT = 'HomeAutomation_TurnOff'; // new intent
    ```

    Observera att perioden, `.`, mellan domänen och avsikten från LUIS-portalens app ersätts med ett understreck, `_`. 

2. Leta upp det **isTurnInterrupted** som tar emot LUIS-förutsägelsen av uttrycket och lägg till en rad för att skriva resultatet till konsolen.

    ```nodejs
    /**
     * Look at the LUIS results and determine if we need to handle
     * an interruptions due to a Help or Cancel intent
     *
     * @param {DialogContext} dc - dialog context
     * @param {LuisResults} luisResults - LUIS recognizer results
     */
    async isTurnInterrupted(dc, luisResults) {
        console.log(JSON.stringify(luisResults));
    ...
    ```

    Roboten har inte exakt samma svar som en LUIS REST API-begäran så det är viktigt att du lär dig skillnaderna genom att titta på JSON-svarskoden. Egenskaperna för text och avsikter är samma men egenskapsvärdena för entiteter har ändrats. 

    ```json
    {
        "$instance": {
            "HomeAutomation_Device": [
                {
                    "startIndex": 23,
                    "endIndex": 29,
                    "score": 0.9776345,
                    "text": "lights",
                    "type": "HomeAutomation.Device"
                }
            ],
            "HomeAutomation_Room": [
                {
                    "startIndex": 12,
                    "endIndex": 22,
                    "score": 0.9079433,
                    "text": "livingroom",
                    "type": "HomeAutomation.Room"
                }
            ]
        },
        "HomeAutomation_Device": [
            "lights"
        ],
        "HomeAutomation_Room": [
            "livingroom"
        ]
    }
    ```

3. Lägg till avsikterna till onTurn-metodens växelinstruktion för `DialogTurnStatus.empty`-fallet:

    ```nodejs
    switch (topIntent) {
        case GREETING_INTENT:
            await dc.begin(GREETING_DIALOG);
            break;

        // New HomeAutomation.TurnOn intent
        case TURNON_INTENT: 

            await dc.context.sendActivity(`TurnOn intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        // New HomeAutomation.TurnOff intent
        case TURNOFF_INTENT: 

            await dc.context.sendActivity(`TurnOff intent found, entities included: ${JSON.stringify(results.entities)}`);
            break;

        case NONE_INTENT:
        default:
            // help or no intent identified, either way, let's provide some help
            // to the user
            await dc.context.sendActivity(`I didn't understand what you just said to me. topIntent ${topIntent}`);
            break;
    }
    ```

## <a name="view-results-in-bot"></a>Visa resultatet i roboten

1. I robotemulatorn anger du uttrycket: `Turn on the livingroom lights to 50%`

2. Roboten svarar med:

    ```json
    TurnOn intent found, entities included: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```

## <a name="learn-more-about-bot-framework"></a>Läs mer om Bot Framework
Azure Bot Service använder Bot Framework SDK. Lär dig mer om SDK och Bot Framework:

* Dokumentation om [Azure Bot Service](https://docs.microsoft.com/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4
* [Bot Builder-exempel](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder-verktyg](https://github.com/Microsoft/botbuilder-tools):

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure-robottjänst, kopierade robothemligheten och .bot-filsökvägen och laddade ned ZIP-filen för koden. Du lade till den fördefinierade HomeAutomation-domänen till LUIS-appen som skapades som en del av den nya Azure-robottjänsten och tränade appen och publicerade den sedan igen. Du extraherade kodprojektet, skapade en miljöfil (`.env`) och definierade robothemligheten och .bot-filsökvägen. I filen bot.js lade du till kod för att hantera de två nya avsikterna. Sedan testade du roboten i robotemulatorn för att se LUIS-svaret för ett uttryck för en av de nya avsikterna. 


> [!div class="nextstepaction"]
> [Skapa en anpassad domän i LUIS](luis-quickstart-intents-only.md)

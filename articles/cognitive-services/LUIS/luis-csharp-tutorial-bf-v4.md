---
title: LUIS-robot med C# – Självstudie – Webbappsrobot – Bot Framework SDK 4.0
titleSuffix: Azure Cognitive Services
description: Använd C# och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här chattroboten använder appen Personalfrågor för att snabbt implementera en robotlösning. Roboten skapas med Bot Framework version 4 och webbappsroboten i Azure.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/25/2018
ms.author: diberry
ms.openlocfilehash: f8350d46fecff726dd9f591fe3df0272f556b3e7
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2018
ms.locfileid: "47168221"
---
# <a name="tutorial-luis-bot-in-c"></a>Självstudie: LUIS-robot i C#
Använd C# och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Den här roboten använder HomeAutomation-appen för att implementera en robotlösning. Roboten bygger på Azures [webbappsrobot](https://docs.microsoft.com/azure/bot-service/) med [Bot Framework version](https://github.com/Microsoft/botbuilder-js) v4.

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
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-web-app-bot"></a>Skapa en webbappsrobot

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

2. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

3. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-csharp-bot-resource-group`.|
    |Plats|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|

4. I **Bot template settings** (Inställningar för robotmall) väljer du följande och sedan knappen **Välj** under dessa inställningar:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**C#**|
    |Echo/Grundläggande robot|Typ av bot|**Basic bot** (Grundläggande robot)|
    
5. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-csharp-bot-XXXX`. Det här namnet baseras på robotens och appens namn i föregående avsnitt.

    [ ![Skapa en webbappsrobot](./media/bfv4-csharp/create-web-app-service.png) ](./media/bfv4-csharp/create-web-app-service.png#lightbox)

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
2. På sidan **Mina appar** väljer du kolumnen **Skapad** för att sortera efter det datum då appen skapades. Azure Bot Service skapade en ny app i föregående avsnitt. Appens namn är `luis-csharp-bot-` + `<your-name>` + 4 slumpmässiga tecken.
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

6. Öppna filen bot.cs och leta efter `_services.LuisServices`. Det är här som användaruttrycket som angetts i roboten skickas till LUIS.

    ```csharp
    /// <summary>
    /// Run every turn of the conversation. Handles orchestration of messages.
    /// </summary>
    /// <param name="turnContext">Bot Turn Context.</param>
    /// <param name="cancellationToken">Task CancellationToken.</param>
    /// <returns>A <see cref="Task"/> representing the asynchronous operation.</returns>
    public async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    {
        var activity = turnContext.Activity;

        if (activity.Type == ActivityTypes.Message)
        {
            // Perform a call to LUIS to retrieve results for the current activity message.
            var luisResults = await _services.LuisServices[LuisConfiguration].RecognizeAsync(turnContext, cancellationToken).ConfigureAwait(false);

            // If any entities were updated, treat as interruption.
            // For example, "no my name is tony" will manifest as an update of the name to be "tony".
            var topScoringIntent = luisResults?.GetTopScoringIntent();

            var topIntent = topScoringIntent.Value.intent;
            switch (topIntent)
            {
                case GreetingIntent:
                    await turnContext.SendActivityAsync("Hello.");
                    break;
                case HelpIntent:
                    await turnContext.SendActivityAsync("Let me try to provide some help.");
                    await turnContext.SendActivityAsync("I understand greetings, being asked for help, or being asked to cancel what I am doing.");
                    break;
                case CancelIntent:
                    await turnContext.SendActivityAsync("I have nothing to cancel.");
                    break;
                case NoneIntent:
                default:
                    // Help or no intent identified, either way, let's provide some help.
                    // to the user
                    await turnContext.SendActivityAsync("I didn't understand what you just said to me.");
                    break;
            }
        }
        else if (activity.Type == ActivityTypes.ConversationUpdate)
        {
            if (activity.MembersAdded.Any())
            {
                // Iterate over all new members added to the conversation.
                foreach (var member in activity.MembersAdded)
                {
                    // Greet anyone that was not the target (recipient) of this message.
                    // To learn more about Adaptive Cards, see https://aka.ms/msbot-adaptivecards for more details.
                    if (member.Id != activity.Recipient.Id)
                    {
                        var welcomeCard = CreateAdaptiveCardAttachment();
                        var response = CreateResponse(activity, welcomeCard);
                        await turnContext.SendActivityAsync(response).ConfigureAwait(false);
                    }
                }
            }
        }

    }
    ```

    Roboten skickar användarens uttryck till LUIS och hämtar resultaten. Den främsta avsikten avgör konversationsflödet. 


## <a name="start-the-bot"></a>Starta roboten
Kontrollera att roboten fungerar innan du ändrar koden eller några inställningar. 

1. Öppna lösningsfilen i Visual Studio. 

2. Skapa en `appsettings.json`-fil som ska innehålla robotvariablerna som robotkoden söker efter:

    ```JSON
    {
    "botFileSecret": "",
    "botFilePath": ""

    }
    ```

    Ange värdena för variablerna till de värden som du kopierade från Programinställningar i Azure Bot Service i steg 1 i avsnittet **[Ladda ned webbappsroboten](#download-the-web-app-bot)**.

3. Starta roboten i Visual Studio. Ett webbläsarfönster öppnas med webbappsrobotens webbplats på `http://localhost:3978/`.

## <a name="start-the-emulator"></a>Starta emulatorn

1. Starta robotemulatorn.

2. I robotemulatorn väljer du *.bot-filen i roten av projektet. Den här `.bot`-filen innehåller robotens URL-slutpunkt för meddelanden:

    [ ![Robotemulatorn v4](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png) ](../../../includes/media/cognitive-services-luis/bfv4/bot-emulator-v4.png#lightbox)

3. Ange den robothemlighet som du kopierade från Programinställningar i Azure Bot Service i steg 1 i avsnittet **[Ladda ned webbappsroboten](#download-the-web-app-bot)**. På så sätt kan emulatorn få åtkomst till alla krypterade fält i `.bot`-filen.

    ![Hemlighet för robotemulatorn v4](../../../includes/media/cognitive-services-luis/bfv4/bot-secret.png)

4. Ange `Hello` i robotemulatorn och få lämpligt svar för den grundläggande roboten.

    [ ![Svar från grundläggande robot i emulatorn](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png) ](../../../includes/media/cognitive-services-luis/bfv4/emulator-test.png#lightbox)

## <a name="modify-bot-code"></a>Ändra robotkoden 

Lägg till kod i `BasicBot.cs`-filen för att hantera de nya avsikterna. 

1. Leta upp avsnittet **Supported LUIS Intents** (LUIS-avsikter som stöds) längst upp i filen och lägg till konstanter för HomeAutomation-avsikterna:

    ```csharp
    // Supported LUIS Intents
    public const string GreetingIntent = "Greeting";
    public const string CancelIntent = "Cancel";
    public const string HelpIntent = "Help";
    public const string NoneIntent = "None";
    public const string TurnOnIntent = "HomeAutomation_TurnOn"; // new intent
    public const string TurnOffIntent = "HomeAutomation_TurnOff"; // new intent
    ```

    Observera att perioden, `.`, mellan domänen och avsikten från LUIS-portalens app ersätts med ett understreck, `_`. 

2. Leta upp **OnTurnAsync**-metoden som tar emot LUIS-förutsägelsen för uttrycket. Lägg till kod i växelinstruktionen för att returnera LUIS-svaret för de två HomeAutomation-avsikterna. 

    ```csharp
    case TurnOnIntent:
        await turnContext.SendActivityAsync("TurnOn intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    case TurnOffIntent:
        await turnContext.SendActivityAsync("TurnOff intent found, JSON response: " + luisResults?.Entities.ToString());
        break;
    ```

    Roboten har inte exakt samma svar som en LUIS REST API-begäran så det är viktigt att du lär dig skillnaderna genom att titta på JSON-svarskoden. Egenskaperna för text och avsikter är samma men egenskapsvärdena för entiteter har ändrats. 

    ```JSON
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



## <a name="view-results-in-bot"></a>Visa resultatet i roboten

1. I robotemulatorn anger du uttrycket: `Turn on the livingroom lights to 50%`

2. Roboten svarar med:

    ```JSON
    TurnOn intent found, JSON response: {"$instance":{“HomeAutomation_Device”:[{“startIndex”:23,“endIndex”:29,“score”:0.9776345,“text”:“lights”,“type”:“HomeAutomation.Device”}],“HomeAutomation_Room”:[{“startIndex”:12,“endIndex”:22,“score”:0.9079433,“text”:“livingroom”,“type”:“HomeAutomation.Room”}]},“HomeAutomation_Device”:[“lights”],“HomeAutomation_Room”:[“livingroom”]}
    ```    ```

## Learn more about Bot Framework
Azure Bot service uses the Bot Framework SDK. Learn more about the SDK and bot framework:

* [Azure Bot Service](https://docs.microsoft.com/en-us/azure/bot-service/bot-service-overview-introduction?view=azure-bot-service-4.0) v4 documentation
* [Bot Builder Samples](https://github.com/Microsoft/botbuilder-samples)
* [Bot Builder SDK](https://docs.microsoft.com/en-us/javascript/api/botbuilder-core/?view=botbuilder-ts-latest)
* [Bot Builder tools](https://github.com/Microsoft/botbuilder-tools):

## Next steps

You created an Azure bot service, copied the bot secret and `.bot` file path, downloaded the zip file of the code. You added the prebuilt HomeAutomation domain to the LUIS app created as part of the new Azure bot service, then trained and published the app again. You extracted the code project, created an environment file (`.env`), and set the bot secret and the `.bot` file path. In the bot.js file, you added code to handle the two new intents. Then you tested the bot in the bot emulator to see the LUIS response for an utterance of one of the new intents. 


> [!div class="nextstepaction"]
> [Build a custom domain in LUIS](luis-quickstart-intents-only.md)

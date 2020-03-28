---
title: 'Handledning: Språk understanding Bot C# v4'
description: Använd C# och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Roboten är byggd med Bot Framework version 4 och Azure Web App bot-tjänsten.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 2381f4cba39f81ab593f3149a2708f442156f30d
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76988012"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Självstudiekurs: Använd en webbapprobot aktiverad med språk understanding i C #

Använd C# för att skapa en chattrobot integrerad med språk understanding (LUIS). Roboten är byggd med Azure [Web App bot](https://docs.microsoft.com/azure/bot-service/) resurs och Bot Framework [version](https://github.com/Microsoft/botbuilder-dotnet) V4.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Ladda ner bot-projektet som skapats av webbrobottjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Krav

* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Skapa en botresurs för webbappar

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

1. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

1. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Prenumeration|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resursgrupp|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-csharp-bot-resource-group`.|
    |Location|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|
    |App serviceplan/plats|Ändra inte från det angivna standardvärdet.|
    |Application Insights|Ändra inte från det angivna standardvärdet.|
    |Microsoft-app-ID och lösenord|Ändra inte från det angivna standardvärdet.|

1. I **Bot-mallen**väljer du följande och väljer sedan knappen **Välj** under följande inställningar:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**C#**|
    |Bot|Typ av bot|**Basic bot** (Grundläggande robot)|

1. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-csharp-bot-XXXX`. Det här namnet baseras på namnet på /Azure Bot Service-appen.

    > [!div class="mx-imgBorder"]
    > [![Skapa webbapprobot](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Vänta tills bot-tjänsten skapas innan du fortsätter.

## <a name="the-bot-has-a-language-understanding-model"></a>Boten har en språk understanding-modell

Bot-tjänsten skapar också en ny LUIS-app med avsikter och exempelyttranden. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter:

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Boka flyg|`Travel to Paris`|
|Avbryt|`bye`|
|GetWeather|`what's the weather like?`|
|Inget|Vad som helst utanför appens domän.|

## <a name="test-the-bot-in-web-chat"></a>Testa boten i webbchatt

1. Medan du fortfarande är i Azure-portalen för den nya roboten väljer du **Testa i webbchatt**.
1. Skriv texten i **textrutan Skriv** `Book a flight from Seattle to Berlin tomorrow`meddelandet . Roboten svarar med en verifiering att du vill boka ett flyg.

    ![Skärmbild av Azure-portalen anger du texten "hello".](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    Du kan använda testfunktionen för att snabbt testa din robot. För mer komplett testning, inklusive felsökning, ladda ner botkoden och använd Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Ladda ner källkoden för webbapproken
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn.

1. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen.

1. Välj **Download Bot source code** (Ladda ned robotens källkod).

    [![Ladda ner webbapp bot källkod för grundläggande bot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. När popup-dialogrutan frågar **Inkludera appinställningar i den nedladdade zip-filen?** väljer du **Ja**.

1. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken.

1. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektet med Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Granska kod för att skicka yttrande till LUIS och få svar

1. Om du vill skicka användarens uttryck till slutpunkten LUIS-förutsägelse öppnar **du FlightBookingRecognizer.cs** filen. Det är här som användaruttrycket som angetts i roboten skickas till LUIS. Svaret från LUIS returneras från **metoden RecognizeAsync.**

    ```csharp
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;

    namespace Microsoft.BotBuilderSamples
    {
        public class FlightBookingRecognizer : IRecognizer
        {
            private readonly LuisRecognizer _recognizer;

            public FlightBookingRecognizer(IConfiguration configuration)
            {
                var luisIsConfigured = !string.IsNullOrEmpty(configuration["LuisAppId"]) && !string.IsNullOrEmpty(configuration["LuisAPIKey"]) && !string.IsNullOrEmpty(configuration["LuisAPIHostName"]);
                if (luisIsConfigured)
                {
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]);

                    _recognizer = new LuisRecognizer(luisApplication);
                }
            }

            // Returns true if luis is configured in the appsettings.json and initialized.
            public virtual bool IsConfigured => _recognizer != null;

            public virtual async Task<RecognizerResult> RecognizeAsync(ITurnContext turnContext, CancellationToken cancellationToken)
                => await _recognizer.RecognizeAsync(turnContext, cancellationToken);

            public virtual async Task<T> RecognizeAsync<T>(ITurnContext turnContext, CancellationToken cancellationToken)
                where T : IRecognizerConvert, new()
                => await _recognizer.RecognizeAsync<T>(turnContext, cancellationToken);
        }
    }
    ````

1. Öppna **Dialogrutor -> MainDialog.cs** fångar uttrycket och skickar det till executeLuisQuery i metoden actStep.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Bot.Schema;
    using Microsoft.Extensions.Logging;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;

    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class MainDialog : ComponentDialog
        {
            private readonly FlightBookingRecognizer _luisRecognizer;
            protected readonly ILogger Logger;

            // Dependency injection uses this constructor to instantiate MainDialog
            public MainDialog(FlightBookingRecognizer luisRecognizer, BookingDialog bookingDialog, ILogger<MainDialog> logger)
                : base(nameof(MainDialog))
            {
                _luisRecognizer = luisRecognizer;
                Logger = logger;

                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(bookingDialog);
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    IntroStepAsync,
                    ActStepAsync,
                    FinalStepAsync,
                }));

                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }

            private async Task<DialogTurnResult> IntroStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    await stepContext.Context.SendActivityAsync(
                        MessageFactory.Text("NOTE: LUIS is not configured. To enable all capabilities, add 'LuisAppId', 'LuisAPIKey' and 'LuisAPIHostName' to the appsettings.json file.", inputHint: InputHints.IgnoringInput), cancellationToken);

                    return await stepContext.NextAsync(null, cancellationToken);
                }

                // Use the text provided in FinalStepAsync or the default if it is the first time.
                var messageText = stepContext.Options?.ToString() ?? "What can I help you with today?\nSay something like \"Book a flight from Paris to Berlin on March 22, 2020\"";
                var promptMessage = MessageFactory.Text(messageText, messageText, InputHints.ExpectingInput);
                return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = promptMessage }, cancellationToken);
            }

            private async Task<DialogTurnResult> ActStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if (!_luisRecognizer.IsConfigured)
                {
                    // LUIS is not configured, we just run the BookingDialog path with an empty BookingDetailsInstance.
                    return await stepContext.BeginDialogAsync(nameof(BookingDialog), new BookingDetails(), cancellationToken);
                }

                // Call LUIS and gather any potential booking details. (Note the TurnContext has the response to the prompt.)
                var luisResult = await _luisRecognizer.RecognizeAsync<FlightBooking>(stepContext.Context, cancellationToken);
                switch (luisResult.TopIntent().intent)
                {
                    case FlightBooking.Intent.BookFlight:
                        await ShowWarningForUnsupportedCities(stepContext.Context, luisResult, cancellationToken);

                        // Initialize BookingDetails with any entities we may have found in the response.
                        var bookingDetails = new BookingDetails()
                        {
                            // Get destination and origin from the composite entities arrays.
                            Destination = luisResult.ToEntities.Airport,
                            Origin = luisResult.FromEntities.Airport,
                            TravelDate = luisResult.TravelDate,
                        };

                        // Run the BookingDialog giving it whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.BeginDialogAsync(nameof(BookingDialog), bookingDetails, cancellationToken);

                    case FlightBooking.Intent.GetWeather:
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        var getWeatherMessageText = "TODO: get weather flow here";
                        var getWeatherMessage = MessageFactory.Text(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(getWeatherMessage, cancellationToken);
                        break;

                    default:
                        // Catch all for unhandled intents
                        var didntUnderstandMessageText = $"Sorry, I didn't get that. Please try asking in a different way (intent was {luisResult.TopIntent().intent})";
                        var didntUnderstandMessage = MessageFactory.Text(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                        await stepContext.Context.SendActivityAsync(didntUnderstandMessage, cancellationToken);
                        break;
                }

                return await stepContext.NextAsync(null, cancellationToken);
            }

            // Shows a warning if the requested From or To cities are recognized as entities but they are not in the Airport entity list.
            // In some cases LUIS will recognize the From and To composite entities as a valid cities but the From and To Airport values
            // will be empty if those entity values can't be mapped to a canonical item in the Airport.
            private static async Task ShowWarningForUnsupportedCities(ITurnContext context, FlightBooking luisResult, CancellationToken cancellationToken)
            {
                var unsupportedCities = new List<string>();

                var fromEntities = luisResult.FromEntities;
                if (!string.IsNullOrEmpty(fromEntities.From) && string.IsNullOrEmpty(fromEntities.Airport))
                {
                    unsupportedCities.Add(fromEntities.From);
                }

                var toEntities = luisResult.ToEntities;
                if (!string.IsNullOrEmpty(toEntities.To) && string.IsNullOrEmpty(toEntities.Airport))
                {
                    unsupportedCities.Add(toEntities.To);
                }

                if (unsupportedCities.Any())
                {
                    var messageText = $"Sorry but the following airports are not supported: {string.Join(',', unsupportedCities)}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await context.SendActivityAsync(message, cancellationToken);
                }
            }

            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                // If the child dialog ("BookingDialog") was cancelled, the user failed to confirm or if the intent wasn't BookFlight
                // the Result here will be null.
                if (stepContext.Result is BookingDetails result)
                {
                    // Now we have all the booking details call the booking service.

                    // If the call to the booking service was successful tell the user.

                    var timeProperty = new TimexProperty(result.TravelDate);
                    var travelDateMsg = timeProperty.ToNaturalLanguage(DateTime.Now);
                    var messageText = $"I have you booked to {result.Destination} from {result.Origin} on {travelDateMsg}";
                    var message = MessageFactory.Text(messageText, messageText, InputHints.IgnoringInput);
                    await stepContext.Context.SendActivityAsync(message, cancellationToken);
                }

                // Restart the main dialog with a different message the second time around
                var promptMessage = "What else can I do for you?";
                return await stepContext.ReplaceDialogAsync(InitialDialogId, promptMessage, cancellationToken);
            }
        }
    }
    ```

## <a name="start-the-bot-code-in-visual-studio"></a>Starta botkoden i Visual Studio

Starta roboten i Visual Studio 2019. Ett webbläsarfönster öppnas med webbappsrobotens webbplats på `http://localhost:3978/`. En hemsida visas med information om din bot.

![En hemsida visas med information om din bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Använd bot emulator för att testa bot

1. Börja Bot Emulator och välj **Öppna Bot**.
1. I popup-dialogrutan **Öppna en robot** anger du din `http://localhost:3978/api/messages`bot-URL, till exempel . Rutten `/api/messages` är webbadressen för roboten.
1. Ange **lösenordet för Microsoft-app-ID** och **Microsoft App**som finns i filen **appsettings.json** i roten till den hämtade robotkoden.


1. I bot emulator, `Book a flight from Seattle to Berlin tomorrow` ange och få samma svar för den grundläggande bot som du fick i **Test i webbchatt** i ett tidigare avsnitt.

    [![Grundläggande bot svar i emulatorn](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Välj **Ja**. Roboten svarar med en sammanfattning av sina åtgärder.
1. Välj den rad som innehåller `Luis Trace`. Detta visar JSON-svaret från LUIS för avsikten och entiteterna för uttryck.

    [![Grundläggande bot svar i emulatorn](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]


## <a name="next-steps"></a>Nästa steg

Se fler [exempel](https://github.com/microsoft/botframework-solutions) med konversationsrobotar.

> [!div class="nextstepaction"]
> [Skapa en språk understanding-app med en anpassad ämnesdomän](luis-quickstart-intents-only.md)

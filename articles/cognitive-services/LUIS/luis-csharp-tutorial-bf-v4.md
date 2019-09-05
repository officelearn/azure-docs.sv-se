---
title: 'Självstudier: Language Understanding bot C# v4'
titleSuffix: Azure Cognitive Services
description: Använd C# och skapa en chattrobot som är integrerad med språkförståelse (LUIS). Roboten har skapats med bot Framework version 4 och Azure Web App bot-tjänsten.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 79f75fbae9a13db985f46408145c7d6762ff56b9
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375588"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Självstudier: Använda en Web App-robot som är aktive rad med Language Understanding iC#

Använd C# för att bygga en chatt-robot integrerad med språk förståelse (Luis). Roboten är byggd med Azure [Web App bot-](https://docs.microsoft.com/azure/bot-service/) resursen och [bot Framework version](https://github.com/Microsoft/botbuilder-dotnet) v4.

**I den här självstudiekursen får du lära du dig att:**

> [!div class="checklist"]
> * Skapa en webbappsrobot. Den här processen skapar en ny LUIS-app.
> * Hämta bot-projektet som skapats av Web bot-tjänsten
> * Starta roboten och emulatorn lokalt på datorn
> * Visa uttrycksresultat i roboten

## <a name="prerequisites"></a>Förutsättningar

* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Skapa en bot-resurs för webb program

1. På [Azure-portalen](https://portal.azure.com) väljer du **Skapa ny resurs**.

1. I sökrutan söker du efter och väljer **Web App Bot** (Webbappsrobot). Välj **Skapa**.

1. Ange nödvändig information i **Robottjänst**:

    |Inställning|Syfte|Rekommenderad inställning|
    |--|--|--|
    |Robotnamn|Resursnamn|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Subscription|Prenumeration där roboten ska skapas.|Din primära prenumeration.
    |Resource group|Logisk grupp med Azure-resurser|Skapa en ny grupp för att lagra alla resurser som används med den här roboten och ge gruppen namnet `luis-csharp-bot-resource-group`.|
    |Location|Azure-region – Det här behöver inte vara samma som redigerings- eller publiceringsregionen för LUIS.|`westus`|
    |Prisnivå|Används för begränsningar och fakturering av tjänstbegäranden.|`F0` är den kostnadsfria nivån.
    |Appnamn|Namnet används som underdomänen när din robot distribueras till molnet (exempelvis humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, till exempel `luis-csharp-bot-johnsmith`|
    |Robotmall|Bot Framework-inställningar – se nästa tabell|
    |LUIS-appens plats|Måste vara samma som LUIS-resursregionen|`westus`|
    |App Service-plan/plats|Ändra inte från angivet standardvärde.|
    |Application Insights|Ändra inte från angivet standardvärde.|
    |Microsoft app-ID och lösen ord|Ändra inte från angivet standardvärde.|

1. I **robot-mallen**väljer du följande och väljer sedan knappen **Välj** under följande inställningar:

    |Inställning|Syfte|Val|
    |--|--|--|
    |SDK-version|Bot Framework-version|**SDK v4**|
    |SDK-språk|Programmeringsspråk för robot|**C#**|
    |Bot|Typ av bot|**Basic bot** (Grundläggande robot)|
    
1. Välj **Skapa**. Nu skapas och distribueras robottjänsten till Azure. I en del av den här processen skapas en LUIS-app med namnet `luis-csharp-bot-XXXX`. Namnet baseras på/Azure bot service-appens namn.

    [![Skapa en webbappsrobot](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Vänta tills bot-tjänsten har skapats innan du fortsätter.

## <a name="the-bot-has-a-language-understanding-model"></a>Roboten har en Language Understanding modell

Processen för att skapa bot-tjänsten skapar också en ny LUIS-app med avsikter och exempel yttranden. Roboten tillhandahåller avsiktsmappning till den nya LUIS-appen för följande avsikter: 

|LUIS-avsikter för grundläggande robot|Exempeluttryck|
|--|--|
|Book Flight|`Travel to Paris`|
|Avbryt|`bye`|
|Inga|Vad som helst utanför appens domän.|

## <a name="test-the-bot-in-web-chat"></a>Testa roboten i Web Chat

1. Medan du fortfarande beAzure Portal för den nya bot väljer du **test i Web Chat**. 
1. Skriv texten `hello`i text rutan **Skriv ditt meddelande** . Roboten svarar med information om bot Framework, samt exempel frågor för den aktuella LUIS-modellen, till exempel att boka en flygning till Paris. 

    ![Skärm bild av Azure Portal anger du texten Hej.](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    Du kan använda test funktionerna för att snabbt testa din robot. Om du vill ha mer fullständig testning, inklusive fel sökning, laddar du ned bot koden och använder Visual Studio. 

## <a name="download-the-web-app-bot-source-code"></a>Ladda ned käll koden för webb programs bot
Ladda ned koden för webbappsroboten så att du kan utveckla den och använda den på den lokala datorn. 

1. Välj **Skapa** i avsnittet **Bot management** (Robothantering) på Azure-portalen. 

1. Välj **Download Bot source code** (Ladda ned robotens källkod). 

    [![Ladda ned källkoden för webbappsroboten för grundläggande robot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. När popup-dialogrutan frågar **innehåller appinställningar i den hämtade ZIP-filen?** väljer du **Ja**.

1. Om källkoden är zippad innehåller meddelandet en länk för att ladda ned koden. Klicka på länken. 

1. Spara ZIP-filen till din lokala dator och extrahera filerna. Öppna projektet med Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Granska koden för att skicka uttryck till LUIS och hämta svar

1. Öppna filen **LuisHelper.cs** . Det är här som användaruttrycket som angetts i roboten skickas till LUIS. Svaret från LUIS returneras från metoden som ett **BookDetails** -objekt. När du skapar en egen robot bör du även skapa ett eget objekt för att returnera informationen från LUIS. 


    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
        }
    }
    ```

1. Öppna **BookingDetails.cs** för att se hur objektet sammanfattar Luis-informationen. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Öppna **dialoger – > BookingDialog.cs** för att förstå hur BookingDetails-objektet används för att hantera konversations flödet. Rese information ställs i steg, sedan bekräftas hela bokningen och upprepas slutligen tillbaka till användaren. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>Starta bot-koden i Visual Studio

Starta roboten i Visual Studio. Ett webbläsarfönster öppnas med webbappsrobotens webbplats på `http://localhost:3978/`. En start sida visas med information om din robot.

![En start sida visas med information om din robot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Använd bot-emulatorn för att testa roboten

1. Starta bot-emulatorn och välj **Öppna bot**.
1. Ange din robot-URL i dialog rutan **öppna en robot** -pop, till exempel `http://localhost:3978/api/messages`. `/api/messages` Vägen är webb adressen för bot.
1. Ange **Microsoft app-ID** och **Microsoft app-lösenordet**, som finns i filen **appSettings. JSON** i roten för den robot kod som du laddade ned.

    Du kan också skapa en ny robot konfiguration och kopiera `appId` och `appPassword` från filen **appSettings. JSON** i Visual Studio-projektet för bot. Namnet på konfigurations filen för bot ska vara samma som namnet på bot-filen. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
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

1. I bot-emulatorn anger `Hello` du och får samma svar för den grundläggande roboten som du fick i **testet i Web Chat**.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Ask bot a fråga för bok

1. I bot-emulatorn, boka en flygning genom att ange följande uttryck: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    Robot-emulatorn ber om att bekräfta. 

1. Välj **Ja**. Roboten svarar med en sammanfattning av dess åtgärder. 
1. Från loggen för bot-emulatorn väljer du den rad som `Luis Trace`innehåller. Detta visar JSON-svaret från LUIS för avsikten och entiteten i uttryck.

    [![Svar från grundläggande robot i emulatorn](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Nästa steg

Se fler [exempel](https://github.com/microsoft/botframework-solutions) med konversationsrobotar. 

> [!div class="nextstepaction"]
> [Bygg en Language Understanding-app med en anpassad ämnes domän](luis-quickstart-intents-only.md)

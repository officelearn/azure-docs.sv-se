---
title: 'Självstudie: Application Insights, C# -Luis'
titleSuffix: Azure Cognitive Services
description: Den här självstudien lägger till robot-och Language Understanding information för att Application Insights telemetri data lagring.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b9c47685253e2a70c7b5e947debaac6f5f3264b2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888302"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-c"></a>Självstudie: Lägg till LUIS-resultat till Application Insights från en robot iC#

Den här självstudien lägger till robot-och Language Understanding information för att [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetri data lagring. När du har dessa data kan du fråga det med Kusto-språket eller Power BI för att analysera, aggregera och rapportera om avsikter och entiteter i uttryck i real tid. Den här analysis hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter av LUIS-appen.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Fånga bot-och språk förståelse data i Application Insights
> * Fråga Application Insights för Language Understanding data

## <a name="prerequisites"></a>Krav

* En robot i Azure bot service som skapats med Application Insights aktiverat.
* Hämtad robot kod från föregående bot- **[Guide](luis-csharp-tutorial-bf-v4.md)** . 
* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio-kod](https://code.visualstudio.com/Download)

All kod i den här självstudien finns i [Azure-samples language Understanding GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Lägg till Application Insights i Web App bot-projekt

Application Insights-tjänsten, som används i den här web app-robot samlar för närvarande allmänt tillstånd telemetri för roboten. Den samlar inte in LUIS-information. 

För att kunna avbilda LUIS-informationen behöver Web App-roboten **[Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet-paketet installerat och konfigurerat.  

1. Lägg till beroendet till lösningen från Visual Studio. I **Solution Explorer**högerklickar du på projekt namnet och väljer **Hantera NuGet-paket...** . NuGet Package Manager visar en lista över installerade paket. 
1. Välj **Bläddra** och Sök efter **Microsoft. ApplicationInsights**.
1. Installera paketet. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Samla in och skicka LUIS frågeresultaten till Application Insights

1. Öppna `LuisHelper.cs`-filen och ersätt innehållet med följande kod. **LogToApplicationInsights** -metoden fångar bot-och Luis-data och skickar dem till Application Insights som en spårnings händelse med namnet `LUIS`.

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
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
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
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
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
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Lägg till Application Insights Instrumentation-nyckel 

För att kunna lägga till data i Application Insights behöver du Instrumentation-nyckeln.

1. I en webbläsare går du till [Azure Portal](https://portal.azure.com)och letar rätt på din robots **Application Insights** -resurs. Namnet kommer att ha det mesta av robotens namn och sedan slumpmässiga tecken i slutet av namnet, till exempel `luis-csharp-bot-johnsmithxqowom`. 
1. På sidan **Översikt** i Application Insights resursen kopierar du **Instrumentation-nyckeln**.
1. Öppna filen **appSettings. JSON** i Visual Studio-roten i bot-projektet. Den här filen innehåller alla miljövariabler.
1. Lägg till en ny variabel `BotDevAppInsightsKey` med värdet för Instrumentation-nyckeln. Värdet i måste vara inom citat tecken. 

## <a name="build-and-start-the-bot"></a>Skapa och starta bot

1. Skapa och kör roboten i Visual Studio. 
1. Starta bot-emulatorn och öppna roboten. Det här [steget](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) finns i den föregående själv studie kursen.

1. Ställ en fråga till bot. Det här [steget](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) finns i den föregående själv studie kursen.

## <a name="view-luis-entries-in-application-insights"></a>Visa LUIS poster i Application Insights

Öppna Application Insights för att se LUIS-poster. Det kan ta några minuter innan data visas i Application Insights.

1. Öppna robotens Application Insights-resurs i [Azure Portal](https://portal.azure.com). 
1. När resursen öppnas väljer du **Sök** och söker efter alla data under de senaste **30 minuterna** med händelse typen **trace**. Välj spåret med namnet **Luis**. 
1. Robot-och LUIS-informationen är tillgänglig under **anpassade egenskaper**. 

    ![Granska anpassade LUIS-egenskaper som lagras i Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Fråga efter Application Insights för avsikt, poäng och uttryck
Application Insights ger dig möjlighet att fråga data med [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) -språket, samt att exportera det till [Power BI](https://powerbi.microsoft.com). 

1. Välj **logg (analys)** . Ett nytt fönster öppnas med ett frågefönster högst upp och en data-fönster för tabellen nedan som. Om du har använt databaser tidigare är i den här ordningen välbekanta. Frågan representerar dina tidigare filtrerade data. Kolumnen **CustomDimensions** innehåller robot-och Luis-informationen.
1. Om du vill dra ut det högsta syftet, poängen och uttryck, lägger du till följande strax ovanför den sista raden (`|top...` linjen) i frågefönstret:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Kör frågan. De nya kolumnerna topIntent, poäng och uttryck är tillgängliga. Välj topIntent kolumn att sortera.

Läs mer om [Kusto-frågespråket](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) eller [exportera data till Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Läs mer om Bot Framework

Läs mer om [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till application insights-data innehåller app-ID, versions-ID, senaste ändringsdatum för modellen, datum för senaste train, senaste publiceringsdatum. Dessa värden kan antingen hämtas från slut punkts-URL: en (app-ID och versions-ID) eller från ett redigerings-API-anrop som sedan ställs in i inställningarna för webbappens bot och hämtas därifrån.  

Om du använder samma slutpunkt-prenumeration för mer än en LUIS-app, bör du också innehålla prenumerations-ID och en egenskap om att det är en delad nyckel.

> [!div class="nextstepaction"]
> [Mer information om exempel yttranden](luis-how-to-add-example-utterances.md)

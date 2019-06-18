---
title: Application Insights, C#
titleSuffix: Azure Cognitive Services
description: Den här självstudien lägger till bot och Language Understanding information datalagring för Application Insights telemetry.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/16/2019
ms.author: diberry
ms.openlocfilehash: fa7147dd1b5f22ead17a60042c1c35c4b770cd18
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67154911"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-c"></a>Lägg till LUIS resultaten till Application Insights från en robot iC#

Den här självstudien lägger till bot och Language Understanding information till [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetri datalagring. När du har dessa data kan du fråga den med Kusto språk eller Power BI för att analysera, aggregeras, och rapportera om avsikter och entiteter av uttryck i realtid. Den här analysis hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter av LUIS-appen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Avbilda bot och Language understanding data i Application Insights
> * Fråga efter Application Insights för Language Understanding-data

## <a name="prerequisites"></a>Nödvändiga komponenter

* En Azure bot service-robot skapats med Application Insights som aktiveras.
* Ned bot kod från tidigare roboten  **[självstudien](luis-csharp-tutorial-bf-v4.md)** . 
* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

All kod i den här självstudien är tillgänglig på den [Azure-Samples Language Understanding GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Lägg till Application Insights web app bot-projekt

Application Insights-tjänsten, som används i den här web app-robot samlar för närvarande allmänt tillstånd telemetri för roboten. Den samlar inte in LUIS information. 

För att samla in informationen om LUIS, web app-robot måste den **[Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet-paketet installeras och konfigureras.  

1. Lägg till beroendet till lösningen från Visual Studio. I den **Solution Explorer**, högerklicka på projektnamnet och välj **hantera NuGet-paket...** . NuGet Package manager visar en lista över installerade paket. 
1. Välj **Bläddra** söker sedan efter **Microsoft.ApplicationInsights**.
1. Installera paketet. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Samla in och skicka LUIS frågeresultaten till Application Insights

1. Öppna den `LuisHelper.cs` och Ersätt innehållet med följande kod. Den **LogToApplicationInsights** metoden avbilda bot och LUIS data och skickar den till Application Insights som en spårningshändelse med namnet `LUIS`.

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

## <a name="add-application-insights-instrumentation-key"></a>Lägga till Application Insights-instrumentationsnyckeln 

Om du vill lägga till data till application insights, behöver du instrumenteringsnyckeln.

1. I en webbläsare i den [Azure-portalen](https://portal.azure.com), hitta din robot **Application Insights** resurs. Namnet har de flesta av de botnamn och sedan slumpmässiga tecken i slutet av namnet, till exempel `luis-csharp-bot-johnsmithxqowom`. 
1. På Application Insights-resurs på den **översikt** sidan, kopiera den **Instrumenteringsnyckeln**.
1. Visual Studio, öppna den **appsettings.json** i roten av projektet bot. Den här filen innehåller alla miljövariabler.
1. Lägg till en ny variabel `BotDevAppInsightsKey` med värdet för din instrumentationsnyckel. Värdet i ska vara inom citattecken. 

## <a name="build-and-start-the-bot"></a>Skapa och starta roboten

1. Skapa och kör roboten i Visual Studio. 
1. Starta bot-emulatorn och öppna roboten. Detta [steg](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) har angetts i föregående självstudie.

1. Ställ en fråga för roboten. Detta [steg](luis-csharp-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) har angetts i föregående självstudie.

## <a name="view-luis-entries-in-application-insights"></a>Visa LUIS poster i Application Insights

Öppna Application Insights för att se LUIS-poster. Det kan ta några minuter innan data visas i Application Insights.

1. I den [Azure-portalen](https://portal.azure.com), öppna robotens Application Insights-resurs. 
1. När resursen öppnas väljer **Search** och Sök efter alla data under senaste **30 minuter** med händelsetyp av **Trace**. Välj spåra med namnet **LUIS**. 
1. Bot och LUIS information är tillgänglig under **anpassade egenskaper**. 

    ![Granska LUIS anpassade egenskaper lagras i Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Fråga efter Application Insights för avsikt, poäng och uttryck
Application Insights ger dig möjlighet att fråga efter data med den [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) språk, samt exportera den till [Power BI](https://powerbi.microsoft.com). 

1. Välj **Log (analys)** . Ett nytt fönster öppnas med ett frågefönster högst upp och en data-fönster för tabellen nedan som. Om du har använt databaser tidigare är i den här ordningen välbekanta. Frågan representerar dina tidigare filtrerade data. Den **CustomDimensions** kolumnen har bot och LUIS information.
1. Om du vill hämta de översta avsikt, poäng och uttryck, lägger du till följande ovanför den sista raden (den `|top...` rad) i frågefönstret:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Kör frågan. De nya kolumnerna topIntent, poäng och uttryck är tillgängliga. Välj topIntent kolumnen för att sortera.

Läs mer om den [Kusto-frågespråket](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) eller [exportera data till Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Läs mer om Bot Framework

Läs mer om [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till application insights-data innehåller app-ID, versions-ID, senaste ändringsdatum för modellen, datum för senaste train, senaste publiceringsdatum. Dessa värden kan antingen vara hämtas från slutpunkts-URL (app-ID och versions-ID) eller från ett redigering API-anrop och sedan anges i inställningarna för web app-robot och hämtas därifrån.  

Om du använder samma slutpunkt-prenumeration för mer än en LUIS-app, bör du också innehålla prenumerations-ID och en egenskap om att det är en delad nyckel.

> [!div class="nextstepaction"]
> [Mer information om exempel yttranden](luis-how-to-add-example-utterances.md)

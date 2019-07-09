---
title: Application Insights Node.js
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
ms.openlocfilehash: cfed5477df75350f24e77786117e85b9c728c49a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2019
ms.locfileid: "67657761"
---
# <a name="add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Lägg till LUIS resultaten till Application Insights från en robot i Node.js
Den här självstudien lägger till bot och Language Understanding information till [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetri datalagring. När du har dessa data kan du fråga den med Kusto språk eller Power BI för att analysera, aggregeras, och rapportera om avsikter och entiteter av uttryck i realtid. Den här analysis hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter av LUIS-appen.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Avbilda bot och Language understanding data i Application Insights
> * Fråga efter Application Insights för Language Understanding-data

## <a name="prerequisites"></a>Förutsättningar

* En Azure bot service-robot skapats med Application Insights som aktiveras.
* Ned bot kod från tidigare roboten  **[självstudien](luis-nodejs-tutorial-bf-v4.md)** . 
* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

All kod i den här självstudien är tillgänglig på den [Azure-Samples Language Understanding GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Lägg till Application Insights web app bot-projekt
Application Insights-tjänsten, som används i den här web app-robot samlar för närvarande allmänt tillstånd telemetri för roboten. Den samlar inte in LUIS information. 

För att samla in informationen om LUIS, web app-robot måste den **[Programinsikter](https://www.npmjs.com/package/applicationinsights)** NPM-paket installeras och konfigureras.  

1. Lägg till följande NPM-paket med hjälp av kommandot visas i roten för bot-projekt i den VSCode integrerade terminalen: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Den **understreck** paketet används för att förenkla LUIS JSON-struktur så att det är lättare att se och använda i Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Samla in och skicka LUIS frågeresultaten till Application Insights

1. Skapa en ny fil i VSCode, **appInsightsLog.js** och Lägg till följande kod:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    Den här filen tar roboten kontext och luis-svaret, plattar ut båda objekten och klistrar in dem i en **Trace** händelsen i application insights. Händelsens namn är **LUIS**. 

1. Öppna den **dialogrutor** mappen kommer **luisHelper.js** fil. Inkludera den nya **appInsightsLog.js** som en nödvändig fil och avbilda bot kontext och LUIS-svar. Den fullständiga koden för den här filen är: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
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
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
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

## <a name="add-application-insights-instrumentation-key"></a>Lägga till Application Insights-instrumentationsnyckeln 

Om du vill lägga till data till application insights, behöver du instrumenteringsnyckeln.

1. I en webbläsare i den [Azure-portalen](https://portal.azure.com), hitta din robot **Application Insights** resurs. Namnet har de flesta av de botnamn och sedan slumpmässiga tecken i slutet av namnet, till exempel `luis-nodejs-bot-johnsmithxqowom`. 
1. På Application Insights-resurs på den **översikt** sidan, kopiera den **Instrumenteringsnyckeln**.
1. Öppna i VSCode, den **.env** i roten av projektet bot. Den här filen innehåller alla miljövariabler.  
1. Lägg till en ny variabel `MicrosoftApplicationInsightsInstrumentationKey` med värdet för din instrumentationsnyckel. Gör inga put värdet inom citattecken. 

## <a name="start-the-bot"></a>Starta roboten

1. Starta roboten från integrerade terminalen VSCode:
    
    ```console
    npm start
    ```

1. Starta bot-emulatorn och öppna roboten. Detta [steg](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) har angetts i föregående självstudie.

1. Ställ en fråga för roboten. Detta [steg](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) har angetts i föregående självstudie.

## <a name="view-luis-entries-in-application-insights"></a>Visa LUIS poster i Application Insights

Öppna Application Insights för att se LUIS-poster. Det kan ta några minuter innan data visas i Application Insights.

1. I den [Azure-portalen](https://portal.azure.com), öppna robotens Application Insights-resurs. 
1. När resursen öppnas väljer **Search** och Sök efter alla data under senaste **30 minuter** med händelsetyp av **Trace**. Välj spåra med namnet **LUIS**. 
1. Bot och LUIS information är tillgänglig under **anpassade egenskaper**. 

    ![Granska LUIS anpassade egenskaper lagras i Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Fråga efter Application Insights för avsikt, poäng och uttryck
Application Insights ger dig möjlighet att fråga efter data med den [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) språk, samt exportera den till [Power BI](https://powerbi.microsoft.com). 

1. Välj **Log (analys)** . Ett nytt fönster öppnas med ett frågefönster högst upp och en data-fönster för tabellen nedan som. Om du har använt databaser tidigare är i den här ordningen välbekanta. Frågan representerar dina tidigare filtrerade data. Den **CustomDimensions** kolumnen har bot och LUIS information.
1. Om du vill hämta de översta avsikt, poäng och uttryck, lägger du till följande ovanför den sista raden (den `|top...` rad) i frågefönstret:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Kör frågan. De nya kolumnerna topIntent, poäng och uttryck är tillgängliga. Välj topIntent kolumnen för att sortera.

Läs mer om den [Kusto-frågespråket](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) eller [exportera data till Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till application insights-data innehåller app-ID, versions-ID, senaste ändringsdatum för modellen, datum för senaste train, senaste publiceringsdatum. Dessa värden kan antingen vara hämtas från slutpunkts-URL (app-ID och versions-ID) eller från ett redigering API-anrop och sedan anges i inställningarna för web app-robot och hämtas därifrån.  

Om du använder samma slutpunkt-prenumeration för mer än en LUIS-app, bör du också innehålla prenumerations-ID och en egenskap om att det är en delad nyckel. 

> [!div class="nextstepaction"]
> [Mer information om exempel yttranden](luis-how-to-add-example-utterances.md)

---
title: 'Självstudie: Application Insights, Node. js-LUIS'
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
ms.openlocfilehash: b0bc4a93df7db7145accf2b485c45256f53c324d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498959"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Självstudie: Lägg till LUIS-resultat till Application Insights från en robot i Node. js
Den här självstudien lägger till robot-och Language Understanding information för att [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetri data lagring. När du har dessa data kan du fråga det med Kusto-språket eller Power BI för att analysera, aggregera och rapportera om avsikter och entiteter i uttryck i real tid. Den här analysen hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter i din LUIS-app.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Fånga bot-och språk förståelse data i Application Insights
> * Fråga Application Insights för Language Understanding data

## <a name="prerequisites"></a>Förutsättningar

* En robot i Azure bot service som skapats med Application Insights aktiverat.
* Hämtad robot kod från föregående bot- **[Guide](luis-nodejs-tutorial-bf-v4.md)** . 
* [Robotemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

All kod i den här självstudien finns i [Azure-samples language Understanding GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Lägg till Application Insights i Web App bot-projekt
För närvarande används den Application Insights tjänsten som används i den här roboten för webbapp, och samlar in allmän tillstånds telemetri för bot. Den samlar inte in LUIS-information. 

För att kunna avbilda LUIS-informationen måste det **[Application Insights](https://www.npmjs.com/package/applicationinsights)** NPM-paketet installeras och konfigureras av Web App bot.  

1. I VSCode-integrerade terminalen i roten för bot-projektet lägger du till följande NPM-paket med kommandot som visas: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Under **strecks** paketet används för att förenkla Luis JSON-strukturen så att det blir enklare att se och använda i Application Insights.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Avbilda och skicka LUIS-frågeresultat till Application Insights

1. I VSCode skapar du en ny fil **appInsightsLog. js** och lägger till följande kod:

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

    Den här filen tar bot-kontexten och Luis-svaret, fören klar båda objekten och infogar dem i en **spårnings** händelse i Application Insights. Händelsens namn är **Luis**. 

1. Öppna mappen **dialog rutor** , sedan filen **luisHelper. js** . Inkludera den nya **appInsightsLog. js** som en obligatorisk fil och avbilda bot-kontexten och Luis-svaret. Den fullständiga koden för den här filen är: 

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

## <a name="add-application-insights-instrumentation-key"></a>Lägg till Application Insights Instrumentation-nyckel 

För att kunna lägga till data i Application Insights behöver du Instrumentation-nyckeln.

1. I en webbläsare går du till [Azure Portal](https://portal.azure.com)och letar rätt på din robots **Application Insights** -resurs. Namnet kommer att ha det mesta av robotens namn och sedan slumpmässiga tecken i slutet av namnet, till exempel `luis-nodejs-bot-johnsmithxqowom`. 
1. På sidan **Översikt** i Application Insights resursen kopierar du **Instrumentation-nyckeln**.
1. Öppna **. kuvert** -filen i VSCode-roten i bot-projektets rot. Den här filen innehåller alla miljövariabler.  
1. Lägg till en ny variabel `MicrosoftApplicationInsightsInstrumentationKey` med värdet för Instrumentation-nyckeln. Lägg inte till värdet i citat tecken. 

## <a name="start-the-bot"></a>Starta roboten

1. Starta roboten från VSCode-integrerade terminalen:
    
    ```console
    npm start
    ```

1. Starta bot-emulatorn och öppna roboten. Det här [steget](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) finns i den föregående själv studie kursen.

1. Ställ en fråga till bot. Det här [steget](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) finns i den föregående själv studie kursen.

## <a name="view-luis-entries-in-application-insights"></a>Visa LUIS-poster i Application Insights

Öppna Application Insights för att se posterna i LUIS. Det kan ta några minuter innan data visas i Application Insights.

1. Öppna robotens Application Insights-resurs i [Azure Portal](https://portal.azure.com). 
1. När resursen öppnas väljer du **Sök** och söker efter alla data under de senaste **30 minuterna** med händelse typen **trace**. Välj spåret med namnet **Luis**. 
1. Robot-och LUIS-informationen är tillgänglig under **anpassade egenskaper**. 

    ![Granska anpassade LUIS-egenskaper som lagras i Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Fråga Application Insights efter avsikt, poäng och uttryck
Application Insights ger dig möjlighet att fråga data med [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) -språket, samt att exportera det till [Power BI](https://powerbi.microsoft.com). 

1. Välj **logg (analys)** . Ett nytt fönster öppnas med ett frågefönster längst upp och ett data tabell fönster. Om du har använt databaser tidigare är den här ordningen bekant. Frågan representerar dina tidigare filtrerade data. Kolumnen **CustomDimensions** innehåller robot-och Luis-informationen.
1. Om du vill dra ut det högsta syftet, poängen och uttryck, lägger du till följande strax ovanför den sista raden (`|top...` linjen) i frågefönstret:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Kör frågan. De nya kolumnerna för topIntent, score och uttryck är tillgängliga. Välj topIntent kolumn att sortera.

Läs mer om [Kusto-frågespråket](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) eller [exportera data till Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till i Application Insights-data inkluderar app-ID, versions-ID, senaste modell ändrings datum, senaste träna datum, senaste Publicerings datum. Dessa värden kan antingen hämtas från slut punkts-URL: en (app-ID och versions-ID) eller från ett redigerings-API-anrop som sedan ställs in i inställningarna för webbappens bot och hämtas därifrån.  

Om du använder samma slut punkts prenumeration för mer än en LUIS-app bör du även inkludera prenumerations-ID och en egenskap som anger att den är en delad nyckel. 

> [!div class="nextstepaction"]
> [Läs mer om exempel yttranden](luis-how-to-add-example-utterances.md)

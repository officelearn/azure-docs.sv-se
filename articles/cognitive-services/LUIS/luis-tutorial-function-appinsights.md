---
title: Application Insights Node.js
titleSuffix: Azure Cognitive Services
description: Skapa en robot som är integrerad med en LUIS-programmet och Application Insights med hjälp av Node.js.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: c9a772af79cba8b5bfb592eaf03efa37520d5e48
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870613"
---
# <a name="add-luis-results-to-application-insights-and-azure-functions"></a>Lägga till LUIS resultaten till Application Insights och Azure functions
Den här självstudien lägger till LUIS begäranden och svar information till [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetri datalagring. När du har dessa data kan du fråga den med Kusto språk eller PowerBi aggregeras, om du vill analysera och rapportera om avsikter och entiteter av uttryck i realtid. Den här analysis hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter av LUIS-appen.

Roboten har byggts med Bot Framework 3.x och Azure Web app-robot.

I den här guiden får du lära dig att:

> [!div class="checklist"]
* Lägg till Application Insights-biblioteket till en web app-robot
* Samla in och skicka LUIS frågeresultaten till Application Insights
* Fråga efter Application Insights för övre avsikt, poäng och uttryck

## <a name="prerequisites"></a>Förutsättningar

* LUIS web app-robot från den **[föregående självstudie](luis-nodejs-tutorial-build-bot-framework-sample.md)** med Application Insights aktiveras. 

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

All kod i den här självstudien är tillgänglig på den [Azure-Samples GitHub-lagringsplatsen](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) och varje rad som är associerade med den här självstudien har kommenterats med `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Web app-robot med LUIS
Den här självstudien förutsätter att du har kod som ser ut som följande eller att du har slutfört den [andra självstudiekursen](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Lägg till Application Insights-biblioteket till web app-robot
Application Insights-tjänsten, som används i den här web app-robot samlar för närvarande allmänt tillstånd telemetri för roboten. Den samlar inte in LUIS begäranden och svar information som du behöver för att kontrollera och åtgärda dina avsikter och entiteter. 

För att samla in LUIS-begäran och svaret, web app-robot måste den **[Programinsikter](https://www.npmjs.com/package/applicationinsights)** NPM-paket installeras och konfigureras i den **app.js** fil. Avsiktshantering dialogrutan hanterare måste du skicka LUIS information om begäranden och svar till Application Insights. 

1. I Azure-portalen i web app bot service väljer **skapa** under den **Bot Management** avsnittet. 

    ![Välj ”Skapa” under avsnittet ”Bot Management” i Azure-portalen i web app bot service. ](./media/luis-tutorial-appinsights/build.png)

2. En ny webbläsarflik öppnas med App Service Editor. Välj appnamnet på den översta raden och välj sedan **öppna Kudu-konsolen**. 

    ![Välj appnamnet på den översta raden och välj sedan ”öppna Kudu-konsolen. ](./media/luis-tutorial-appinsights/kudu-console.png)

3. Ange följande kommando för att installera Application Insights och understreck-paket i konsolen:

    ```console
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Använd npm-kommandon för att installera Application Insights och understreck-paket](./media/luis-tutorial-appinsights/npm-install.png)

    Vänta tills paketen installeras:

    ```console
    luisbot@1.0.0 D:\home\site\wwwroot
    `-- applicationinsights@1.0.1 
      +-- diagnostic-channel@0.2.0 
      +-- diagnostic-channel-publishers@0.2.1 
      `-- zone.js@0.7.6 
    
    npm WARN luisbot@1.0.0 No repository field.
    luisbot@1.0.0 D:\home\site\wwwroot
    +-- botbuilder-azure@3.0.4
    | `-- azure-storage@1.4.0
    |   `-- underscore@1.4.4 
    `-- underscore@1.8.3 
    ```

    Du är klar med fliken kudu-konsolen.

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Samla in och skicka LUIS frågeresultaten till Application Insights
1. App Service Editor i fliken i webbläsaren, öppna den **app.js** fil.

2. Lägg till följande NPM bibliotek under den befintliga `requires` rader:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Skapa Application Insights-objektet och använda web app bot inställningen **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Lägg till den **appInsightsLog** funktionen:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    Den sista raden i funktionen är där data har lagts till i Application Insights. Händelsens namn är **LUIS-resultat**, ett unikt namn förutom några andra telemetridata som samlas in av den här web app-robot. 

5. Använd den **appInsightsLog** funktion. Du lägger till den var avsiktlig dialogrutan:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Testa web app-robot genom att använda den **testa i Web Chat** funktionen. Du bör se någon skillnad eftersom allt arbete i Application Insights inte är i bot-svar.

## <a name="view-luis-entries-in-application-insights"></a>Visa LUIS poster i Application Insights
Öppna Application Insights för att se LUIS-poster. 

1. I portalen, väljer **alla resurser** sedan filtrera efter namnet på webbappen bot. Klicka på resursen med typen **Application Insights**. Ikonen för Application Insights är en glödlampa. 

    ! [[Sökning för app insights i Azure Portal](./media/luis-tutorial-appinsights/search-for-app-insights.png)

2. När resursen öppnas klickar du på den **Search** ikon på förstoringsglaset i panelen till höger. En ny panel till rätt visar. Beroende på hur mycket telemetridata har hittats, panelen kan ta en andra att visa. Sök efter `LUIS-results` och trycker på Ange på tangentbordet. Listan är smalare med bara LUIS frågeresultat har lagts till i den här självstudiekursen.

    ![Filtrera till beroenden](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Välj den översta posten. Ett nytt fönster visar mer detaljerad data, inklusive anpassade data för LUIS-frågan till längst till höger. Innehåller främsta syftet och dess poäng.

    ![Information om beroenden](./media/luis-tutorial-appinsights/app-insights-detail.png)

    När du är klar väljer du upp till höger **X** att återgå till listan över beroende objekt. 


> [!Tip]
> Om du vill spara beroendelistan och återgå till den senare klickar du på **... Mer** och klicka på **spara favorit**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Fråga efter Application Insights för avsikt, poäng och uttryck
Application Insights ger dig möjlighet att fråga efter data med den [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) språk, samt exportera den till [PowerBI](https://powerbi.microsoft.com). 

1. Klicka på **Analytics** överst i beroendet lista över filterfältet. 

    ![Knappen Analytics](./media/luis-tutorial-appinsights/analytics-button.png)

2. Ett nytt fönster öppnas med ett frågefönster högst upp och en data-fönster för tabellen nedan som. Om du har använt databaser tidigare är i den här ordningen välbekanta. Frågan innehåller alla objekt från de senaste 24 timmarna från och med namnet `LUIS-results`. Den **CustomDimensions** kolumnen har LUIS-frågeresultat som namn/värde-par.

    ![Analytics frågefönster](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Om du vill hämta de översta avsikt, poäng och uttryck, lägger du till följande ovanför den sista raden i frågefönstret:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Kör frågan. Rulla ned till höger i tabellen. De nya kolumnerna topIntent, poäng och uttryck är tillgängliga. Klicka på kolumnen topIntent för att sortera.

    ![Främsta syftet för analys](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Läs mer om den [Kusto-frågespråket](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) eller [exportera data till Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till application insights-data innehåller app-ID, versions-ID, senaste ändringsdatum för modellen, datum för senaste train, senaste publiceringsdatum. Dessa värden kan antingen hämtas från slutpunkts-URL (app-ID och versions-ID) eller från en [redigering API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) anropa sedan anges i inställningarna för web app-robot och hämtas därifrån.  

Om du använder samma slutpunkt-prenumeration för mer än en LUIS-app, bör du också innehålla prenumerations-ID och en egenskap om att det är en delad nyckel. 

> [!div class="nextstepaction"]
> [Mer information om exempel yttranden](luis-how-to-add-example-utterances.md)

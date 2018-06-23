---
title: Lägg till THOMAS data till Application Insights med Node.js | Microsoft Docs
titleSuffix: Azure
description: Skapa en bot integrerad med ett THOMAS program och Application Insights med Node.js.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 01/18/2018
ms.author: v-geberr
ms.openlocfilehash: 929b6e1cc980d7215f91a616820e257aed26bab7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35355467"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Lägga till THOMAS resultat till Application Insights från en web app bot
Den här självstudiekursen lägger till THOMAS förfrågan och svar information till [Programinsikter](https://azure.microsoft.com/services/application-insights/) telemetri datalagring. När du har dessa data kan du kan fråga med det Kusto eller PowerBi aggregeras, om du vill analysera och rapportera om avsikter och entiteter av utterance i realtid. Den här analysis hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter för THOMAS appen.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
* Lägga till Application Insights bibliotek i en web app bot
* Samla in och skicka THOMAS frågeresultaten till Application Insights
* Fråga Programinsikter för övre avsikt, poäng och utterance

## <a name="prerequisites"></a>Förutsättningar

* THOMAS web app bot från den **[tidigare kursen](luis-nodejs-tutorial-build-bot-framework-sample.md)** med Application Insights aktiverat. 

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

All kod i den här kursen är tillgängligt på den [THOMAS prover github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/nodejs) och varje rad som är associerade med den här självstudiekursen har kommenterats med `//APPINSIGHT:`. 

## <a name="web-app-bot-with-luis"></a>Web app bot med THOMAS
Den här kursen förutsätter att du har kod som ser ut som följande eller att du har slutfört den [andra kursen](luis-nodejs-tutorial-build-bot-framework-sample.md): 

   [!code-javascript[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/nodejs/app.js "Web app bot with LUIS")]

## <a name="add-application-insights-library-to-web-app-bot"></a>Lägga till bibliotek för Application Insights web app bot
Application Insights-tjänsten, som används i den här web app bot samlar för närvarande allmänt tillstånd telemetri för bot. Den samlar inte in THOMAS förfrågan och svar information som du behöver för att kontrollera och åtgärda dina avsikter och enheter. 

För att samla in THOMAS förfrågan och svar, web app bot måste den **[Programinsikter](https://www.npmjs.com/package/applicationinsights)** NPM paketet installeras och konfigureras i den **app.js** fil. Avsiktshantering dialogrutan hanterare måste skicka THOMAS förfrågan och svar information till Application Insights. 

1. Välj i Azure-portalen i tjänsten web app bot **skapa** under den **Bot Management** avsnitt. 

    ![Sök efter appen insikter](./media/luis-tutorial-appinsights/build.png)

2. En ny webbläsarflik öppnas med App Service Editor. Välj namnet på appen i det översta fältet och sedan **öppna Kudu-konsolen**. 

    ![Sök efter appen insikter](./media/luis-tutorial-appinsights/kudu-console.png)

3. Ange följande kommando för att installera Application Insights och understreck paket i konsolen:

    ```
    cd site\wwwroot && npm install applicationinsights && npm install underscore
    ```

    ![Sök efter appen insikter](./media/luis-tutorial-appinsights/npm-install.png)

    Vänta tills de paket som ska installeras:

    ```
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

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Samla in och skicka THOMAS frågeresultaten till Application Insights
1. Fliken App Service Editor webbläsare, öppna den **app.js** fil.

2. Lägg till följande NPM bibliotek under den befintliga `requires` rader:

   [!code-javascript[Add NPM packages to app.js](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=12-16 "Add NPM packages to app.js")]

3. Skapa Application Insights-objektet och använda web app bot inställningen **BotDevInsightsKey**: 

   [!code-javascript[Create the Application Insights object](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=68-80 "Create the Application Insights object")]

4. Lägg till den **appInsightsLog** funktionen:

   [!code-javascript[Add the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=82-109 "Add the appInsightsLog function")]

    Den sista raden i funktionen är där data läggs till Application Insights. Händelsens namn är **THOMAS resultat**, ett unikt namn förutom andra telemetridata som samlas in av den här web app bot. 

5. Använd den **appInsightsLog** funktion. Du lägger till den var avsiktshantering dialogrutan:

   [!code-javascript[Use the appInsightsLog function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/nodejs/app.js?range=117-118 "Use the appInsightsLog function")]

6. Testa din web app bot med den **testa i Web chatta** funktion. Du bör se någon skillnad eftersom allt arbete i Application Insights inte bot svar.

## <a name="view-luis-entries-in-application-insights"></a>Visa THOMAS poster i Application Insights
Öppna Application Insights för att se THOMAS transaktioner. 

1. I portalen, Välj **alla resurser** sedan filtrera efter bot webbprogramnamnet. Klicka på resursen med typen **Programinsikter**. Ikonen för Application Insights är pratbubblan. 

    ![Sök efter appen insikter](./media/luis-tutorial-appinsights/search-for-app-insights.png)



2. När resursen öppnas klickar du på den **Sök** ikonen förstoringsglas på panelen längst till höger. En ny panel till rätt visar. Beroende på hur mycket telemetridata hittas, panelen kan ta dig tid att visa. Sök efter `LUIS-results` och trycker ange på tangentbordet. Listan minskar med bara THOMAS frågeresultat lagts till med den här kursen.

    ![Filtrera beroenden](./media/luis-tutorial-appinsights/app-insights-filter.png)

3. Markera den översta posten. Ett nytt fönster visar mer detaljerad data, inklusive anpassade data för THOMAS frågan till längst till höger. Innehåller information som övre avsikten och dess resultat.

    ![Information om beroenden](./media/luis-tutorial-appinsights/app-insights-detail.png)

    När du är klar väljer du upp längst till höger **X** att återgå till listan över beroende objekt. 


> [!Tip]
> Om du vill spara beroendelistan och återgår till den senare klickar du på **... Flera** och på **spara favorit**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Frågan Programinsikter för avsikt, poäng och utterance
Application Insights ger dig möjlighet att fråga efter data med den [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) språk, samt exportera den till [PowerBI](https://powerbi.microsoft.com). 

1. Klicka på **Analytics** överst i beroendet lista över filterfältet. 

    ![Knappen Analytics](./media/luis-tutorial-appinsights/analytics-button.png)

2. Med ett frågefönster överst och fönstret med tabellen nedan som öppnas ett nytt fönster. Om du har använt databaser innan är den här ordningen bekant. Frågan innehåller alla objekt från de senaste 24 timmarna som börjar med namnet `LUIS-results`. Den **CustomDimensions** kolumnen har THOMAS frågeresultat som namn/värde-par.

    ![Analytics query-fönstret](./media/luis-tutorial-appinsights/analytics-query-window.png)

3. Om du vill dra ut översta avsikt, poäng och utterance, lägger du till följande ovanför den sista raden i frågefönstret:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_intent_intent)
    | extend score = todouble(customDimensions.LUIS_intent_score)
    | extend utterance = tostring(customDimensions.LUIS_text)
    ```

4. Kör frågan. Rulla längst till höger i en tabell. De nya kolumnerna topIntent, poäng och utterance är tillgängliga. Klicka på kolumnen topIntent för att sortera.

    ![Analytics översta avsikt](./media/luis-tutorial-appinsights/app-insights-top-intent.png)


Lär dig mer om den [Kusto frågespråk](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) eller [exportera data till PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till application insights-data innehåller app-ID, versions-ID, senaste ändringsdatum för modellen, datum för senaste train, senast publiceringsdatum. Dessa värden kan antingen hämtas från slutpunkts-URL (app-ID och versions-ID) eller från en [redigering API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) anropa sedan i bot webbappsinställningarna och hämtas därifrån.  

Om du använder samma endpoint-prenumeration för mer än en THOMAS app, bör du också inkludera prenumerations-ID och en egenskap om att det är en delad nyckel. 

> [!div class="nextstepaction"]
> [Mer information om exempel utterances](luis-how-to-add-example-utterances.md)

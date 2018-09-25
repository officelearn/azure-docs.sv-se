---
title: Application Insights-data från LUIS med C#
titleSuffix: Azure Cognitive Services
description: Skapa en robot som är integrerad med en LUIS-programmet och Application Insights med C#.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/24/2018
ms.author: diberry
ms.openlocfilehash: 83ad70e1242af1e01af06206a3a141f455072a44
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47038960"
---
# <a name="add-luis-results-to-application-insights"></a>Lägg till LUIS resultaten till Application Insights
Den här självstudien lägger till LUIS Svarsinformation till [Application Insights](https://azure.microsoft.com/services/application-insights/) telemetri datalagring. När du har dessa data kan du fråga den med Kusto språk eller PowerBi aggregeras, om du vill analysera och rapportera om avsikter och entiteter av uttryck i realtid. Den här analysis hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter av LUIS-appen. 

Roboten har byggts med Bot Framework 3.x och Azure Web app-robot.

I den här guiden får du lära dig att:

> [!div class="checklist"]
* Lägg till Application Insights web app-robot
* Samla in och skicka LUIS frågeresultaten till Application Insights
* Fråga efter Application Insights för övre avsikt, poäng och uttryck

## <a name="prerequisites"></a>Förutsättningar

* LUIS web app-robot från den **[föregående självstudie](luis-csharp-tutorial-build-bot-framework-sample.md)** med Application Insights aktiveras. 
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) installerat lokalt på datorn.

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera dig för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

All kod i den här självstudien är tillgänglig på den [LUIS-Samples github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) och varje rad som är associerade med den här självstudien har kommenterats med `//LUIS Tutorial:`. 

## <a name="review-luis-web-app-bot"></a>Granska LUIS web app-robot
Den här självstudien förutsätter att du har kod som ser ut som följande eller att du har slutfört den [andra självstudiekursen](luis-csharp-tutorial-build-bot-framework-sample.md): 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights i web app-robot
För närvarande Application Insights-tjänsten har lagts till som en del av web app bot service skapar, samlar in telemetri allmänt tillstånd för roboten. Den samlar inte in LUIS Svarsinformation. För att analysera och förbättra LUIS, behöver du LUIS Svarsinformation.  

För att samla in LUIS-svaret, web app-robot måste **[Programinsikter](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** installerad och konfigurerad för projektet. 

## <a name="download-web-app-bot"></a>Ladda ned web app-robot
Använd [Visual Studio 2017](https://www.visualstudio.com/downloads/) att lägga till och konfigurera Application Insights för web app-robot. Ladda ned web app bot-kod för att kunna använda web app-robot i Visual Studio.

1. I Azure-portalen för web app-robot väljer **skapa**.

    ![Välj Build i portalen](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Välj **Download zip-filen** och vänta tills filen förbereds.

    ![Hämta zip-fil](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Välj **Download zip-filen** i popup-fönstret. Kom ihåg platsen på datorn, måste det i nästa avsnitt.

    ![Ladda ned zip-filen popup-fönstret](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Öppna lösningen i Visual Studio 2017

1. Extrahera filen till en mapp. 

2. Öppna Visual Studio 2017 och öppna lösningsfilen, `Microsoft.Bot.Sample.LuisBot.sln`. Om säkerhetsvarningen visas, väljer du ”OK”.

    ![Öppna lösningen i Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio måste lägga till beroenden i lösningen. I den **Solution Explorer**, högerklicka på **referenser**, och välj **hantera NuGet-paket...** . 

    ![Hantera NuGet-paket](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. NuGet Package manager visar en lista över installerade paket. Välj **återställa** i det gula fältet. Vänta tills återställningen ska slutföras.

    ![Återställ NuGet-paketen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Lägg till Application Insights i projektet
Installera och konfigurera Application Insights i Visual Studio. 

1. I Visual Studio 2017 på den översta menyn väljer **projekt**och välj sedan **Lägg till Application Insights Telemetry...** .

2. I den **konfiguration av Application Insights** väljer **börja kostnadsfritt**

    ![Börja konfigurera Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Registrera din app med Application Insights. Du kan behöva ange dina autentiseringsuppgifter för Azure portal. 

4. Visual Studio lägger till Application Insights i projektet, visa status när detta sker. 

    ![Status för Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    När processen har slutförts, den **konfiguration av Application Insights** visar förloppets status. 

    ![Installationsstatus för Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    Den **aktivera spårningsinsamling** rött, vilket innebär att den inte är aktiverad. Den här självstudien använder inte funktionen. 

## <a name="build-and-resolve-errors"></a>Skapa och åtgärda fel

1. Bygg lösningen genom att välja den **skapa** menyn och välj sedan **återskapa lösning**. Vänta på build ska slutföras. 

2. Om versionen inte med `CS0104` fel, måste du åtgärda dem. I den `Controllers` mapp i den `MessagesController.cs file`, åtgärda tvetydig användningen av `Activity` typ genom aktivitetstyp med typ av koppling. Gör detta genom att ändra namnet `Activity` på raderna 22 och 36 från `Activity` till `Connector.Activity`. Skapa lösningen igen. Det bör finnas några fler build-fel.

    Fullständig källan för filen är:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publicera projektet på Azure
Den **Application Insights** paketet är nu i projektet och korrekt konfigurerat för dina autentiseringsuppgifter i Azure-portalen. Ändringar för projektet måste publiceras tillbaka till Azure.

1. I den **Solution Explorer**, högerklicka på projektnamnet och välj sedan **publicera**.

    ![Publicera projektet på portalen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. I den **publicera** väljer **Skapa ny profil**.

    ![Publicera projektet på portalen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Välj **importera profil**, och välj **OK**.

    ![Publicera projektet på portalen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. I den **publicera inställningar importfilen** windows, navigera till projektmappen, navigera till den `PostDeployScripts` mapp, Välj den fil som slutar med `.PublishSettings`, och välj `Open`. Du har nu konfigurerat publicering för det här projektet. 

5. Publicera din lokala källkod Bot-tjänsten genom att välja den **publicera** knappen. Den **utdata** status visas i fönstret. Resten av kursen har slutförts i Azure-portalen. Stäng Visual Studio 2017. 

## <a name="open-three-browser-tabs"></a>Öppna tre flikar i webbläsaren
Hitta web app-robot i Azure-portalen och öppna den. Följande steg använder tre olika vyer av web app-robot. Det kan vara praktiskt att ha tre separata flikar öppna i webbläsaren: 
  
>  * Testa i webbchatt
>  * Skapa/öppna online Kodredigerare -> App Service Editor
>  * App Service Editor/öppna Kudu-konsolen -> diagnostiska konsolen

## <a name="modify-basicluisdialogcs-code"></a>Ändra BasicLuisDialog.cs koden

1. I den **App Service Editor** flik i webbläsaren, öppna den `BasicLuisDialog.cs` filen.

2. Lägg till följande NuGet beroende under den befintliga `using` rader:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Lägg till den `LogToApplicationInsights` funktionen:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Application Insights-instrumentationsnyckeln finns redan i web app robot program inställning med namnet `BotDevInsightsKey`. 

    Den sista raden i funktionen lägger till data till Application Insights. I spårningen heter `LUIS`, ett unikt namn förutom några andra telemetridata som samlas in av den här web app-robot. Alla egenskaper har också ett prefix med `LUIS_` så att du kan se vilka data som den här självstudien lägger till jämfört med information som ges med web app-robot.

4. Anropa den `LogToApplicationInsights` funktionen överst i den `ShowLuisResult` funktionen:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Skapa web app-robot
1. Skapa web app-robot på något av två sätt. Den första metoden är att högerklicka på `build.cmd` i den **App Service Editor**och välj sedan **kör från konsolen**. Utdata från konsolen visas och är klar med `Finished successfully.`

2. Om detta inte slutförs korrekt, måste du öppna konsolen, gå till skriptet och kör den med hjälp av följande steg. I den **App Service Editor**, i det översta blå fältet, Välj namnet på din robot, och välj sedan **öppna Kudu-konsolen** i den nedrullningsbara listan.

    ![Öppna Kudu-konsolen](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. Ange följande kommando i konsolfönstret:

    ```
    cd site\wwwroot && build.cmd
    ```

    Vänta på build att slutföra med `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testa web app-robot

1. Om du vill testa web app-robot, öppna den **testa i Web Chat** funktionen i portalen. 

2. Ange frasen `Coffee bar on please`.  

    ![Testa web app-robot i chatten](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Du bör se någon skillnad i chattrobot svaret. Ändringen är inte i roboten svar skickar data till Application Insights. Ange några fler yttranden så att det finns lite mer data i Application Insights:

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>Visa LUIS poster i Application Insights
Öppna Application Insights för att se LUIS-poster. 

1. I portalen, väljer **alla resurser** sedan filtrera efter namnet på webbappen bot. Klicka på resursen med typen **Application Insights**. Ikonen för Application Insights är en glödlampa. 

    ![Sök efter app insights](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. När resursen öppnas klickar du på den **Search** ikon på förstoringsglaset i panelen till höger. En ny panel till rätt visar. Beroende på hur mycket telemetridata har hittats, panelen kan ta en andra att visa. Sök efter `LUIS`. Listan är smalare med bara LUIS frågeresultat har lagts till i den här självstudiekursen.

    ![Sök efter spårningar](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Välj den översta posten. Ett nytt fönster visar mer detaljerad data, inklusive anpassade data för LUIS-frågan till längst till höger. Innehåller främsta syftet och dess poäng.

    ![Granska trace objekt](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    När du är klar väljer du upp till höger **X** att återgå till listan över beroende objekt. 


> [!Tip]
> Om du vill spara beroendelistan och återgå till den senare klickar du på **... Mer** och klicka på **spara favorit**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Fråga efter Application Insights för avsikt, poäng och uttryck
Application Insights ger dig möjlighet att fråga efter data med den [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) språk, samt exportera den till [PowerBI](https://powerbi.microsoft.com). 

1. Klicka på **Analytics** överst i beroendet lista över filterfältet. 

    ![Knappen Analytics](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Ett nytt fönster öppnas med ett frågefönster högst upp och en data-fönster för tabellen nedan som. Om du har använt databaser tidigare är i den här ordningen välbekanta. Frågan innehåller alla objekt från de senaste 24 timmarna från och med namnet `LUIS`. Den **CustomDimensions** kolumnen har LUIS-frågeresultat som namn/värde-par.

    ![Standard analysrapporten](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Om du vill hämta de översta avsikt, poäng och uttryck, lägger du till följande ovanför den sista raden i frågefönstret:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Kör frågan. Rulla ned till höger i tabellen. De nya kolumnerna topIntent, poäng och uttryck är tillgängliga. Klicka på kolumnen topIntent för att sortera.

    ![Anpassade analysrapporten](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


Läs mer om den [Kusto-frågespråket](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) eller [exportera data till Power BI](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Läs mer om Bot Framework
Läs mer om [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till application insights-data innehåller app-ID, versions-ID, senaste ändringsdatum för modellen, datum för senaste train, senaste publiceringsdatum. Dessa värden kan antingen hämtas från slutpunkts-URL (app-ID och versions-ID) eller från en [redigering API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) anropa sedan anges i inställningarna för web app-robot och hämtas därifrån.  

Om du använder samma slutpunkt-prenumeration för mer än en LUIS-app, bör du också innehålla prenumerations-ID och en egenskap om att det är en delad nyckel. 

> [!div class="nextstepaction"]
> [Mer information om exempel yttranden](luis-how-to-add-example-utterances.md)

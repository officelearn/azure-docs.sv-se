---
title: Lägg till THOMAS data till Application Insights med C# | Microsoft Docs
titleSuffix: Azure
description: Skapa en bot integrerad med ett THOMAS program och Application Insights med C#.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/07/2018
ms.author: v-geberr
ms.openlocfilehash: 52b6ae224b0e8da12eb4903f5100a6e5cc39704d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "35356126"
---
# <a name="add-luis-results-to-application-insights-from-a-web-app-bot"></a>Lägga till THOMAS resultat till Application Insights från en web app bot
Den här självstudiekursen lägger till THOMAS Svarsinformation till [Programinsikter](https://azure.microsoft.com/services/application-insights/) telemetri datalagring. När du har dessa data kan du kan fråga med det Kusto eller PowerBi aggregeras, om du vill analysera och rapportera om avsikter och entiteter av utterance i realtid. Den här analysis hjälper dig att avgöra om du ska lägga till eller redigera avsikter och entiteter för THOMAS appen.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
* Lägg till Application Insights web app bot
* Samla in och skicka THOMAS frågeresultaten till Application Insights
* Fråga Programinsikter för övre avsikt, poäng och utterance

## <a name="prerequisites"></a>Förutsättningar

* THOMAS web app bot från den **[tidigare kursen](luis-csharp-tutorial-build-bot-framework-sample.md)** med Application Insights aktiverat. 
* [Visual Studio 2017](https://www.visualstudio.com/downloads/) lokalt installerade på datorn.

> [!Tip]
> Om du inte redan har en prenumeration kan du registrera för en [kostnadsfritt konto](https://azure.microsoft.com/free/).

All kod i den här kursen är tillgängligt på den [THOMAS prover github-lagringsplatsen](https://github.com/Microsoft/LUIS-Samples/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/csharp) och varje rad som är associerade med den här självstudiekursen har kommenterats med `//LUIS Tutorial:`. 

## <a name="review-luis-web-app-bot"></a>Granska THOMAS web app bot
Den här kursen förutsätter att du har kod som ser ut som följande eller att du har slutfört den [andra kursen](luis-csharp-tutorial-build-bot-framework-sample.md): 

   [!code-csharp[Web app bot with LUIS](~/samples-luis/documentation-samples/tutorial-web-app-bot/csharp/BasicLuisDialog.cs "Web app bot with LUIS")]

## <a name="application-insights-in-web-app-bot"></a>Application Insights i web app bot
För närvarande Application Insights-tjänsten som lagts till som en del av bot service webbappen, samlar in allmänna telemetri för bot. Den samlar inte in THOMAS Svarsinformation. För att analysera och förbättra THOMAS, behöver du THOMAS Svarsinformation.  

För att avbilda THOMAS svaret web app bot måste **[Programinsikter](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** installeras och konfigureras för projektet. 

## <a name="download-web-app-bot"></a>Ladda ned web app bot
Använd [Visual Studio 2017](https://www.visualstudio.com/downloads/) lägga till och konfigurera Application Insights för web app bot. Ladda ned web app bot-kod för att kunna använda web app bot i Visual Studio.

1. Välj i Azure-portalen för web app bot, **skapa**.

    ![Välj Build i portalen](./media/luis-tutorial-bot-csharp-appinsights/download-build-menu.png)

2. Välj **hämta zip-filen** och vänta tills filen förbereds.

    ![Hämta zip-filen](./media/luis-tutorial-bot-csharp-appinsights/download-link.png)

3. Välj **hämta zip-filen** i popup-fönstret. Kom ihåg platsen på datorn, måste det i nästa avsnitt.

    ![Hämta zip-filen popup](./media/luis-tutorial-bot-csharp-appinsights/download-popup.png)

## <a name="open-solution-in-visual-studio-2017"></a>Öppna lösningen i Visual Studio 2017

1. Extrahera filen till en mapp. 

2. Öppna Visual Studio 2017 och öppna lösningsfilen, `Microsoft.Bot.Sample.LuisBot.sln`. Om säkerhetsvarningen visas, väljer du ”OK”.

    ![Öppna lösningen i Visual Studio 2017](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-security-warning.png)

3. Visual Studio måste lägga till beroenden lösningen. I den **Solution Explorer**, högerklicka på **referenser**, och välj **hantera NuGet-paket...** . 

    ![Hantera NuGet-paket](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-manage-nuget-packages.png)

4. NuGet Package manager visar en lista över installerade paket. Välj **återställa** i gult fält. Vänta tills återställningen ska slutföras.

    ![Återställ NuGet-paketen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-restore-packages.png)

## <a name="add-application-insights-to-the-project"></a>Lägg till Application Insights i projektet
Installera och konfigurera Application Insights i Visual Studio. 

1. Markera på den översta menyn i Visual Studio 2017 **projekt**och välj **Lägg till Application Insights Telemetry...** .

2. I den **Application Insights-konfigurationen** väljer **starta ledigt**

    ![Börja konfigurera Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configure-app-insights.png)

3. Registrera din app med Application Insights. Du kan behöva ange autentiseringsuppgifterna för Azure-portalen. 

4. Visual Studio lägger till Application Insights i projektet, visa status när detta sker. 

    ![Application Insights status](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-adding-application-insights-to-project.png)

    När processen har slutförts i **Application Insights-konfigurationen** visar statusen. 

    ![Installationsstatus för Application Insights](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-configured-application-insights-to-project.png)

    Den **Aktivera insamling** röd, vilket innebär att den inte är aktiverad. Den här kursen använder inte funktionen. 

## <a name="build-and-resolve-errors"></a>Skapa och åtgärda fel

1. Skapa lösningen genom att välja den **skapa** -menyn, välj sedan **återskapa lösning**. Vänta tills build till slut. 

2. Om versionen misslyckas med `CS0104` fel, måste du åtgärda dem. I den `Controllers` mapp i den `MessagesController.cs file`, åtgärda tvetydig användningen av `Activity` typ med aktivitetstypen med typ av koppling. Det gör du genom att ändra namnet `Activity` på rader 22 och 36 från `Activity` till `Connector.Activity`. Skapa lösningen igen. Det bör finnas några fler build-fel.

    Fullständig källan för filen är:

    [!code-csharp[MessagesController.cs file](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/MessagesController.cs "MessagesController.cs file")]

## <a name="publish-project-to-azure"></a>Publicera projektet till Azure
Den **Programinsikter** paketet finns nu i projektet och korrekt konfigurerad för dina autentiseringsuppgifter i Azure-portalen. Ändringarna för projektet ska publiceras till Azure.

1. I den **Solution Explorer**, högerklicka på projektnamnet och markerar sedan **publicera**.

    ![Publicera projekt till portalen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish.png)

2. I den **publicera** väljer **Skapa ny profil**.

    ![Publicera projekt till portalen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-1.png)

3. Välj **importera profil**, och välj **OK**.

    ![Publicera projekt till portalen](./media/luis-tutorial-bot-csharp-appinsights/vs-2017-publish-2.png)

4. I den **publicera inställningar importfilen** windows, navigerar du till projektmappen, navigera till den `PostDeployScripts` mapp, Välj den fil som slutar i `.PublishSettings`, och välj `Open`. Du har nu konfigurerat publicering för det här projektet. 

5. Publicera lokala källkoden till Bot-tjänsten genom att välja den **publicera** knappen. Den **utdata** fönstret visas status. Resten av guiden har slutförts i Azure-portalen. Stäng Visual Studio 2017. 

## <a name="open-three-browser-tabs"></a>Öppna tre flikar i webbläsaren
Hitta web app bot och öppna den i Azure-portalen. Tre olika vyer av web app bot använder du följande steg. Det kan vara praktiskt att ha tre separata flikar som öppnar i webbläsaren: 
  
>  * Testa i webbchatt
>  * Redigerare för build/öppna online-> fliklängd App Service
>  * App Service Editor/öppna Kudu-konsolen -> diagnostiska konsol

## <a name="modify-basicluisdialogcs-code"></a>Ändra BasicLuisDialog.cs kod

1. I den **App Service Editor** flik i webbläsaren, öppna den `BasicLuisDialog.cs` filen.

2. Lägg till följande NuGet-beroendet under den befintliga `using` rader:

   [!code-csharp[Add using statement](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=11-12 "Add using statement")]

3. Lägg till den `LogToApplicationInsights` funktionen:

   [!code-csharp[Add the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=61-92 "Add the LogToApplicationInsights function")]

    Application Insights instrumentation nyckeln är redan i web app bot programmet inställning med namnet `BotDevInsightsKey`. 

    Den sista raden i funktionen lägger till data till Application Insights. Namnet på den spårningsprovider är `LUIS`, ett unikt namn förutom andra telemetridata som samlas in av den här web app bot. Alla egenskaper är också med prefixet `LUIS_` så att du kan se vilka data den här självstudiekursen lägger till jämfört med information som ges av web app bot.

4. Anropa den `LogToApplicationInsights` funktionen överst i den `ShowLuisResult` funktionen:

   [!code-csharp[Use the LogToApplicationInsights function](~/samples-luis/documentation-samples/tutorial-web-app-bot-application-insights/csharp/BasicLuisDialog.cs?range=114-115 "Use the LogToApplicationInsights function")]

## <a name="build-web-app-bot"></a>Skapa web app bot
1. Skapa web app bot på något av två sätt. Den första metoden är att högerklicka på `build.cmd` i den **App Service Editor**och välj **köra från konsolen**. Utdata från konsolen visar och är klar med `Finished successfully.`

2. Om detta inte slutförs korrekt så måste du öppna konsolen, gå till skriptet och kör den med hjälp av följande steg. I den **App Service Editor**, Välj namnet på din bot i det översta blå fältet och markerar sedan **öppna Kudu-konsolen** i den nedrullningsbara listan.

    ![Öppna Kudu-konsolen](./media/luis-tutorial-bot-csharp-appinsights/open-kudu-console.png)

3. Ange följande kommando i konsolfönstret:

    ```
    cd site\wwwroot && build.cmd
    ```

    Vänta tills build att slutföra med `Finished successfully.`

## <a name="test-the-web-app-bot"></a>Testa web app bot

1. Testa din web app bot genom att öppna den **testa i Web chatta** funktion i portalen. 

2. Ange frasen `Coffee bar on please`.  

    ![Testa web app bot i chatt](./media/luis-tutorial-bot-csharp-appinsights/test-in-web-chat.png)

3. Du bör se någon skillnad i chatbot svaret. Ändringen är inte i bot svar skickar data till Application Insights. Ange några fler utterances så att det finns lite mer data i Application Insights:

```
Please deliver a pizza
Turn off all the lights
Turn on the hall light
```

## <a name="view-luis-entries-in-application-insights"></a>Visa THOMAS poster i Application Insights
Öppna Application Insights för att se THOMAS transaktioner. 

1. I portalen, Välj **alla resurser** sedan filtrera efter bot webbprogramnamnet. Klicka på resursen med typen **Programinsikter**. Ikonen för Application Insights är pratbubblan. 

    ![Sök efter appen insikter](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights.png)

2. När resursen öppnas klickar du på den **Sök** ikonen förstoringsglas på panelen längst till höger. En ny panel till rätt visar. Beroende på hur mycket telemetridata hittas, panelen kan ta dig tid att visa. Sök efter `LUIS`. Listan minskar med bara THOMAS frågeresultat lagts till med den här kursen.

    ![Sök efter spårningar](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace.png)

3. Markera den översta posten. Ett nytt fönster visar mer detaljerad data, inklusive anpassade data för THOMAS frågan till längst till höger. Innehåller information som övre avsikten och dess resultat.

    ![Granska trace objekt](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-trace-item.png)

    När du är klar väljer du upp längst till höger **X** att återgå till listan över beroende objekt. 


> [!Tip]
> Om du vill spara beroendelistan och återgår till den senare klickar du på **... Flera** och på **spara favorit**.

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Frågan Programinsikter för avsikt, poäng och utterance
Application Insights ger dig möjlighet att fråga efter data med den [Kusto](https://docs.microsoft.com/azure/application-insights/app-insights-analytics#query-data-in-analytics) språk, samt exportera den till [PowerBI](https://powerbi.microsoft.com). 

1. Klicka på **Analytics** överst i beroendet lista över filterfältet. 

    ![Knappen Analytics](./media/luis-tutorial-bot-csharp-appinsights/portal-service-list-app-insights-search-luis-analytics-button.png)

2. Med ett frågefönster överst och fönstret med tabellen nedan som öppnas ett nytt fönster. Om du har använt databaser innan är den här ordningen bekant. Frågan innehåller alla objekt från de senaste 24 timmarna som börjar med namnet `LUIS`. Den **CustomDimensions** kolumnen har THOMAS frågeresultat som namn/värde-par.

    ![Analytics standardrapport](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-1.png)

3. Om du vill dra ut översta avsikt, poäng och utterance, lägger du till följande ovanför den sista raden i frågefönstret:

    ```SQL
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

4. Kör frågan. Rulla längst till höger i en tabell. De nya kolumnerna topIntent, poäng och utterance är tillgängliga. Klicka på kolumnen topIntent för att sortera.

    ![Anpassade analytics-rapport](./media/luis-tutorial-bot-csharp-appinsights/analytics-query-2.png)


Lär dig mer om den [Kusto frågespråk](https://docs.loganalytics.io/docs/Learn/Getting-Started/Getting-started-with-queries) eller [exportera data till PowerBi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Mer information om Bot Framework
Lär dig mer om [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Nästa steg

Annan information som du kanske vill lägga till application insights-data innehåller app-ID, versions-ID, senaste ändringsdatum för modellen, datum för senaste train, senast publiceringsdatum. Dessa värden kan antingen hämtas från slutpunkts-URL (app-ID och versions-ID) eller från en [redigering API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c3d) anropa sedan i bot webbappsinställningarna och hämtas därifrån.  

Om du använder samma endpoint-prenumeration för mer än en THOMAS app, bör du också inkludera prenumerations-ID och en egenskap om att det är en delad nyckel. 

> [!div class="nextstepaction"]
> [Mer information om exempel utterances](luis-how-to-add-example-utterances.md)

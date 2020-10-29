---
title: Övervaka din ASP.NET-livewebbapp med Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att snabbt konfigurera en ASP.NET-webbapp för övervakning med Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/26/2019
ms.custom: mvc
ms.openlocfilehash: dd262b942d6ce4e423f524c7381574afd501f466
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/29/2020
ms.locfileid: "92927557"
---
# <a name="start-monitoring-your-aspnet-web-application"></a>Börja övervaka din ASP.NET-webbapp

Med Azure Application Insights kan du enkelt övervaka en webbapp för tillgänglighet, prestanda och användning.  Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem.  Med hjälp av informationen du samlar in från Application Insights om appens prestanda och effektivitet kan du göra välgrundade val för att underhålla och förbättra ditt program.

Den här snabbstarten visar hur du lägger till Application Insights till en befintlig ASP.NET-webbapp och börjar analysera livestatistik. Det är bara en av de olika metoderna du kan använda för att analysera din app. Om du inte har ett ASP.NET-webbprogram kan du skapa ett som följer snabb starten för att [skapa en ASP.NET-webbapp](../../app-service/quickstart-dotnet-framework.md).

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här snabbstarten behöver du:

- Installera [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) med följande arbets belastningar:
    - ASP.NET och webbutveckling
    - Azure Development


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="enable-application-insights"></a>Aktivera Application Insights

1. Öppna projektet i Visual Studio 2019.
2. Välj **Configure Application Insights** (Konfigurera Application Insights) på projektmenyn. Visual Studio lägger till Application Insights SDK för programmet.

    > [!IMPORTANT]
    > Processen för att lägga till Application Insights varierar beroende på malltypen i ASP.NET. Om du använder mallen **Tom** eller **Azure-mobilapp** väljer du **Projekt** > **Lägg till Application Insights Telemetry** . För alla andra ASP.NET-mallar läser du anvisningarna i steget ovan. 

3. Klicka på **Get Started** (Kom igång) (tidigare versioner av Visual Studio har knappen **Start Free** (Börja kostnadsfritt) i stället).

    ![Lägga till Application Insights i Visual Studio](./media/quick-monitor-portal/add-application-insights-b.png)

4. Välj din prenumeration och klicka på **Registrera** .

5. Välj **projekt**  >  **Hantera NuGet paket**  >  **Källa: NuGet.org**  >  **Uppdatera** Application Insights SDK-paket till den senaste stabila versionen.

6. Kör ditt program genom att antingen välja alternativet för att **starta felsökning** på menyn **Felsök** eller genom att trycka på tangenten F5.

## <a name="confirm-app-configuration"></a>Bekräfta appkonfiguration

Application Insights samlar in telemetridata för tillämpningsprogrammet oavsett var det körs. Gör så här om du vill börja granska dessa data:

1. Öppna Application Insights genom att klicka på **Visa**  ->  **andra Windows**  ->  **Application Insights Sök** .  Du kan se telemetrin från den aktuella sessionen.<BR><br>![Telemetri i Visual Studio](./media/quick-monitor-portal/telemetry-in-vs.png)

2. Klicka på den första begäran i listan (GET Home/Index i det här exemplet) för att se mer information. Observera att både statuskoden och svarstiden ingår tillsammans med annan värdefull information om begäran.<br><br>![Svarsinformation i Visual Studio](media/quick-monitor-portal/request-details.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

Nu kan du öppna Application Insights i Azure-portalen för att visa olika detaljer om programmet som körs.

1. Expandera mappen **anslutna tjänster** (moln-och plugin-ikonen) i Solution Explorer högerklicka sedan på mappen **Application Insights** och klicka på **Öppna Application Insights Portal** .  Du ser viss information om ditt program och många andra alternativ.

    ![Skärm bild som visar information om programmet i Application Insights-portalen.](media/quick-monitor-portal/04-overview.png)

2. Klicka på **Programkarta** för en visuell layout av beroenden mellan programkomponenterna.  Varje komponent visas KPI: er som belastning, prestanda, fel och varningar.

    ![Programkarta](media/quick-monitor-portal/05-appmap.png)

3. Klicka på ikonen för **app Analytics** -ikonen ![ för app Analytics-ikonen.](media/quick-monitor-portal/app-viewinlogs-icon.png) **Visa i loggar (analys)** på en av program komponenterna. Detta öppnar **loggar (analyser)** , som innehåller ett omfattande frågespråk för att analysera alla data som samlas in av Application Insights. I det här fallet skapas en fråga som återger antalet begäranden som ett diagram. Du kan skriva egna frågor för att analysera andra data.

    ![Analys](media/quick-monitor-portal/6viewanalytics.png)

4. Klicka på **Live Metrics Stream** till vänster under Undersök. Här ser du livestatistik om programmet när medan det körs. Det innehåller information som antalet inkommande begäranden, varaktigheten för dessa begäranden och fel som inträffar. Du kan även granska kritiska prestandavärden, till exempel processor och minne.

    ![Direktsänd ström](media/quick-monitor-portal/7livemetrics.png)

    Om du är redo att vara värd för programmet i Azure kan publicera den nu. Följ stegen i informationen om att [skapa en snabbstart för ASP.NET-webbappen](../../app-service/quickstart-dotnetcore.md#update-the-app-and-redeploy).

5. Om du använder Visual Studio för att lägga till Application Insights-övervakning kan du automatiskt lägga till övervakning på klientsidan. För att lägga till övervakning på klientsidan manuellt till ett program lägger du till följande JavaScript för ditt program:

```html
<!-- 
To collect user behavior analytics about your application, 
insert the following script into each page you want to track.
Place this code immediately before the closing </head> tag,
and before any other scripts. Your first data will appear 
automatically in just a few seconds.
-->
<script type="text/javascript">
var appInsights=window.appInsights||function(a){
  function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
  }({
      instrumentationKey:"<your instrumentation key>"
  });

window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
</script>
```

Mer information finns på GitHub-lagringsplatsen för vår [JavaScript-SDK med öppen källkod](https://github.com/Microsoft/ApplicationInsights-JS).

## <a name="clean-up-resources"></a>Rensa resurser
När du är färdig med testningen kan du ta bort resurs gruppen och alla relaterade resurser. Följ stegen nedan.
1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup** .
2. På sidan resurs grupp klickar du på **ta bort** , skriver **myResourceGroup** i text rutan och klickar sedan på **ta bort** .

## <a name="next-steps"></a>Nästa steg
I den här snabb starten har du aktiverat ditt program för övervakning genom att Azure Application insikter.  Fortsätt till självstudierna för att lära dig att använda övervakningsstatistik och identifiera problem i programmet.

> [!div class="nextstepaction"]
> [Azure Application Insights-självstudier](tutorial-runtime-exceptions.md)

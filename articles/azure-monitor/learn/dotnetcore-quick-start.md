---
title: Snabbstart med Azure Application Insights | Microsoft Docs
description: Innehåller instruktioner för att snabbt konfigurera en ASP.NET Core-Webbapp för övervakning med Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 03/29/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: eae9dc6447dd8211a3919c52beaea64274fc0ec5
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801087"
---
# <a name="start-monitoring-your-aspnet-core-web-application"></a>Börja övervaka din ASP.NET Core-webbapp

Med Azure Application Insights kan du enkelt övervaka en webbapp med avseende på tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. 

Den här snabbstarten hjälper dig att lägga till Application Insights SDK i en befintlig ASP.NET Core-webbapp. 

## <a name="prerequisites"></a>Förutsättningar

För att slutföra den här snabbstarten behöver du:

- [Installera Visual Studio 2017](https://www.visualstudio.com/downloads/) med följande arbetsbelastningar:
  - ASP.NET och webbutveckling
  - Azure Development
- [Installera .NET Core 2.0 SDK](https://www.microsoft.com/net/core)
- Du behöver en Azure-prenumeration och en befintlig .NET Core-webbapp.

Om du inte har en ASP.NET Core-webbapp kan du använda vår stegvisa instruktioner för hur [skapa en ASP.NET Core-app och lägga till Application Insights.](../../azure-monitor/app/asp-net-core.md)

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla internetanslutna appar, oavsett om de körs lokalt eller i molnet. Gör så här om du vill börja granska dessa data:

1. Välj **Skapa en resurs** > **Utvecklarverktyg** > **Application Insights**.

   ![lägg till en Application Insights-resurs](./media/dotnetcore-quick-start/1createresourceappinsight.png)

    En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

    | Inställningar        |  Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Programtyp** | ASP.NET-webbapp | Typen av app du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resursgrupp som är värd för App Insights-data |
   | **Plats** | Östra USA | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.

## <a name="configure-app-insights-sdk"></a>Konfigurera App Insights SDK

1. Öppna **projektet** för ASP.NET Core-webbappen i Visual Studio > högerklicka på appnamnet i **Solution Explorer** > välj **Lägg till** > **Application Insights Telemetry**.

    ![lägg till Application Insights Telemetry](./media/dotnetcore-quick-start/2vsaddappinsights.png)

2. Klicka på den **börjar** knappen

3. Välj ditt konto och prenumeration > Välj den **befintliga resurs** du skapade i Azure-portalen > Klicka på **registrera**.

4. Välj **Felsök** > **Starta utan felsökning** (Ctrl + F5) för att starta appen.

    ![översiktsvyn för Application Insights](./media/dotnetcore-quick-start/3debug.png)

> [!NOTE]
> Det tar 3–5 minuter innan data börjar visas i portalen. Om appen är en testapp för lågtrafik bör du komma ihåg att de flesta mått endast registreras när det finns aktiva begäranden eller åtgärder.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Öppna Application Insights **översikt** sidan på Azure portal genom att välja **Start** och markera den resurs du skapade tidigare, för att visa information om som körs under de senaste resurser programmet.

   ![översiktsvyn för Application Insights](./media/dotnetcore-quick-start/4overview.png)

2. Klicka på **Programkarta** om du vill se en layout med beroendena mellan appens komponenter. För varje komponent visas KPI:er som belastning, prestanda, fel och varningar.

   ![Programkarta](./media/dotnetcore-quick-start/5appmap.png)

3. Klicka på den **Appanalys** ikonen ![Programkartan ikonen](./media/dotnetcore-quick-start/006.png) **visa i analys**. Då öppnas **Application Insights Analytics**, med ett funktionsrikt frågespråk för att analysera alla data som samlas in av Application Insights. I det här fallet skapas en fråga som återger antalet begäranden som ett diagram. Du kan skriva egna frågor för att analysera andra data.

   ![analysdiagram över användarbegäranden under en viss tidsperiod](./media/dotnetcore-quick-start/6analytics.png)

4. Gå tillbaka till sidan **Översikt** och undersök KPI-instrumentpanelerna.  På den här instrumentpanelen visas statistik om appens hälsotillstånd, inklusive antalet inkommande begäranden, varaktigheten för dessa begäranden och fel som inträffar. 

   ![diagram med hälsotillståndsöversiktens tidslinje](./media/dotnetcore-quick-start/7kpidashboards.png)

   Om du vill att diagrammet **Inläsningstid för sidvisning** ska fyllas i med **telemetridata på klientsidan** lägger du till det här skriptet på varje sida du vill spåra:

   ```HTML
   <!-- 
   To collect user behavior analytics about your application, 
   insert the following script into each page you want to track.
   Place this code immediately before the closing </head> tag,
   and before any other scripts. Your first data will appear 
   automatically in just a few seconds.
   -->
   <script type="text/javascript">
     var appInsights=window.appInsights||function(config){
       function i(config){t[config]=function(){var i=arguments;t.queue.push(function(){t[config].apply(t,i)})}}var t={config:config},u=document,e=window,o="script",s="AuthenticatedUserContext",h="start",c="stop",l="Track",a=l+"Event",v=l+"Page",y=u.createElement(o),r,f;y.src=config.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js";u.getElementsByTagName(o)[0].parentNode.appendChild(y);try{t.cookie=u.cookie}catch(p){}for(t.queue=[],t.version="1.0",r=["Event","Exception","Metric","PageView","Trace","Dependency"];r.length;)i("track"+r.pop());return i("set"+s),i("clear"+s),i(h+a),i(c+a),i(h+v),i(c+v),i("flush"),config.disableExceptionTracking||(r="onerror",i("_"+r),f=e[r],e[r]=function(config,i,u,e,o){var s=f&&f(config,i,u,e,o);return s!==!0&&t["_"+r](config,i,u,e,o),s}),t
       }({
           instrumentationKey:"<insert instrumentation key>"
       });
       
       window.appInsights=appInsights;
       appInsights.trackPageView();
   </script>
   ```

5. På den vänstra klickar du på **mått**. Använd metrics explorer för att undersöka hälsa och användning av din resurs. Du kan klicka på **Lägg till nytt diagram** om du vill skapa fler anpassade vyer eller välja **Redigera** för att ändra befintliga diagramtyper, höjd, färgpalett, grupperingar och mått. Du kan till exempel göra ett diagram som visar den genomsnittliga webbsideinläsningar genom att välja ”webbsideinläsningar” från mått, listruta och ”medel” från aggregering. Mer information om Azure Metrics Explorer besök [komma igång med Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

     ![Fliken mått: Genomsnittlig webbläsare sidan belastningen diagrammets](./media/dotnetcore-quick-start/8metrics.png)

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar testning bör du ta bort resursgruppen och alla relaterade resurser. Att så att följa stegen nedan.

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver **myResourceGroup** i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera körningsundantag](https://docs.microsoft.com/azure/application-insights/app-insights-tutorial-runtime-exceptions)

---
title: 'Snabb start: övervaka webbplatser med Azure Monitor Application Insights'
description: I den här snabb starten får du lära dig hur du konfigurerar övervakning av webbplatser på klient-och webb sidan med Azure Monitor Application Insights.
ms.topic: quickstart
ms.date: 07/15/2019
ms.custom: mvc
ms.openlocfilehash: eb6c0a8319257949cee8f35be6cdfac22f1fe5d4
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/28/2020
ms.locfileid: "87323442"
---
# <a name="quickstart-start-monitoring-your-website-with-azure-monitor-application-insights"></a>Snabb start: börja övervaka din webbplats med Azure Monitor Application Insights

I den här snabb starten lär du dig att lägga till Application Insights Java Script SDK med öppen källkod på din webbplats. Du lär dig också hur du bättre förstår klient-och webb läsar upplevelsen för besökare på din webbplats.

Med Azure Monitor Application Insights kan du enkelt övervaka din webbplats med avseende på tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. Application Insights tillhandahåller övervakning på både server sidan och klient-och webb läsar sidan.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* En webbplats där du kan lägga till Application Insights JavaScript SDK.

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla Internet-anslutna program som körs lokalt eller i molnet. Använd följande steg för att visa dessa data:

1. Logga in på [Azure-portalen](https://portal.azure.com/).
1. Välj **skapa ett resurs**  >  **hanterings verktyg**  >  **Application Insights**.

   > [!NOTE]
   >Om det här är första gången du skapar en Application Insights-resurs, se [skapa en Application Insights resurs](./create-new-resource.md).
1. När konfigurations rutan visas, använder du följande tabell för att fylla i inmatade fält:

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namnet som identifierar den app som du övervakar. |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resurs gruppen som ska vara värd för Application Insights data. Du kan skapa en ny resursgrupp eller välja en befintlig. |
   | **Plats** | East US | Välj en plats nära dig eller nära den plats där din app finns. |
1. Välj **Skapa**.

## <a name="create-an-html-file"></a>Skapa en HTML-fil

1. På din lokala dator skapar du en fil med namnet ``hello_world.html``. I det här exemplet skapar du filen i roten på enhet C så att den ser ut ``C:\hello_world.html`` .
1. Kopiera och klistra in följande skript i ``hello_world.html`` :

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations, visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-application-insights-sdk"></a>Konfigurera Application Insights SDK

1. Välj **Översikt**  >  **Essentials**och kopiera sedan programmets **Instrumentation-nyckel**.

   ![Nytt Application Insights-resursformulär](media/website-monitoring/instrumentation-key-001.png)

1. Lägg till följande skript i ``hello_world.html`` filen före den avslutande ``</head>`` taggen:

   ```javascript
   <script type="text/javascript">
      var sdkInstance="appInsightsSDK";window[sdkInstance]="appInsights";var aiName=window[sdkInstance],aisdk=window[aiName]||function(e){function n(e){t[e]=function(){var n=arguments;t.queue.push(function(){t[e].apply(t,n)})}}var t={config:e};t.initialize=!0;var i=document,a=window;setTimeout(function(){var n=i.createElement("script");n.src=e.url||"https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js",i.getElementsByTagName("script")[0].parentNode.appendChild(n)});try{t.cookie=i.cookie}catch(e){}t.queue=[],t.version=2;for(var r=["Event","PageView","Exception","Trace","DependencyData","Metric","PageViewPerformance"];r.length;)n("track"+r.pop());n("startTrackPage"),n("stopTrackPage");var s="Track"+r[0];if(n("start"+s),n("stop"+s),n("setAuthenticatedUserContext"),n("clearAuthenticatedUserContext"),n("flush"),!(!0===e.disableExceptionTracking||e.extensionConfig&&e.extensionConfig.ApplicationInsightsAnalytics&&!0===e.extensionConfig.ApplicationInsightsAnalytics.disableExceptionTracking)){n("_"+(r="onerror"));var o=a[r];a[r]=function(e,n,i,a,s){var c=o&&o(e,n,i,a,s);return!0!==c&&t["_"+r]({message:e,url:n,lineNumber:i,columnNumber:a,error:s}),c},e.autoExceptionInstrumented=!0}return t}(
      {
         instrumentationKey:"INSTRUMENTATION_KEY"
      }
      );window[aiName]=aisdk,aisdk.queue&&0===aisdk.queue.length&&aisdk.trackPageView({});
   </script>
   ```

1. Redigera ``hello_world.html`` och Lägg till din instrumentationsnyckel.

1. Öppna ``hello_world.html`` i en lokal webbläsarsession. Den här åtgärden skapar en enskild sid visning. Du kan uppdatera din webbläsare om du vill generera flera vyer på prov sidan.

## <a name="monitor-your-website-in-the-azure-portal"></a>Övervaka din webbplats i Azure Portal

1. Öppna sidan Application Insights **Översikt** i Azure Portal för att visa information om ditt program som körs. På sidan **Översikt** har du hämtat din Instrumentation-nyckel.

   De fyra standarddiagrammen på översiktssidan är begränsade till programdata på serversidan. Eftersom vi instrumenterar klient-och webb läsar interaktioner med Java Script SDK, gäller inte den här vyn om vi även har installerat SDK för Server sidan.

1. Välj ikonen **analys** ![ program karta ](media/website-monitoring/006.png) .  Den här åtgärden öppnar **analys**, som innehåller ett omfattande frågespråk för analys av alla data som samlas in av Application Insights. Kör följande fråga om du vill visa data som är relaterade till förfrågningar på klient sidan:

    ```kusto
    // average pageView duration by name
    let timeGrain=1s;
    let dataset=pageViews
    // additional filters can be applied here
    | where timestamp > ago(15m)
    | where client_Type == "Browser" ;
    // calculate average pageView duration for all pageViews
    dataset
    | summarize avg(duration) by bin(timestamp, timeGrain)
    | extend pageView='Overall'
    // render result in a chart
    | render timechart
    ```

   ![analysdiagram över användarbegäranden under en viss tidsperiod](./media/website-monitoring/analytics-query.png)

1. Gå tillbaka till sidan **Översikt**. Under **Undersök** rubriken väljer du **webbläsare**och sedan **prestanda**.  Mått som rör webbplatsens prestanda visas. Det finns en motsvarande vy för att analysera fel och undantag på din webbplats. Du kan välja **exempel** för att få åtkomst till [slut punkt till slut punkts transaktions information](./transaction-diagnostics.md).

   ![Diagram för servermått](./media/website-monitoring/browser-performance.png)

1. Gå till huvud Application Insights-menyn och välj [**användare**](./usage-segmentation.md) under **användnings** rubriken för att börja utforska [verktygen för analys av användar beteende](./usage-overview.md). Eftersom vi testar från en enda dator ser vi bara data för en användare. För en Live-webbplats kan distributionen av användare se ut så här:

     ![Användardiagram](./media/website-monitoring/usage-users.png)

1. För en mer komplex webbplats med flera sidor kan du använda [**användarflöden**](./usage-flows.md) -verktyget för att spåra den väg besökarna tar genom de olika delarna av webbplatsen.

   ![Visualisering av användarflöden](./media/website-monitoring/user-flows.png)

Mer avancerade konfigurationer för övervakning av webbplatser finns i [Java Script SDK API-referensen](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta arbeta med ytterligare snabb starter eller självstudier ska du inte rensa upp resurserna som du skapade i den här snabb starten. Annars kan du använda följande steg för att ta bort alla resurser som skapats i den här snabb starten i Azure Portal.

> [!NOTE]
> Om du använde en befintlig resurs grupp fungerar inte följande instruktioner. I stället kan du bara ta bort den enskilda Application Insights resursen. Tänk på att när du tar bort en resurs grupp tas alla underyling-resurser som är medlemmar i gruppen också bort.

1. Välj **resurs grupper**på den vänstra menyn på Azure Portal och välj sedan **myResourceGroup** eller namnet på den tillfälliga resurs gruppen.
1. På sidan resurs grupp väljer du **ta bort**, anger **myResourceGroup** i text rutan och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera prestandaproblem](../log-query/log-query-overview.md)


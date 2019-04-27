---
title: Snabbstart – Övervaka webbplatser med Azure Monitor Application Insights | Microsoft Docs
description: Innehåller instruktioner för att snabbt konfigurera webbplatsövervakning på klient-/webbläsarsidan med Azure Monitor Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/29/2018
ms.service: application-insights
ms.custom: mvc
ms.topic: quickstart
manager: carmonm
ms.openlocfilehash: a35f4d8c1d5bf5943ecba02ff262fbc7fc0730fe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783881"
---
# <a name="start-monitoring-your-website"></a>Börja övervaka din webbplats

Med Azure Monitor Application Insights kan du enkelt övervaka din webbplats med avseende på tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. Application Insights erbjuder funktioner för övervakning både på serversidan samt på klient-/webbläsarsidan.

Den här snabbstarten vägleder dig genom att lägga till [Application Insights JavaScript SDK med öppen källkod](https://github.com/Microsoft/ApplicationInsights-JS) så att du förstår upplevelsen på klient-/webbläsarsidan för användarna på din webbplats.

## <a name="prerequisites"></a>Nödvändiga komponenter

För att slutföra den här snabbstarten behöver du:

- Du behöver en Azure-prenumeration.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla Internetanslutna appar som körs lokalt eller i molnet. Gör så här om du vill börja granska dessa data:

1. Välj **Skapa en resurs** > **Hanteringsverktyg** > **Application Insights**.

   En konfigurationsruta visas. Använd följande tabell när du ska fylla i indatafälten.

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Programtyp** | Allmänt program | Typen av app du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namnet på den nya resursgrupp som är värd för App Insights-data |
   | **Plats** | Östra USA | Välj en plats nära dig eller nära där appen finns |

2. Klicka på **Skapa**.

## <a name="create-an-html-file"></a>Skapa en HTML-fil

1. På din lokala dator skapar du en fil med namnet ``hello_world.html``. För det här exemplet placeras filen i roten på C:-enheten i ``C:\hello_world.html``.
2. Kopiera skriptet nedan till ``hello_world.html``:

    ```html
    <!DOCTYPE html>
    <html>
    <head>
    <title>Azure Monitor Application Insights</title>
    </head>
    <body>
    <h1>Azure Monitor Application Insights Hello World!</h1>
    <p>You can use the Application Insights JavaScript SDK to perform client/browser-side monitoring of your website. To learn about more advanced JavaScript SDK configurations visit the <a href="https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md" title="API Reference">API reference</a>.</p>
    </body>
    </html>
    ```

## <a name="configure-app-insights-sdk"></a>Konfigurera App Insights SDK

1. Välj **Översikt** > **Essentials** > kopiera appens **instrumenteringsnyckel**.

   ![Nytt App Insights-resursformulär](media/website-monitoring/instrumentation-key-001.png)

2. Lägg till följande skript till ``hello_world.html`` före den avslutande ``</head>``-taggen:

   ```javascript
      <script type="text/javascript">
        var appInsights=window.appInsights||function(a){
            function b(a){c[a]=function(){var b=arguments;c.queue.push(function(){c[a].apply(c,b)})}}var c={config:a},d=document,e=window;setTimeout(function(){var b=d.createElement("script");b.src=a.url||"https://az416426.vo.msecnd.net/scripts/a/ai.0.js",d.getElementsByTagName("script")[0].parentNode.appendChild(b)});try{c.cookie=d.cookie}catch(a){}c.queue=[];for(var f=["Event","Exception","Metric","PageView","Trace","Dependency"];f.length;)b("track"+f.pop());if(b("setAuthenticatedUserContext"),b("clearAuthenticatedUserContext"),b("startTrackEvent"),b("stopTrackEvent"),b("startTrackPage"),b("stopTrackPage"),b("flush"),!a.disableExceptionTracking){f="onerror",b("_"+f);var g=e[f];e[f]=function(a,b,d,e,h){var i=g&&g(a,b,d,e,h);return!0!==i&&c["_"+f](a,b,d,e,h),i}}return c
        }({
            instrumentationKey: "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx"
        });
        
        window.appInsights=appInsights,appInsights.queue&&0===appInsights.queue.length&&appInsights.trackPageView();
   </script>
   ```

3. Redigera ``hello_world.html`` och Lägg till din instrumentationsnyckel.

4. Öppna ``hello_world.html`` i en lokal webbläsarsession. Detta skapar en enda sidvisning. Du kan uppdatera webbläsaren för att generera flera testsidvisningar.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna sidan **Översikt** i Application Insights i Azure-portalen, där du hämtade instrumenteringsnyckeln, för att visa information om den app som körs. De fyra standarddiagrammen på översiktssidan är begränsade till programdata på serversidan. Eftersom vi instrumenterar interaktioner på klient-/webbläsarsidan med JavaScript SDK gäller inte just den här vyn såvida vi inte även har en serverdels-SDK installerad.

2. Klicka på ![ikonen för programkarta](media/website-monitoring/006.png) **Analys**.  Då öppnas **Analys**, med ett funktionsrikt frågespråk för att analysera alla data som samlas in av Application Insights. Visa data relaterade till klientsidans webbläsarbegäranden genom att köra följande fråga:

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

3. Gå tillbaka till sidan **Översikt**. Klicka på **Webbläsare** under rubriken **Undersök** och välj sedan **Prestanda**. Här hittar du prestandamått för din webbplats. Det finns även en motsvarande vy för att analysera fel och undantag på webbplatsen. Du kan klicka på **Exempel** för att visa detaljer för enskilda transaktioner. Härifrån kan du komma åt upplevelsen [transaktionsinformation från början till slut](../../azure-monitor/app/transaction-diagnostics.md).

   ![diagram med servermått](./media/website-monitoring/browser-performance.png)

4. För att börja utforska [analysverktygen för användarbeteende](../../azure-monitor/app/usage-overview.md) går du till Application Insights-huvudmenyn och väljer [**Användare**](../../azure-monitor/app/usage-segmentation.md) under rubriken **Användning**. Eftersom vi testar från en enskild dator ser vi bara data för en användare. För en webbplats som är live kan fördelningen av användare se ut på följande sätt:

     ![Användardiagram](./media/website-monitoring/usage-users.png)

5. Om vi hade instrumenterat en mer komplex webbplats med flera sidor skulle ett annat användbart verktyg vara [**Användarflöden**](../../azure-monitor/app/usage-flows.md). Med **Användarflöden** kan du spåra den väg som besökare tar via de olika delarna på webbplatsen.

   ![Visualisering av användarflöden](./media/website-monitoring/user-flows.png)

Om du vill lära dig mer om avancerade konfigurationer för att övervaka webbplatser kan du läsa [JavaScript SDK API-referensen](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md).

## <a name="clean-up-resources"></a>Rensa resurser

Om du planerar att fortsätta att arbeta med efterföljande snabbstarter eller med självstudierna ska du inte rensa resurserna som skapas i denna snabbstart. Om du inte planerar att fortsätta följer du annars stegen nedan för att ta bort alla resurser som du skapade i den här snabbstarten på Azure-portalen.

1. På menyn till vänster i Azure Portal klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. På sidan med resursgrupper klickar du på **Ta bort**, skriver **myResourceGroup** i textrutan och klickar sedan på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera prestandaproblem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)

---
title: 'Snabb start: övervaka med Azure Application insikter'
description: Innehåller instruktioner för att snabbt konfigurera en Node.js-webbapp för övervakning med Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 23fdf326bd1d3deac56f138130c3767427d062e5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2019
ms.locfileid: "72894946"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Snabb start: börja övervaka Node. js-webbappen med Azure Application insikter

Denna snabbstart guidar dig genom processen att lägga till version 0.22 av Application Insights SDK för Node.js för en befintlig Node.js-webbapp.

Med Azure Application Insights kan du enkelt övervaka en webbapp för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. Med SDK-version 0.20 och framåt kan du övervaka vanliga tredjepartspaket, till exempel MongoDB, MySQL och Redis.

## <a name="prerequisites"></a>Krav

För att slutföra den här snabbstarten behöver du:

- Du behöver en Azure-prenumeration och en befintlig Node.js-webbapp.

Om du inte har en Node.js-webbapp kan du skapa en genom att följa [snabbstarten för att skapa en Node.js webbapp](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla internetanslutna appar, oavsett om de körs lokalt eller i molnet. Gör så här om du vill börja granska dessa data:

1. Välj **Skapa en resurs** > **Utvecklarverktyg** > **Application Insights**.

   ![Lägg till en Azure Application Insights-resurs](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Om det här är första gången du skapar en Application Insights-resurs kan du lära dig mer genom att gå till [skapa ett Application Insights resurs](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) dokument.

   En konfigurations sida visas. Använd följande tabell för att fylla i inmatade fält. 

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Programtyp** | Node.js-app | Typ av app som du övervakar |
   | **Plats** | USA, östra | Välj en plats nära dig eller nära där appen finns |

2. Välj **Skapa**.

## <a name="configure-app-insights-sdk"></a>Konfigurera App Insights SDK

1. Välj **Översikt** och kopiera ditt programs **Instrumentation-nyckel**.

   ![Visa Application Insights Instrumentation-nyckeln](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Lägg till Application Insights SDK för Node.js i appen. Från appens rotmapp kör du:

   ```bash
   npm install applicationinsights --save
   ```

3. Redigera appens första .js-fil och lägg till de två raderna under den översta delen av skriptet. Om du använder [appen Node.js-snabbstart](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs) ändrar du filen index.js. Ersätt &lt;instrumentation_key&gt; med ditt programs instrumentationsnyckel. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Starta om appen.

> [!NOTE]
> Det tar 3-5 minuter innan data börjar visas i portalen. Om appen är en testapp för lågtrafik bör du komma ihåg att de flesta måtten endast registreras när det förekommer aktiva begäranden eller åtgärder.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna sidan **Översikt** i Application Insights i Azure-portalen, där du hämtade instrumenteringsnyckeln, för att visa information om den app som körs.

   ![Application Insights översikts meny](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Välj **program karta** för en visuell layout av beroende relationerna mellan program komponenterna. För varje komponent visas KPI:er som belastning, prestanda, fel och varningar.

   ![Application Insights program karta](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Välj ikonen för **app Analytics** ![program kartans ikon](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) **vyn i Analytics**.  Då öppnas **Application Insights Analytics**, med ett funktionsrikt frågespråk för att analysera alla data som samlas in av Application Insights. I det här fallet skapas en fråga som återger antalet begäranden som ett diagram. Du kan skriva egna frågor för att analysera andra data.

   ![Application Insights Analytics-diagram](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Gå tillbaka till sidan **Översikt** och undersök KPI-diagrammen.  På den här instrumentpanelen visas statistik om appens hälsotillstånd, inklusive antalet inkommande begäranden, varaktigheten för dessa begäranden och fel som inträffar.

   ![Tids linje diagram för Application Insights Health-översikt](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

   Om du vill att diagrammet **Inläsningstid för sidvisning** ska fyllas i med **telemetridata på klientsidan** lägger du till det här skriptet på varje sida du vill spåra:

   ```HTML
   <!-- 
   To collect user behavior analytics tools about your application, 
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

5. Välj **mått**till vänster. Använd Mät Utforskaren för att undersöka hälso tillståndet och användningen av resursen. Du kan välja **Lägg till nytt diagram** för att skapa ytterligare anpassade vyer eller välja **Redigera** för att ändra befintliga diagram typer, höjd, färgpalett, grupperingar och mått. Du kan till exempel skapa ett diagram som visar den genomsnittliga webb sidans inläsnings tid genom att välja "webbläsare sid inläsnings tid" från List rutan mått och "AVG" från aggregation. Mer information om Azure Metrics Explorer finns i [komma igång med azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

   ![Diagram över Application Insights Server mått](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Mer information om hur du övervakar Node.js finns i [den ytterligare dokumentationen för App Insights Node.js](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du är färdig med testningen kan du ta bort resurs gruppen och alla relaterade resurser. Följ stegen nedan.

1. På menyn till vänster i Azure-portalen klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. På sidan resurs grupp väljer du **ta bort**, anger **myResourceGroup** i text rutan och väljer sedan **ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera prestandaproblem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)

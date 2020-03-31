---
title: 'Snabbstart: Övervaka nod.js med Azure Monitor Application Insights'
description: Innehåller instruktioner för att snabbt konfigurera en Node.js Web App för övervakning med Azure Monitor Application Insights
ms.subservice: application-insights
ms.topic: quickstart
author: mrbullwinkle
ms.author: mbullwin
ms.date: 07/12/2019
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 694d2ae529202223869fcbb2a084e32bccaedbf1
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77660231"
---
# <a name="quickstart-start-monitoring-your-nodejs-web-application-with-azure-application-insights"></a>Snabbstart: Börja övervaka ditt Node.js-webbprogram med Azure Application Insights

I den här snabbstarten lägger du till Application Insights SDK version 0.22 för Node.js i ett befintligt Node.js-webbprogram.

Med Azure Application Insights kan du enkelt övervaka en webbapp för tillgänglighet, prestanda och användning. Du kan också snabbt identifiera och diagnostisera fel i appen utan att vänta på att en användare rapporterar dem. Med SDK-version 0.20 och framåt kan du övervaka vanliga tredjepartspaket, till exempel MongoDB, MySQL och Redis.

## <a name="prerequisites"></a>Krav

* Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Ett fungerande Node.js-program.

## <a name="enable-application-insights"></a>Aktivera Application Insights

Application Insights kan samla in telemetridata från alla internetanslutna program, oavsett om det körs lokalt eller i molnet. Gör så här om du vill börja granska dessa data:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj **Skapa en resurs** > **Developer tools** > **Application Insights**.

   ![Lägga till en Azure Application Insights-resurs](./media/nodejs-quick-start/azure-app-insights-create-resource.png)

   > [!NOTE]
   >Om det är första gången du skapar en Application Insights-resurs kan du lära dig mer genom att besöka [doc-dokumentet Skapa en application insights-resurs.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

   En konfigurationssida visas. Använd följande tabell för att fylla i inmatningsfälten. 

    | Inställningar        | Värde           | Beskrivning  |
   | ------------- |:-------------|:-----|
   | **Namn**      | Globalt unikt värde | Namn som identifierar appen du övervakar |
   | **Resursgrupp**     | myResourceGroup      | Namn på den nya resursgruppen som ska vara värd för AppInsights-data. Du kan skapa en ny resursgrupp eller välja en befintlig. |
   | **Location** | USA, östra | Välj en plats nära dig eller nära där appen finns |

3. Välj **Skapa**.

## <a name="configure-appinsights-sdk"></a>Konfigurera AppInsights SDK

1. Välj **Översikt** och kopiera programmets **instrumenteringsnyckel**.

   ![Visa instrumenteringsnyckeln för programinsikter](./media/nodejs-quick-start/azure-app-insights-instrumentation-key.png)

2. Lägg till Application Insights SDK för Node.js i appen. Från appens rotmapp kör du:

   ```bash
   npm install applicationinsights --save
   ```

3. Redigera appens första *.js-fil* och lägg till de två raderna nedan i den översta delen av skriptet. Om du använder [snabbstartsappen Node.js](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-nodejs)ändrar du filen *index.js.* Ersätt `<instrumentation_key>` med programmets instrumenteringsnyckel. 

   ```JavaScript
   const appInsights = require('applicationinsights');
   appInsights.setup('<instrumentation_key>').start();
   ```

4. Starta om appen.

> [!NOTE]
> Det tar 3–5 minuter innan data börjar visas i portalen. Om appen är en testapp för lågtrafik bör du komma ihåg att de flesta måtten endast registreras när det förekommer aktiva begäranden eller åtgärder.

## <a name="start-monitoring-in-the-azure-portal"></a>Börja övervaka i Azure-portalen

1. Nu kan du öppna sidan **Översikt** i Application Insights i Azure-portalen, där du hämtade instrumenteringsnyckeln, för att visa information om den app som körs.

   ![Översiktsmenyn Application Insights](./media/nodejs-quick-start/azure-app-insights-overview-menu.png)

2. Välj **Programöversikt** för en visuell layout för beroenderelationerna mellan programkomponenterna. Varje komponent visas KPI: er som belastning, prestanda, fel och varningar.

   ![Programinsiktskarta för program](./media/nodejs-quick-start/azure-app-insights-application-map.png)

3. Välj ikonen ![](./media/nodejs-quick-start/azure-app-insights-analytics-icon.png) Programkarta för **programprogram** **i Analytics**.  Den här åtgärden öppnar **Application Insights Analytics**, som ger ett omfattande frågespråk för att analysera alla data som samlas in av Application Insights. I det här fallet skapas en fråga som återger antalet begäranden som ett diagram. Du kan skriva egna frågor för att analysera andra data.

   ![Programinsiktsanalysdiagram](./media/nodejs-quick-start/azure-app-insights-analytics-queries.png)

4. Gå tillbaka till sidan **Översikt** och undersök KPI-diagrammen.  På den här instrumentpanelen visas statistik om appens hälsotillstånd, inklusive antalet inkommande begäranden, varaktigheten för dessa begäranden och fel som inträffar.

   ![Tidslinjediagram för hälsoöversikt för programinsikter](./media/nodejs-quick-start/azure-app-insights-health-overview.png)

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

5. Till vänster väljer du **Mått**. Använd statistikutforskaren för att undersöka resursens hälsa och utnyttjande. Du kan välja **Lägg till nytt diagram** om du vill skapa ytterligare anpassade vyer eller välja **Redigera** om du vill ändra befintliga diagramtyper, höjd, färgpalett, grupperingar och mått. Du kan till exempel skapa ett diagram som visar den genomsnittliga inläsningstiden för webbläsarsidan genom att välja "Inläsningstid för webbläsarsidan" i listrutan mätvärden och "Medel" från aggregering. Mer information om Azure Metrics Explorer finns i [Komma igång med Azure Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md).

   ![Mätdiagram för programstatistikserver](./media/nodejs-quick-start/azure-app-insights-server-metrics.png)

Om du vill veta mer om övervakning nod.js, kolla in [ytterligare AppInsights Node.js dokumentation](../../azure-monitor/app/nodejs.md).

## <a name="clean-up-resources"></a>Rensa resurser

När du är klar med testningen kan du ta bort resursgruppen och alla relaterade resurser. För att göra detta följ stegen nedan.

> [!NOTE]
> Om du har använt en befintlig resursgrupp fungerar inte instruktionerna nedan och du måste bara ta bort den enskilda Application Insights-resursen. Tänk på att alla underylingsresurser som är medlemmar i gruppen tas bort när du tar bort en resursgrupp.

1. På menyn till vänster i Azure-portalen klickar du på **Resursgrupper** och sedan på **myResourceGroup**.
2. På resursgruppssidan väljer du **Ta bort,** anger **myResourceGroup** i textrutan och väljer sedan **Ta bort**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Hitta och diagnostisera prestandaproblem](https://docs.microsoft.com/azure/application-insights/app-insights-analytics)

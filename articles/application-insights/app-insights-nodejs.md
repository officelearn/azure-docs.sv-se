---
title: "Övervaka din Node.js-tjänster med Azure Application Insights | Microsoft Docs"
description: "Övervaka prestanda- och diagnostiseringsproblem i Node.js-tjänster med Application Insights."
services: application-insights
documentationcenter: nodejs
author: mrbullwinkle
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: mbullwin
ms.openlocfilehash: 5b8e35a953c936949af0d496345f537ad85fc359
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Övervaka dina Node-js-tjänster och -appar med Application Insights

[Azure Application Insights](app-insights-overview.md) övervakar dina serverdelstjänster och komponenter när du har distribuerat dem för att hjälpa dig att [upptäcka och snabbt diagnostisera problem med prestanda och annat](app-insights-detect-triage-diagnose.md). Du kan använda Application Insights för Node.js-tjänster som finns i ditt datacenter, i virtuella Azure-datorer och webbappar och även i andra offentliga moln.

För att ta emot, lagra, och utforska dina övervakade data inkluderar du SKD:t i koden och konfigurerar en motsvarande Application Insights-resurs i Azure. SDK:t skickar data till den resursen för ytterligare analys och undersökning.

Node.js-SDK:n kan automatiskt övervaka inkommande och utgående HTTP-begäran, undantag och vissa systemmått. Från och med version 0.20 kan SDK även övervaka vissa vanliga paket från tredje part, MongoDB, MySQL och Redis. Alla händelser som relaterar till en inkommande HTTP-begäran korreleras för snabbare felsökning.

Med TelemetryClient API kan du manuellt instrumentera och övervaka ytterligare aspekter av din app och ditt system. Vi beskriver TelemetryClient-API:n mer ingående senare i den här artikeln.

![Exempel på prestandaövervakningsdiagram](./media/app-insights-nodejs/10-perf.png)

## <a name="get-started"></a>Kom igång

Utför följande uppgifter för att konfigurera övervakning för en app eller tjänst.

### <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du se till att ha en Azure-prenumeration eller [så skaffar du en kostnadsfritt][azure-free-offer]. Om din organisation redan har en Azure-prenumeration kan en administratör följa [de här instruktionerna][add-aad-user] för att lägga till dig.

[azure-free-offer]: https://azure.microsoft.com/free/
[add-aad-user]: https://docs.microsoft.com/azure/active-directory/active-directory-users-create-azure-portal


### <a name="resource"></a> Konfigurera en Application Insights-resurs


1. Logga in på [Azure Portal][portal].
2. Välj **Skapa en resurs** > **Utvecklarverktyg** > **Application Insights**. Resursen innehåller en slutpunkt för att ta emot telemetridata, lagring för dessa data, sparade rapporter och instrumentpaneler, regel- och aviseringskonfiguration med mera.

  ![Skapa en Application Insights-resurs](./media/app-insights-nodejs/03-new_appinsights_resource.png)

3. På sidan där du skapar resursen väljer du **Node.js Application** i rutan **Programtyp**. Apptypen bestämmer vilken standardinstrumentpanel och rapporter som skapas. (Alla App Insights-resurser kan samla in data från alla språk och plattformar.)

  ![Nytt Application Insights-resursformulär](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="sdk"></a>Konfigurera Node.js-SDK:n

Inkludera SDK:n i din app så den kan samla in data. 

1. Kopiera resursens instrumenteringsnyckel (även kallad *ikey*) från Azure Portal. Application Insights använder ikey för att mappa data till Azure-resursen. Innan SDK:n kan använda din ikey måste du ange den i en miljövariabel eller i din kod.  

  ![Kopiera instrumenteringsnyckel](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

2. Lägg till Node.js-SDK-biblioteket till appens beroenden via package.json. Från appens rotmapp kör du:

  ```bash
  npm install applicationinsights --save
  ```

3. Läs uttryckligen in biblioteket i din kod. Eftersom SDK:n lägger in instrumentationen i många andra bibliotek ska du läsa in biblioteket så tidigt som möjligt, till och med före andra `require`-uttryck. 

  Överst i din första .js-fil lägger du till följande kod. Metoden `setup` konfigurerar nyckeln (och således Azure-resursen) som ska användas som standard för alla spårade objekt.

  ```javascript
  const appInsights = require("applicationinsights");
  appInsights.setup("<instrumentation_key>");
  appInsights.start();
  ```
   
  Du kan också tillhandahålla en ikey via miljövariabeln APPINSIGHTS\_INSTRUMENTATIONKEY istället för att skicka den manuellt till `setup()` eller `new appInsights.TelemetryClient()`. Med den här metoden kan du behålla ikeys utanför den allokerade källkoden och ange olika ikeys för olika miljöer.

  Ytterligare konfigurationsalternativ finns i följande avsnitt.

  Du kan testa SDK:n utan att skicka telemetri genom att ställa in `appInsights.defaultClient.config.disableAppInsights = true`.

### <a name="monitor"></a> Övervaka din app

SDK:n samlar automatiskt in telemetri om Node.js-körningen och några vanliga moduler från tredje part. Använd ditt program för att skapa vissa av dessa data.

På [Azure Portal][portal] går du sedan till Application Insights och öppnar den resurs som du skapade tidigare. I **Översiktstidslinje** letar du efter dina första datapunkter. Välj olika komponenter i schemana för att se mer detaljerade data.

![Första datapunkter](./media/app-insights-nodejs/12-first-perf.png)

Om du vill visa topologin som identifierats för din app markerar du knappen **Programavbildning**. Välj komponenter i kartan för att se mer information.

![Mappning av enskild app](./media/app-insights-nodejs/06-appinsights_appmap.png)

Läs mer om din app och felsök problem går du till avsnittet **UNDERSÖK** och väljer de andra tillgängliga vyerna.

![Avsnittet Undersök](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Ser du inga data?

Eftersom SDK:n slår ihop data för sändning kan det uppstå en fördröjning innan objekt visas i portalen. Om du inte ser några data i resursen kan du prova något av följande:

* Fortsätta att använda programmet. Vidta fler åtgärder för att generera mer telemetri.
* Klicka på **Uppdatera** i portalens resursvy. Diagram uppdaterar sig själva regelbundet, men när du trycker på uppdateringsknappen manuellt tvingas de att uppdatera genast.
* Verifiera att [nödvändiga utgående portar](app-insights-ip-addresses.md) är öppna.
* Använd [Sök](app-insights-diagnostic-search.md) för att söka efter specifika händelser.
* Se [Vanliga frågor][FAQ].


## <a name="sdk-configuration"></a>SDK-konfiguration

SDK:ns konfigurationsmetoder och standardvärden anges i följande kodexempel.

Ställ in `.setAutoDependencyCorrelation(true)` för att korrelera händelser i en tjänst fullständigt. När det alternativet är inställt kan SDK:n spåra innehåll över asynkrona återanrop i Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(true)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .setAutoCollectConsole(true)
    .setUseDiskRetryCaching(true)
    .start();
```

## <a name="telemetryclient-api"></a>TelemetryClient-API

En fullständig beskrivning av TelemetryClient-API:n finns i [Application Insights API for custom events and metrics](app-insights-api-custom-events-metrics.md) (Application Insights-API för anpassade händelser och mått).

Du kan spåra valfri begäran, händelse, mått eller undantag med Node.js-SDK:n i Application Insights. I följande kodexempel visas några av de tillgängliga API:erna som du kan använda:

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey is in env var
let client = appInsights.defaultClient;

client.trackEvent({name: "my custom event", properties: {customProperty: "custom property value"}});
client.trackException({exception: new Error("handled exceptions can be logged with this method")});
client.trackMetric({name: "custom metric", value: 3});
client.trackTrace({message: "trace message"});
client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL"});
client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true});

let http = require("http");
http.createServer( (req, res) => {
  client.trackNodeHttpRequest({request: req, response: res}); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Spåra dina beroenden

Du kan använda följande kod för att spåra dina beroenden:

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.defaultClient;

var success = false;
let startTime = Date.now();
// Execute dependency call here...
let duration = Date.now() - startTime;
success = true;

client.trackDependency({dependencyTypeName: "dependency name", name: "command name", duration: duration, success: success});
```

### <a name="add-a-custom-property-to-all-events"></a>Lägga till en anpassad egenskap i alla händelser

Använd följande kod för att lägga till en anpassad egenskap i alla händelser:

```javascript
appInsights.defaultClient.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Spåra HTTP GET-begäranden

Du kan använda följande kod för att spåra HTTP GET-begäranden:

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.defaultClient.trackNodeHttpRequest({request: req, response: res});
    }
    // Other work here...
    res.end();
});
```

### <a name="track-server-startup-time"></a>Spåra starttiden för server

Du kan använda följande kod för att spåra starttiden för servern:

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.defaultClient.trackMetric({name: "server startup time", value: duration});
});
```

## <a name="next-steps"></a>Nästa steg

* [Övervaka din telemetri i portalen](app-insights-dashboards.md)
* [Skriv Analytics-frågor via din telemetri](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[FAQ]: app-insights-troubleshoot-faq.md


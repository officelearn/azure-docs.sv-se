---
title: "Övervaka din Node.js-app med Azure Application Insights SDK | Microsoft Docs"
description: "Analysera användningen, tillgängligheten och prestanda i din lokala program eller Microsoft Azure-webbapp med Application Insights."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/23/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 46b829ce52994a5112494145a02e78859c5fae2d
ms.openlocfilehash: d4c7fa2058b1c07671329304c37630d2e6e8e8a7
ms.lasthandoff: 02/23/2017


---
# <a name="add-application-insights-sdk-to-monitor-your-nodejs-app"></a>Övervaka din Node.js-app genom att lägga till Application Insights SDK


Med [Azure Application Insights](app-insights-overview.md) kan du övervaka ditt liveprogram och [identifiera och diagnostisera prestandaproblem och undantag](app-insights-detect-triage-diagnose.md) och [se hur din app används](app-insights-overview-usage.md). Tjänsten kan användas med appar som finns på dina lokala IIS-servrar eller på virtuella datorer i Azure, samt för Azure-webbappar.

SDK tillhandahåller automatisk insamling av frekvens och svar för inkommande HTTP-förfrågningar, prestandaräknare (processor, minne, RPS) och ohanterade undantag. Du kan också lägga till anpassade anrop om du vill spåra beroenden, mätvärden och andra händelser.

![Exempel på prestandaövervakningsdiagram](./media/app-insights-nodejs/10-perf.png)

#### <a name="before-you-start"></a>Innan du börjar
Du behöver:

* En prenumeration på [Microsoft Azure](http://azure.com). Om ditt team eller din organisation har en Azure-prenumeration kan ägaren lägga till dig med hjälp av ditt [Microsoft-konto](http://live.com).

## <a name="a-nameaddacreate-an-application-insights-resource"></a><a name="add"></a>Skapa en Application Insights-resurs
Logga in på [Azure Portal][portal] och skapa en ny Application Insights-resurs. En [resurs][roles] i Azure är en instans av en tjänst. Det är i den här resursen som telemetrin från din app analyseras och visas.

![Klicka på Nytt, Application Insights](./media/app-insights-nodejs/01-new-asp.png)

Välj General (Allmänt) som programtyp. Valet av programtyp anger standardinnehållet i resursbladen och egenskaperna som visas i [Metrics Explorer][metrics].

#### <a name="copy-the-instrumentation-key"></a>Kopiera instrumenteringsnyckeln
Nyckeln identifierar resursen och du ska snart installera den i SDK för att dirigera data till resursen.

![Klicka på Egenskaper, markera nyckeln och tryck på CTRL + C.](./media/app-insights-nodejs/02-props-asp.png)

## <a name="a-namesdka-install-the-sdk-in-your-application"></a><a name="sdk"></a> Installera SDK i ditt program
```
npm install applicationinsights --save
```

## <a name="usage"></a>Användning
Med SDK kan du övervaka förfrågningar, spåra ohanterade undantag och övervaka systemets prestanda (processor/minne/RPS).

```javascript

var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>").start();
```

Instrumenteringsnyckeln kan också anges i miljövariabeln APPINSIGHTS_INSTRUMENTATIONKEY. Om detta görs krävs inga argument vid anrop till `appInsights.setup()` eller `appInsights.getClient()`.

Du kan prova SDK utan att skicka telemetri: ange instrumenteringsnyckeln till en sträng som inte är tom.

## <a name="a-nameruna-run-your-project"></a><a name="run"></a> Köra projektet
Kör ditt program och prova det: öppna olika sidor för att generera telemetri.

## <a name="a-namemonitora-view-your-telemetry"></a><a name="monitor"></a> Visa telemetrin
Gå tillbaka till [Azure-portalen](https://portal.azure.com) och bläddra till Application Insights-resursen.

Titta efter data på sidan Översikt. Först ser du bara en eller två punkter. Till exempel:

![Klicka dig vidare till mer data](./media/app-insights-nodejs/12-first-perf.png)

Klicka dig vidare i diagrammet om du vill visa mer detaljerade mätvärden. [Lär dig mer om mätvärden.][perf]

#### <a name="no-data"></a>Ser du inga data?
* Använd programmet och öppna olika sidor så att det genererar telemetri.
* Öppna panelen [Sök](app-insights-diagnostic-search.md) om du vill visa enskilda händelser. Ibland tar det lite längre tid för händelser att passera genom pipelinen för mätvärden.
* Vänta några sekunder och klicka på **Uppdatera**. Diagrammen uppdateras automatiskt med jämna mellanrum, men du kan uppdatera dem manuellt om du väntar på att vissa data ska visas.
* Mer information finns i [Felsökning][qna].

## <a name="publish-your-app"></a>Publicera appen
Distribuera ditt program till IIS eller Azure och se hur data ackumuleras.

#### <a name="no-data-after-you-publish-to-your-server"></a>Visas inga data efter att du har publicerat till servern?
Öppna dessa portar för utgående trafik i serverns brandvägg:

* `dc.services.visualstudio.com:443`
* `f5.services.visualstudio.com:443`

#### <a name="trouble-on-your-build-server"></a>Har du problem på build-servern?
Mer information finns i [det här felsökningsavsnittet](app-insights-asp-net-troubleshoot-no-data.md#NuGetBuild).

## <a name="customized-usage"></a>Anpassad användning
### <a name="disabling-auto-collection"></a>Inaktivera automatisk insamling
```javascript
import appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    // no telemetry will be sent until .start() is called
    .start();
```

### <a name="custom-monitoring"></a>Anpassad övervakning
```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

client.trackEvent("custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");
```

[Mer information om telemetri-API:et](app-insights-api-custom-events-metrics.md).

### <a name="using-multiple-instrumentation-keys"></a>Använda flera instrumenteringsnycklar
```javascript
import appInsights = require("applicationinsights");

// configure auto-collection with one instrumentation key
appInsights.setup("<instrumentation_key>").start();

// get a client for another instrumentation key
var otherClient = appInsights.getClient("<other_instrumentation_key>");
otherClient.trackEvent("custom event");
```

## <a name="examples"></a>Exempel
### <a name="tracking-dependency"></a>Spåra beroende
```javascript
import appInsights = require("applicationinsights");
var client = appInsights.getClient();

var startTime = Date.now();
// execute dependency call
var endTime = Date.now();

var elapsedTime = endTime - startTime;
var success = true;
client.trackDependency("dependency name", "command name", elapsedTime, success);
```



### <a name="manual-request-tracking-of-all-get-requests"></a>Manuell förfrågningsspårning av alla ”GET”-begäranden
```javascript
var http = require("http");
var appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoCollectRequests(false) // disable auto-collection of requests for this example
    .start();

// assign common properties to all telemetry sent from the default client
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};

// track a system startup event
appInsights.client.trackEvent("server start");

// create server
var port = process.env.port || 1337
var server = http.createServer(function (req, res) {
    // track all "GET" requests
    if(req.method === "GET") {
        appInsights.client.trackRequest(req, res);
    }

    res.writeHead(200, { "Content-Type": "text/plain" });
    res.end("Hello World\n");
}).listen(port);

// track startup time of the server as a custom metric
var start = +new Date;
server.on("listening", () => {
    var end = +new Date;
    var duration = end - start;
    appInsights.client.trackMetric("StartupTime", duration);
});
```

## <a name="next-steps"></a>Nästa steg
* [Övervaka din telemetri i portalen](app-insights-dashboards.md)
* [Skriv Analytics-frågor via din telemetri](app-insights-analytics-tour.md)

<!--Link references-->

[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md


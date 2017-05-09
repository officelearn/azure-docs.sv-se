---
title: "Övervaka din Node.js-tjänster med Azure Application Insights | Microsoft Docs"
description: "Övervaka prestanda- och diagnostiseringsproblem i Node.js-tjänster med Application Insights."
services: application-insights
documentationcenter: nodejs
author: joshgav
manager: carmonm
ms.assetid: 2ec7f809-5e1a-41cf-9fcd-d0ed4bebd08c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/01/2017
ms.author: joshgav
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 76a8025cd2a67533beb321c88e924517c1977dfc
ms.contentlocale: sv-se
ms.lasthandoff: 05/02/2017


---

# <a name="monitor-your-nodejs-services-and-apps-with-application-insights"></a>Övervaka dina Node-js-tjänster och -appar med Application Insights

[Azure Application Insights](app-insights-overview.md) övervakar dina serverdelstjänster och komponenter när du har distribuerat dem för att hjälpa dig att [upptäcka och snabbt diagnostisera problem med prestanda och annat](app-insights-detect-triage-diagnose.md). Använd det för Node.js-tjänster som finns var som helst: ditt datacenter, dina virtuella Azure-datorer och -webbappar och till och med andra offentliga moln.

För att ta emot, lagra, och utforska dina övervakade data följer du instruktionerna för att inkludera en agent i koden och konfigurera en motsvarande Application Insights-resurs i Azure. Agenten skickar data till den resursen för ytterligare analys och undersökning.

Node.js-agenten kan automatiskt övervaka inkommande och utgående HTTP-begäran, flera systemmått och undantag. Från och med v0.20 kan den också övervaka vissa vanliga tredjepartspaket som `mongodb`, `mysql` och `redis`. Alla händelser som relaterar till en inkommande HTTP-begäran korreleras för snabbare felsökning.

Du kan övervaka fler aspekter av din app och ditt system genom att manuellt instrumentera dem med agent-API:n som kommer att beskrivas längre fram.

![Exempel på prestandaövervakningsdiagram](./media/app-insights-nodejs/10-perf.png)

## <a name="getting-started"></a>Komma igång

Vi går igenom hur du konfigurerar övervakning för en app eller tjänst.

### <a name="resource"></a> Konfigurera en App Insights-resurs

**Innan du börjar** ska du se till att ha en Azure-prenumeration eller [så skaffar du en kostnadsfritt][azure-free-offer]. Om din organisation redan har en Azure-prenumeration kan en administratör följa [de här instruktionerna][add-aad-user] för att lägga till dig.

[azure-free-offer]: https://azure.microsoft.com/en-us/free/
[add-aad-user]: https://docs.microsoft.com/en-us/azure/active-directory/active-directory-users-create-azure-portal

Logga nu in på [Azure Portal][portal] och skapa en Application Insights-resurs enligt följande – klicka på "Nytt" > "Utvecklarverktyg" > "Application Insights". Resursen innehåller en slutpunkt för att ta emot telemetridata, lagring för dessa data, sparade rapporter och instrumentpaneler, regel- och aviseringskonfiguration med mera.

![Skapa en App Insights-resurs](./media/app-insights-nodejs/03-new_appinsights_resource.png)

På sidan där du skapar resursen väljer du "Node.js Application" i programmets listruta. Apptypen bestämmer vilken standarduppsättning av instrumentpaneler och rapporter som skapas automatiskt. Oroa dig inte – alla App Insights-resurser kan i själva verket kan samla in data från alla språk och plattformar.

![Nytt App Insights-resursformulär](./media/app-insights-nodejs/04-create_appinsights_resource.png)

### <a name="agent"></a>Konfigurera Node.js-agenten

Nu är det dags att inkludera agenten i din app så den kan samla in data.
Börja genom att kopiera resursens instrumenteringsnyckel (kallas hädanefter för din `ikey`) från portalen som du ser nedan. App Insights-systemet använder den här nyckeln för att mappa data till din Azure-resurs, så du måste ange den i en miljövariabel eller din kod för att agenten ska kunna använda den.  

![Kopiera instrumenteringsnyckel](./media/app-insights-nodejs/05-appinsights_ikey_portal.png)

Lägg sedan till Node.js-agentbiblioteket till appens beroenden via package.json. Från appens rotmapp kör du:

```bash
npm install applicationinsights --save
```

Nu behöver du uttryckligen läsa in biblioteket i din kod. Eftersom agenten lägger in instrumentationen i många andra bibliotek ska du läsa in den så tidigt som möjligt, till och med före andra `require`-uttryck. Kom igång genom att lägga till följande överst i din första .js-fil:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>");
appInsights.start();
```

Metoden `setup` konfigurerar instrumentationsnyckeln (och således Azure-resursen) som ska användas som standard för alla spårade objekt. Anropa `start` när konfigurationen är slutförd för att börja samla in och skicka telemetridata.

Du kan också tillhandahålla en ikey via miljövariabeln APPINSIGHTS\_INSTRUMENTATIONKEY istället för att skicka den manuellt till `setup()` eller `getClient()`. Med den här metoden kan du behålla ikeys utanför den allokerade källkoden och ange olika ikeys för olika miljöer.

Ytterligare konfigurationsalternativ dokumenteras nedan.

Du kan prova agenten utan att skicka telemetri genom att ange instrumenteringsnyckeln till en sträng som inte är tom.

### <a name="monitor"></a> Övervaka din app

Agenten samlar automatiskt in telemetri om Node.js-körningen och några vanliga moduler från tredje part. Använd ditt program nu för att skapa vissa av dessa data.

I [Azure Portal][portal] bläddrar du sedan till den Application Insights-resurs du skapade tidigare och letar efter dina första datapunkter i översiktstidslinjen som på bilden nedan. Klicka dig igenom diagrammen för att få mer information.

![Första datapunkter](./media/app-insights-nodejs/12-first-perf.png)

Klicka på programmets mappningsknapp om du vill visa topologi som identifierats för din app, som i följande bild. Klicka dig igenom komponenterna på kartan för att få mer information.

![Mappning av enskild app](./media/app-insights-nodejs/06-appinsights_appmap.png)

Läs mer om din app och felsök problem med andra tillgängliga vyer i avsnittet "Undersök".

![Avsnittet Undersök](./media/app-insights-nodejs/07-appinsights_investigate_blades.png)

#### <a name="no-data"></a>Ser du inga data?

Eftersom agenten slår ihop data för sändning kan det uppstå en fördröjning innan objekt visas i portalen. Om du inte ser några data i resursen kan du prova något av följande:

* Använd programmet lite mer och vidta fler åtgärder för att generera mer telemetri.
* Klicka på **Uppdatera** i portalens resursvy. Diagram uppdaterar sig själva automatiskt, men om du uppdaterar sker det omedelbart.
* Verifiera att [nödvändiga utgående portar](app-insights-ip-addresses.md) är öppna.
* Öppna panelen [Sök](app-insights-diagnostic-search.md) om du vill visa enskilda händelser.
* Se [Vanliga frågor och svar][].


## <a name="agent-configuration"></a>Agentkonfiguration

Här följer agentens konfigurationsmetoder och deras standardvärden.

Ställ in `.setAutoDependencyCorrelation(true)` för att korrelera händelser i en tjänst fullständigt. På så sätt kan agenten spåra innehåll över asynkrona återanrop i Node.js.

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("<instrumentation_key>")
    .setAutoDependencyCorrelation(false)
    .setAutoCollectRequests(true)
    .setAutoCollectPerformance(true)
    .setAutoCollectExceptions(true)
    .setAutoCollectDependencies(true)
    .start();
```

## <a name="agent-api"></a>Agent-API

<!-- TODO: Fully document agent API. -->

.NET-agentens API beskrivs fullständigt [här](app-insights-api-custom-events-metrics.md).

Du kan spåra valfri begäran, händelse, mått eller undantag med Node.js-klienten i Application Insights. I följande exempel visas några av de tillgängliga API:erna.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup().start(); // assuming ikey in env var
let client = appInsights.getClient();

client.trackEvent("my custom event", {customProperty: "custom property value"});
client.trackException(new Error("handled exceptions can be logged with this method"));
client.trackMetric("custom metric", 3);
client.trackTrace("trace message");

let http = require("http");
http.createServer( (req, res) => {
  client.trackRequest(req, res); // Place at the beginning of your request handler
});
```

### <a name="track-your-dependencies"></a>Spåra dina beroenden

```javascript
let appInsights = require("applicationinsights");
let client = appInsights.getClient();

var success = false;
let startTime = Date.now();
// execute dependency call here....
let duration = Date.now() - startTime;
success = true;

client.trackDependency("dependency name", "command name", duration, success);
```

### <a name="add-a-custom-property-to-all-events"></a>Lägga till en anpassad egenskap i alla händelser

```javascript
appInsights.client.commonProperties = {
    environment: process.env.SOME_ENV_VARIABLE
};
```

### <a name="track-http-get-requests"></a>Spåra HTTP GET-begäranden

```javascript
var server = http.createServer((req, res) => {
    if ( req.method === "GET" ) {
            appInsights.client.trackRequest(req, res);
    }
    // other work here....
    res.end();
});
```

### <a name="track-server-startup-time"></a>Spåra starttiden för server

```javascript
let start = Date.now();
server.on("listening", () => {
    let duration = Date.now() - start;
    appInsights.client.trackMetric("server startup time", duration);
});
```

## <a name="more-resources"></a>Fler resurser

* [Övervaka din telemetri i portalen](app-insights-dashboards.md)
* [Skriv Analytics-frågor via din telemetri](app-insights-analytics-tour.md)

<!--references-->

[portal]: https://portal.azure.com/
[Vanliga frågor och svar]: app-insights-troubleshoot-faq.md


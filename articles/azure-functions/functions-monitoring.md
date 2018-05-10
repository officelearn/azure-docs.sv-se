---
title: Övervaka Azure Functions
description: Lär dig hur du använder Azure Application Insights med Azure Functions för att övervaka körning av funktionen.
services: functions
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: cbdb4691bac01843a451c988e09d77dd10f97461
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

## <a name="overview"></a>Översikt 

[Azure Functions](functions-overview.md) erbjuder inbyggda integreringen med [Azure Application Insights](../application-insights/app-insights-overview.md) för övervakning av funktioner. Den här artikeln visar hur du konfigurerar funktioner för att skicka telemetridata till Application Insights.

![Application Insights Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Funktioner har också [inbyggda övervakning som inte använder Application Insights](#monitoring-without-application-insights). Vi rekommenderar Application Insights eftersom det ger mer data och bättre sätt att analysera data.

## <a name="application-insights-pricing-and-limits"></a>Application Insights priser och gränser

Du kan prova Application Insights-integrering med funktionen appar kostnadsfritt. Men det finns en daglig gräns för hur mycket data kan bearbetas gratis och du kan träffa denna gräns under testningen. Azure tillhandahåller portal och e-postaviseringar när du närmar dig din dagliga gränsen.  Men om du missar de aviseringarna och nådde gränsen som nya loggar visas inte i Application Insights frågor. Vara medveten om gränsen för att undvika onödiga tidsåtgången för felsökning. Mer information finns i [hantera priser och data volym i Application Insights](../application-insights/app-insights-pricing.md).

## <a name="enable-app-insights-integration"></a>Aktivera App Insights-integrering

Den måste veta instrumentation nyckeln för Application Insights-resurs för en funktionsapp att skicka data till Application Insights. Nyckeln måste anges i en appinställning med namnet APPINSIGHTS_INSTRUMENTATIONKEY.

Du kan ställa in den här anslutningen i den [Azure-portalen](https://portal.azure.com):

* [Automatiskt för en ny funktionsapp](#new-function-app)
* [Anslut en App Insights-resursen manuellt](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Ny funktionsapp

1. Gå till appen med funktionen **skapa** sidan.

1. Ange den **Programinsikter** växla **på**.

2. Välj en **Application Insights plats**.

   Välj den region som ligger närmast din funktionsapp region i en [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill att data ska lagras.

   ![Aktivera Application Insights när du skapar en funktionsapp](media/functions-monitoring/enable-ai-new-function-app.png)

3. Ange den nödvändiga informationen.

1. Välj **Skapa**.

Nästa steg är att [inaktivera inbyggda loggning](#disable-built-in-logging).

### <a name="manually-connect-an-app-insights-resource"></a>Anslut en App Insights-resursen manuellt 

1. Skapa Application Insights-resursen. Ange program **allmänna**.

   ![Skapa en Application Insights-resurs skriver Allmänt](media/functions-monitoring/ai-general.png)

2. Kopiera instrumentation nyckeln från den **Essentials** sidan i Application Insights-resursen. Hovra över slutet av värdet för nyckeln visas att hämta en **Klicka om du vill kopiera** knappen.

   ![Kopiera nyckeln som Application Insights instrumentation](media/functions-monitoring/copy-ai-key.png)

1. I appen funktionen **programinställningar** sidan [lägga till en appinställning](functions-how-to-use-azure-function-app-settings.md#settings) genom att klicka på **lägga till nya inställningen**. Namnge den nya inställningen APPINSIGHTS_INSTRUMENTATIONKEY och klistra in den kopierade instrumentation nyckeln.

   ![Lägg till nyckel för instrumentation appinställningar](media/functions-monitoring/add-ai-key.png)

1. Klicka på **Spara**.

## <a name="disable-built-in-logging"></a>Inaktivera inbyggda loggning

Om du aktiverar Application Insights, rekommenderar vi att du inaktiverar den [inbyggd loggning som använder Azure storage](#logging-to-storage). Inbyggd loggning är användbart för testning med lätta arbetsbelastningar men är inte avsedd för hög belastning produktion. Application Insights rekommenderas för övervakning av produktionen. Om du använder inbyggd loggning i produktion kan loggning posten vara ofullständiga på grund av begränsningar i Azure Storage.

Om du vill inaktivera inbyggda loggning genom att ta bort den `AzureWebJobsDashboard` appinställningen. Information om hur du tar bort app-inställningar i Azure portal finns det **programinställningar** avsnitt i [hur du hanterar en funktionsapp](functions-how-to-use-azure-function-app-settings.md#settings). Innan du tar bort appinställningen, se till att inga befintliga funktioner i appen med samma funktion använder den för Azure Storage-utlösare och bindningar.

## <a name="view-telemetry-in-monitor-tab"></a>Visa telemetri i fliken övervakning

När du har ställt in Application Insights integration som visas i föregående avsnitt, kan du visa telemetridata i den **övervakaren** fliken.

1. På sidan funktionen app väljer du en funktion som har körts minst en gång när Application Insights har konfigurerats och välj sedan den **övervakaren** fliken.

   ![Välj fliken övervakning](media/functions-monitoring/monitor-tab.png)

2. Välj **uppdatera** regelbundet tills listan med funktionsanrop visas.

   Det kan ta upp till 5 minuter innan listan ska visas på grund av hur telemetridata för klienten batchar för överföring till servern. (Den här fördröjningen kan inte användas för den [direktsänd dataström med mått](../application-insights/app-insights-live-stream.md). Tjänsten ansluter till värden för funktioner när du läser in sidan så loggar strömmas direkt till sidan.)

   ![Anrop listan](media/functions-monitoring/monitor-tab-ai-invocations.png)

2. Om du vill visa loggarna för ett visst funktionsanrop, Välj den **datum** kolumnen länk för att anropa.

   ![Anrop Informationslänk](media/functions-monitoring/invocation-details-link-ai.png)

   Loggning utdata för att anropa visas i en ny sida.

   ![Anropsinformation](media/functions-monitoring/invocation-details-ai.png)

Båda sidor (anrop listan och information) som länkar till Application Insights Analytics-fråga som hämtar data:

![Kör i Application Insights](media/functions-monitoring/run-in-ai.png)

![Programlista insikter Analytics anrop](media/functions-monitoring/ai-analytics-invocation-list.png)

De här frågorna du se att listan anrop är begränsad till den senaste 30 dagar längre än 20 rader (`where timestamp > ago(30d) | take 20`) och anrop information om listan är för de senaste 30 dagarna utan gräns.

Mer information finns i [fråga telemetridata](#query-telemetry-data) senare i den här artikeln.

## <a name="view-telemetry-in-app-insights"></a>Visa telemetri i appen insikter

Om du vill öppna Application Insights från en funktionsapp i Azure portal, Välj den **Application Insights** länken i den **konfigurerats funktioner** avsnitt i appen funktionen **översikt** sidan.

![Application Insights länk på översiktssidan](media/functions-monitoring/ai-link.png)


Information om hur du använder Application Insights finns i [Application Insights dokumentationen](https://docs.microsoft.com/azure/application-insights/). Detta avsnitt visar några exempel på hur du visar data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [avsnitt om att konfigurera och anpassa telemetridata](#configure-categories-and-log-levels).

I [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md), du kan skapa diagram och aviseringar baserat på mått sådana som antal funktionsanrop, körningstid och lyckade resultat.

![Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

På den [fel](../application-insights/app-insights-asp-net-exceptions.md) fliken kan du skapa diagram och aviseringar baserat på funktionen fel och server undantag. Den **åtgärdsnamn** är namnet på funktionen. Fel i beroenden visas inte om du implementerar [telemetri om anpassade](#custom-telemetry-in-c-functions) för beroenden.

![Fel](media/functions-monitoring/failures.png)

På den [prestanda](../application-insights/app-insights-performance-counters.md) kan du analysera prestandaproblem.

![Prestanda](media/functions-monitoring/performance.png)

Den **servrar** visar resursutnyttjande och genomströmning per server. Dessa data kan vara användbart för felsökning av scenarier där funktioner bogging ned underliggande resurserna. Servrar kallas **molnet rollinstanser**.

![Servrar](media/functions-monitoring/servers.png)

Den [direktsänd dataström med mått](../application-insights/app-insights-live-stream.md) mätvärdesdata visar när den skapas i realtid.

![Direktsänd ström](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Fråga telemetridata

[Application Insights Analytics](../application-insights/app-insights-analytics.md) ger dig tillgång till alla telemetridata i form av tabeller i en databas. Analytics ger ett frågespråk för extrahering, hantering och visualisera dina data.

![Välj Analytics](media/functions-monitoring/select-analytics.png)

![Analytics-exempel](media/functions-monitoring/analytics-traces.png)

Här är ett exempel på frågan. Den här visar fördelningen av begäranden per worker under de senaste 30 minuterna.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabeller som är tillgängliga som visas i den **schemat** fliken i det vänstra fönstret. Du kan hitta data som genereras av funktionsanrop i följande tabeller:

* **spår** -loggar som skapats av körningen och av funktionskoden.
* **begäranden** -en för varje funktionsanrop.
* **undantag** – alla undantag av körningen.
* **customMetrics** -antal lyckade och misslyckade anrop, lyckade resultat, varaktighet.
* **customEvents** -händelser spåras av körningen, till exempel: HTTP-begäranden som utlöser en funktion.
* **performanceCounters** -information om prestanda för de servrar som funktionerna som körs på.

De andra tabellerna är för tillgänglighetstester och klientwebbläsaren/telemetri. Du kan implementera anpassad telemetri om du vill lägga till data i dem.

Vissa funktioner-specifika data inom varje tabell är i ett `customDimensions` fält.  Till exempel följande fråga hämtar alla spårningar som har loggningsnivån `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Körningen ger `customDimensions.LogLevel` och `customDimensions.Category`. Du kan ange ytterligare fält i loggarna som du skriver i funktionskoden. Se [strukturerad loggning](#structured-logging) senare i den här artikeln.

## <a name="configure-categories-and-log-levels"></a>Konfigurera kategorier och logga nivåer

Du kan använda Application Insights utan någon anpassad konfiguration, men standardkonfigurationen kan leda till stora mängder data. Om du använder en Visual Studio-Azure-prenumeration kan du träffa dina data cap för Application Insights. Resten av den här artikeln visar hur du konfigurerar och anpassa de data som dina funktioner skickar till Application Insights.

### <a name="categories"></a>Kategorier

Azure Functions loggaren innehåller en *kategori* för varje logg. Kategorin anger vilken del av runtime-kod eller ditt Funktionskoden skrev loggen. 

Functions-runtime skapar loggar som har en kategori som börjar med ”värd”. Till exempel ”funktionen igång”, ”funktionen körs” och ”klar” funktionsloggar har kategori ”Host.Executor”. 

Om du skriver loggar i Funktionskoden är ”funktion” deras kategori.

### <a name="log-levels"></a>Loggningsnivåer

Azure functions loggaren innehåller också en *certifikatutfärdarnivå* med varje logg. [LogLevel](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loglevel#Microsoft_Extensions_Logging_LogLevel) är en uppräkning och koden heltal anger relativa betydelse:

|LogLevel    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritiska    | 5 |
|Ingen        | 6 |

Logga nivå `None` beskrivs i nästa avsnitt. 

### <a name="configure-logging-in-hostjson"></a>Konfigurera loggning i host.json

Den *host.json* filen konfigurerar hur mycket loggning en funktionsapp skickar till Application Insights. För varje kategori kan ange du den lägsta loggningsnivån för att skicka. Här är ett exempel:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Det här exemplet anger följande regler:

1. Efter loggar med kategorin ”Host.Results” eller ”funktionen” Skicka endast `Error` nivå och högre till Application Insights. Loggar för `Warning` nivå och nedanför ignoreras.
2. Efter loggar med kategori värden. Aggregatorn, skicka endast `Information` nivå och högre till Application Insights. Loggar för `Debug` nivå och nedanför ignoreras.
3. Skicka bara för alla andra loggar `Information` nivå och högre till Application Insights.

Kategori-värdet i *host.json* styr loggning för alla kategorier som börjar med samma värde. Till exempel ”värd” i *host.json* styr loggning för ”Host.General”, ”Host.Executor”, ”Host.Results” och så vidare.

Om *host.json* innehåller flera kategorier som börjar med samma sträng de längre som matchas. Anta att du vill att allt från runtime utom ”Host.Aggregator” för att logga in på `Error` nivån när ”Host.Aggregator” loggar vid `Information` nivå:

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

Om du inte alla loggar för en kategori, kan du använda loggningsnivån `None`. Inga loggar skrivs med denna kategori och det finns inga loggningsnivån ovanför.

I följande avsnitt beskrivs huvudkategorierna loggar som skapas av körningsmiljön. 

### <a name="category-hostresults"></a>Kategori Host.Results

Dessa loggar visas som ”begäranden” i Application Insights. De visar lyckad eller misslyckad för en funktion.

![Begäranden diagram](media/functions-monitoring/requests-chart.png)

Alla dessa loggar skrivs på `Information` nivå, så om du filtrerar på `Warning` eller senare, visas inte någon av dessa data.

### <a name="category-hostaggregator"></a>Kategori Host.Aggregator

Dessa loggar ge antal och genomsnitt av funktionsanrop via en [konfigurerbara](#configure-the-aggregator) period tid. Standardvärdet är 30 sekunder eller 1 000 resultat, beroende på vilket som inträffar först. 

Loggarna är tillgängliga i den **customMetrics** tabellen i Application Insights. Exempel är antalet körs, lyckade resultat och varaktighet.

![customMetrics fråga](media/functions-monitoring/custom-metrics-query.png)

Alla dessa loggar skrivs på `Information` nivå, så om du filtrerar på `Warning` eller senare, visas inte någon av dessa data.

### <a name="other-categories"></a>Andra kategorier

Alla loggar för kategorier än som redan i listan är tillgängliga i den **spårningar** tabellen i Application Insights.

![spår fråga](media/functions-monitoring/analytics-traces.png)

Alla loggar med kategorier som börjar med ”värd” är skrivna med Functions-runtime. ”Starta funktionen” och ”funktionen slutfört” loggar har kategori ”Host.Executor”. För lyckad kör dessa loggar är `Information` nivån; undantag loggas på `Error` nivå. Körningen skapar även `Warning` nivå loggar, till exempel: kö meddelanden som skickas till kön skadligt.

Loggar som skrivs av Funktionskoden har kategori ”funktionen” och kan vara alla loggningsnivå.

## <a name="configure-the-aggregator"></a>Konfigurera aggregator

Enligt beskrivningen i föregående avsnitt samlar körningsmiljön in data om funktionen körningar under en viss tidsperiod. Standardvärdet är 30 sekunder eller 1 000 körs, beroende på vilket som kommer först. Du kan konfigurera den här inställningen på den *host.json* fil.  Här är ett exempel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurera provtagning

Application Insights har en [provtagning](../application-insights/app-insights-sampling.md) funktion som kan skydda dig från producerar för mycket telemetridata ibland av belastning. När antalet objekt som telemetri överskrider en angiven hastighet, startar Application Insights att ignorera slumpmässigt vissa inkommande objekt. Standardinställningen för maximalt antal objekt per sekund är 5. Du kan konfigurera provtagning i *host.json*.  Här är ett exempel:

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```

## <a name="write-logs-in-c-functions"></a>Skriva loggar i C#-funktioner

Du kan skriva till loggar i Funktionskoden som visas som spåren i Application Insights.

### <a name="ilogger"></a>ILogger

Använd en [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) parameter i dina funktioner i stället för en `TraceWriter` parameter. Loggarna som skapas med hjälp av `TraceWriter` går till Application Insights, men `ILogger` kan du göra [strukturerad loggning](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Med en `ILogger` objekt du anropa `Log<level>` [tilläggsmetoder på ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.loggerextensions#Methods_) att skapa loggar. Till exempel i följande kod skrivningar `Information` loggar med kategori ”funktionen”.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturerade loggning

Ordningen på platshållare, inte namnen avgör vilka parametrar som används i loggmeddelandet. Anta att du har följande kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Om du behålla samma Meddelandesträngen och vända på ordningen på parametrarna skulle resulterande meddelandetexten ha värden på fel platser.

Platshållare hanteras det här sättet så att du kan göra strukturerade loggning. Application Insights lagrar parametern namn / värde-par förutom message-sträng. Resultatet är att argumenten meddelandet blir fält som du kan fråga på.

Till exempel om loggaren-metodanrop ser ut som i föregående exempel, du kan fråga fältet `customDimensions.prop__rowKey`. Den `prop__` prefix har lagts till så att det inte finns några konflikter mellan fälten körningsmiljön fälten Funktionskoden och lägger till lägger du till.

Du kan också fråga på den ursprungliga Meddelandesträngen med refererar till fältet `customDimensions.prop__{OriginalFormat}`.  

Här är ett exempel JSON-representation av `customDimensions` data:

```json
{
  customDimensions: {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="logging-custom-metrics"></a>Anpassade mått för loggning  

I C# skript funktion, kan du använda den `LogMetric` tilläggsmetoden på `ILogger` att skapa anpassade mått i Application Insights. Här är ett exempel metodanrop:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Den här koden är ett alternativ till anropar `TrackMetric` med [Application Insights API för .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Skriva loggar i JavaScript-funktioner

I Node.js-funktion, använda `context.log` att skriva loggar. Strukturerade loggning är inte aktiverad.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Anpassade mått för loggning  

I Node.js-funktion, kan du använda den `context.log.metric` metoden för att skapa anpassade mått i Application Insights. Här är ett exempel metodanrop:

```javascript
context.log.metric("TestMetric", 1234); 
```

Den här koden är ett alternativ till anropar `trackMetric` med [Node.js SDK för Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Anpassad telemetri i C#-funktioner

Du kan använda den [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet-paketet för att skicka telemetri om anpassade data till Application Insights.

Här är ett exempel på C#-kod som använder den [telemetri om anpassade API](../application-insights/app-insights-api-custom-events-metrics.md). I exemplet används för en .NET-klassbiblioteket, men Application Insights-koden är densamma för C# skript.

```cs
using System;
using System.Net;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.Azure.WebJobs;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Linq;

namespace functionapp0915
{
    public static class HttpTrigger2
    {
        private static string key = TelemetryConfiguration.Active.InstrumentationKey = 
            System.Environment.GetEnvironmentVariable(
                "APPINSIGHTS_INSTRUMENTATIONKEY", EnvironmentVariableTarget.Process);

        private static TelemetryClient telemetryClient = 
            new TelemetryClient() { InstrumentationKey = key };

        [FunctionName("HttpTrigger2")]
        public static async Task<HttpResponseMessage> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)]
            HttpRequestMessage req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.GetQueryNameValuePairs()
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Get request body
            dynamic data = await req.Content.ReadAsAsync<object>();

            // Set name to query string or body data
            name = name ?? data?.name;
         
            // Track an Event
            var evt = new EventTelemetry("Function called");
            UpdateTelemetryContext(evt.Context, context, name);
            telemetryClient.TrackEvent(evt);
            
            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            UpdateTelemetryContext(metric.Context, context, name);
            telemetryClient.TrackMetric(metric);
            
            // Track a Dependency
            var dependency = new DependencyTelemetry
                {
                    Name = "GET api/planets/1/",
                    Target = "swapi.co",
                    Data = "https://swapi.co/api/planets/1/",
                    Timestamp = start,
                    Duration = DateTime.UtcNow - start,
                    Success = true
                };
            UpdateTelemetryContext(dependency.Context, context, name);
            telemetryClient.TrackDependency(dependency);
            
            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
        
        // This correllates all telemetry with the current Function invocation
        private static void UpdateTelemetryContext(TelemetryContext context, ExecutionContext functionContext, string userName)
        {
            context.Operation.Id = functionContext.InvocationId.ToString();
            context.Operation.ParentId = functionContext.InvocationId.ToString();
            context.Operation.Name = functionContext.FunctionName;
            context.User.Id = userName;
        }
    }    
}
```

Anropa inte `TrackRequest` eller `StartOperation<RequestTelemetry>`, ser du dubbla begäranden för ett funktionsanrop.  Functions-runtime spårar automatiskt begäranden.

Konfigurerar inte `telemetryClient.Context.Operation.Id`. Detta är en global inställning och kommer att orsaka felaktigt correllation när många funktioner som körs samtidigt. Skapa en ny telemetri instans i stället (`DependencyTelemetry`, `EventTelemetry`) och ändra dess `Context` egenskapen. Sedan skicka telemetri instans till motsvarande `Track` metod på `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Detta säkerställer att telemetrin har rätt correllation information för den aktuella funktionsanrop.

## <a name="custom-telemetry-in-javascript-functions"></a>Anpassad telemetri i JavaScript-funktioner

Den [Application Insights SDK för Node.js](https://www.npmjs.com/package/applicationinsights) är för närvarande i betaversion. Här är några exempelkod som skickar telemetri om anpassade till Application Insights:

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    client.trackEvent({name: "my custom event", tagOverrides:{"ai.operation.id": context.invocationId}, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackTrace({message: "trace message", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:{"ai.operation.id": context.invocationId}});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:{"ai.operation.id": context.invocationId}});

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status: 200, /* Defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

Den `tagOverrides` parameteruppsättningarna `operation_Id` till den funktionen anrops-ID. Den här inställningen kan du matcha alla genereras automatiskt och anpassad telemetri för ett givet funktionsanrop.

## <a name="known-issues"></a>Kända problem

### <a name="dependencies"></a>Beroenden

Beroenden som funktionen har till andra tjänster visas inte automatiskt, men du kan skriva anpassade kod för att visa beroenden. Exempelkoden i den [C# telemetri om anpassade avsnittet](#custom-telemetry-in-c-functions) visar hur. Exempelkoden resulterar i en *programavbildningen* i Application Insights ser ut så här:

![Programkarta](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Rapporten problem

Rapportera ett problem med Application Insights integrering i funktioner och ge förslag eller begäran, [skapar ett problem i GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Övervakning utan Application Insights

Vi rekommenderar Application Insights för övervakningsfunktionerna eftersom det ger mer data och bättre sätt att analysera data. Men om du föredrar att systemets inbyggda loggning som använder Azure Storage kan du fortsätta att använda.

### <a name="logging-to-storage"></a>Loggning till lagring

Inbyggd loggning använder storage-konto som anges av anslutningssträngen i den `AzureWebJobsDashboard` appinställningen. Välj en funktion i en funktion app-sida, och välj sedan den **övervakaren** fliken och väljer att behålla i klassiskt läge.

![Växla till klassisk vy](media/functions-monitoring/switch-to-classic-view.png)

 Du kan hämta en lista över funktionen körningar. Välj en funktion körning ska granska varaktighet, indata, fel och associerade loggfilerna.

Om du har aktiverat Application Insights tidigare, men nu ska du gå tillbaka till inbyggda loggning, inaktivera Application Insights manuellt och välj sedan den **övervakaren** fliken. Ta bort inställningen APPINSIGHTS_INSTRUMENTATIONKEY app om du vill inaktivera Application Insights-integration.

Även om den **övervakaren** visar Application Insights-data, kan du se loggdata i filsystemet om du inte gjort [inaktiverad inbyggda loggning](#disable-built-in-logging). Gå till filer, Välj tjänsten för funktionen och gå sedan till i resursen lagring `LogFiles > Application > Functions > Function > your_function` till finns i loggfilen.

### <a name="real-time-monitoring"></a>Realtidsövervakning

Du kan strömma loggfiler till en kommandorad session på en lokal arbetsstation med hjälp av den [Azure kommandoradsgränssnittet (CLI) 2.0](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).  

Använd följande kommandon för Azure CLI 2.0 att logga in, väljer din prenumeration och dataströmmen loggfiler:

```
az login
az account list
az account set <subscriptionNameOrId>
az appservice web log tail --resource-group <resource group name> --name <function app name>
```

För Azure PowerShell använder du följande kommandon för att lägga till ditt Azure-konto, väljer din prenumeration och dataströmmen loggfiler:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Mer information finns i [så att strömma loggar](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Visa loggfiler lokalt

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core loggning](/aspnet/core/fundamentals/logging/)

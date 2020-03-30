---
title: Övervaka Azure Functions
description: Lär dig hur du använder Azure Application Insights med Azure Functions för att övervaka funktionskörning.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.openlocfilehash: 08da17f1ef023676aa0c499cf4e7e1bb9687f1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257861"
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

[Azure Functions](functions-overview.md) erbjuder inbyggd integrering med [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) för att övervaka funktioner. Den här artikeln visar hur du konfigurerar Azure Functions för att skicka systemgenererade loggfiler till Application Insights.

Vi rekommenderar att du använder Application Insights eftersom den samlar in logg-, prestanda- och feldata. Den identifierar automatiskt prestandaavvikelser och innehåller kraftfulla analysverktyg som hjälper dig att diagnostisera problem och förstå hur dina funktioner används. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Du kan även använda Application Insights under lokal funktion app projektutveckling. Mer information finns i [Vad är Programinsikter?](../azure-monitor/app/app-insights-overview.md).

Eftersom den nödvändiga Application Insights-instrumenteringen är inbyggd i Azure Functions behöver du bara en giltig instrumenteringsnyckel för att ansluta din funktionsapp till en Application Insights-resurs. Instrumenteringsnyckeln ska läggas till i programinställningarna när din funktionsappresurs skapas i Azure. Om funktionsappen inte redan har den här nyckeln kan du [ställa in den manuellt](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Prissättning och begränsningar för Application Insights

Du kan prova Application Insights integration med Function Apps gratis. Det finns en daglig gräns för hur mycket data som kan bearbetas gratis. Du kan nå den här gränsen under testningen. Azure tillhandahåller portal- och e-postaviseringar när du närmar dig din dagliga gräns. Om du missar dessa aviseringar och når gränsen visas inte nya loggar i Application Insights-frågor. Var medveten om gränsen för att undvika onödig felsökningstid. Mer information finns i [Hantera priser och datavolym i Application Insights](../azure-monitor/app/pricing.md).

Den fullständiga listan över funktioner för Application Insights som är tillgängliga för din funktionsapp beskrivs i funktioner som stöds av [Application Insights för Azure Functions](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="view-telemetry-in-monitor-tab"></a>Visa telemetri på fliken Övervaka

När [application insights-integrering är aktiverat](#enable-application-insights-integration)kan du visa telemetridata på fliken **Bildskärm.**

1. På funktionsappsidan väljer du en funktion som har körts minst en gång efter att Application Insights har konfigurerats. Välj sedan fliken **Bildskärm.** Välj **Uppdatera** med jämna mellanrum tills listan över funktionsanrop visas.

   ![Lista över anrop](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Det kan ta upp till fem minuter innan listan visas medan telemetriklienten batchar data för överföring till servern. Fördröjningen gäller inte för [livemåttströmmen](../azure-monitor/app/live-stream.md). Den tjänsten ansluter till functions-värden när du läser in sidan, så loggar strömmas direkt till sidan.

1. Om du vill visa loggarna för en viss funktionsanrop väljer du kolumnlänken **Datum (UTC)** för den anropet. Loggningsutdata för den anropet visas på en ny sida.

   ![Information om anrop](media/functions-monitoring/invocation-details-ai.png)

1. Välj länken **Kör i Programinsikter** för att visa källan till frågan som hämtar Azure Monitor-loggdata i Azure Log Om det här är första gången du använder Azure Log Analytics i din prenumeration, ombeds du att aktivera.

1. När du väljer den länken och väljer att aktivera Logga analytisk. följande fråga visas. Du kan se att frågeresultaten är begränsade till`where timestamp > ago(30d)`de senaste 30 dagarna ( ). Dessutom visar resultaten inte mer än 20`take 20`rader ( ). Däremot är anropsinformationslistan för din funktion för de senaste 30 dagarna utan någon gräns.

   ![Programinsiktsanalys analytics-anropslista](media/functions-monitoring/ai-analytics-invocation-list.png)

Mer information finns i [Frågetelemetridata](#query-telemetry-data) senare i den här artikeln.

## <a name="view-telemetry-in-application-insights"></a>Visa telemetri i Application Insights

Om du vill öppna Application Insights från en funktionsapp **Overview** i Azure-portalen går du till funktionsappens översiktssida. Under **Konfigurerade funktioner**väljer du **Application Insights**.

![Öppna programstatistik från sidan Översikt över funktionsapp](media/functions-monitoring/ai-link.png)

Information om hur du använder Application Insights finns i [dokumentationen för Application Insights](https://docs.microsoft.com/azure/application-insights/). I det här avsnittet visas några exempel på hur du visar data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [avsnitten om hur du konfigurerar och anpassar telemetridata](#configure-categories-and-log-levels).

![Fliken Översikt över programinsikter](media/functions-monitoring/metrics-explorer.png)

Följande områden i Application Insights kan vara till hjälp när du utvärderar beteende, prestanda och fel i dina funktioner:

| Undersök
 | Beskrivning |
| ---- | ----------- |
| **[Fel](../azure-monitor/app/asp-net-exceptions.md)** |  Skapa diagram och aviseringar baserat på funktionsfel och serverundantag. **Operationsnamnet** är funktionsnamnet. Fel i beroenden visas inte om du inte implementerar anpassad telemetri för beroenden. |
| **[Prestanda](../azure-monitor/app/performance-counters.md)** | Analysera prestandaproblem genom att visa resursutnyttjande och dataflöde per **molnrollinstanser**. Dessa data kan vara användbara för felsökning av scenarier där funktioner kör fast dina underliggande resurser. |
| **[Statistik](../azure-monitor/app/metrics-explorer.md)** | Skapa diagram och aviseringar som baseras på mått. Mått inkluderar antalet funktionsanrop, körningstid och framgångsgrader. |
| **[Live-mått](../azure-monitor/app/live-stream.md)** | Visa måttdata när de skapas i nära realtid. |

## <a name="query-telemetry-data"></a>Frågetelemetridata

[Application Insights Analytics](../azure-monitor/app/analytics.md) ger dig tillgång till alla telemetridata i form av tabeller i en databas. Analytics tillhandahåller ett frågespråk för att extrahera, manipulera och visualisera data. 

Välj **Loggar** för att utforska eller fråga efter loggade händelser.

![Exempel på analys](media/functions-monitoring/analytics-traces.png)

Här är ett frågeexempel som visar fördelningen av begäranden per anställd under de senaste 30 minuterna.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

De tabeller som är tillgängliga visas på fliken **Schema** till vänster. Du kan hitta data som genereras av funktionsanrop i följande tabeller:

| Tabell | Beskrivning |
| ----- | ----------- |
| **Spår** | Loggar som skapats av körningen och funktionskoden. |
| **begäran** | En begäran för varje funktionstallorop. |
| **Undantag** | Eventuella undantag som genereras av körningen. |
| **customMetrics** | Antalet lyckade och misslyckade åkallor, framgång och varaktighet. |
| **customEvents** | Händelser som spåras av körningen, till exempel: HTTP-begäranden som utlöser en funktion. |
| **performanceCounters** | Information om prestanda för de servrar som funktionerna körs på. |

De andra tabellerna är för tillgänglighetstester och klient- och webbläsartelemetri. Du kan implementera anpassad telemetri för att lägga till data i dem.

I varje tabell finns vissa av de `customDimensions` functions-specifika data i ett fält.  Följande fråga hämtar till exempel alla spårningar `Error`som har loggnivå .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Körningen innehåller `customDimensions.LogLevel` fälten och. `customDimensions.Category` Du kan ange ytterligare fält i loggar som du skriver i funktionskoden. Se [Strukturerad loggning](#structured-logging) senare i den här artikeln.

## <a name="configure-categories-and-log-levels"></a>Konfigurera kategorier och loggnivåer

Du kan använda Application Insights utan anpassad konfiguration. Standardkonfigurationen kan resultera i stora datavolymer. Om du använder en Visual Studio Azure-prenumeration kan du få tag på ditt datatak för Application Insights. Senare i den här artikeln får du lära dig hur du konfigurerar och anpassar data som dina funktioner skickar till Application Insights. För en funktionsapp konfigureras loggning i [filen host.json.]

### <a name="categories"></a>Kategorier

Azure Functions logger innehåller en *kategori* för varje logg. Kategorin anger vilken del av körningskoden eller funktionskoden som skrev loggen. I följande diagram beskrivs de huvudkategorier av loggar som körningen skapar. 

| Kategori | Beskrivning |
| ----- | ----- | 
| Host.Results | Dessa loggar visas som **begäranden** i Application Insights. De indikerar framgång eller fel på en funktion. Alla dessa loggar är `Information` skrivna på nivå. Om du `Warning` filtrerar på eller över ser du inga av dessa data. |
| Host.Aggregator | Dessa loggar ger antal och medelvärden för funktionsgåvningar under en [konfigurerbar](#configure-the-aggregator) tidsperiod. Standardperioden är 30 sekunder eller 1 000 resultat, beroende på vilket som inträffar först. Loggarna är tillgängliga i tabellen **customMetrics** i Application Insights. Exempel är antalet körningar, framgång och varaktighet. Alla dessa loggar är `Information` skrivna på nivå. Om du `Warning` filtrerar på eller över ser du inga av dessa data. |

Alla loggar för andra kategorier än dessa är tillgängliga i **spårningstabellen** i Application Insights.

Alla loggar med kategorier `Host` som börjar med skrivs av körningen Funktioner. **Funktionen startade** och **Funktionsloggar** har kategori `Host.Executor`. För lyckade körningar är `Information` dessa loggar nivå. Undantag loggas `Error` på nivå. Körningen skapar också `Warning` nivåloggar, till exempel: kömeddelanden som skickas till giftkön.

Med körningen Funktioner skapas loggar med en kategori som börjar med "Värd". I version 1.x `function started` `function executed`har `function completed` , och loggarna kategorin `Host.Executor`. Från och med version 2.x har `Function.<YOUR_FUNCTION_NAME>`dessa loggar kategorin .

Om du skriver loggar in din funktionskod är kategorin och `Function.<YOUR_FUNCTION_NAME>.User` kan vara vilken loggnivå som helst. I version 1.x av funktionskörningen `Function`är kategorin .

### <a name="log-levels"></a>Loggnivåer

Azure Functions logger innehåller också en *loggnivå* med varje logg. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) är en uppräkning och heltalskoden anger relativ betydelse:

|Loggnivå    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritisk    | 5 |
|Inget        | 6 |

Loggnivå `None` förklaras i nästa avsnitt. 

### <a name="log-configuration-in-hostjson"></a>Loggkonfiguration i host.json

[Filen host.json] konfigurerar hur mycket loggning en funktionsapp skickar till Application Insights. För varje kategori anger du den lägsta loggnivå som ska skickas. Det finns två exempel: det första exemplet är upp till [version 2.x och senare](functions-versions.md#version-2x) av funktionskörningen (med .NET Core), och det andra exemplet är för körningen version 1.x.

### <a name="version-2x-and-higher"></a>Version 2.x och högre

Version v2.x och senare versioner av funktionskörningen använder [filterhierarkin .NET Core .](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) 

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

I det här exemplet anges följande regler:

* För loggar med `Host.Results` `Function`kategori eller `Error` skickar du endast nivå och högre till Application Insights. Loggar för `Warning` nivå och lägre ignoreras.
* För loggar med `Host.Aggregator`kategori skickar du alla loggar till Application Insights. Loggnivån `Trace` är densamma som vad vissa `Verbose`loggers `Trace` kallar , men använder i [filen host.json.]
* För alla andra loggar `Information` skickar du endast nivå och högre till Application Insights.

Kategorivärdet i [host.json] styr loggning för alla kategorier som börjar med samma värde. `Host`i [host.json-kontroller] `Host.General`som `Host.Executor` `Host.Results`loggar för , , och så vidare.

Om [host.json] innehåller flera kategorier som börjar med samma sträng, matchas de längre först. Anta att du vill ha `Host.Aggregator` allt `Error` från körningen `Host.Aggregator` förutom att `Information` logga på nivå, men du vill logga på nivån:

### <a name="version-2x-and-later"></a>Version 2.x och senare

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x 

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

Om du vill dölja alla loggar för `None`en kategori kan du använda loggnivå . Inga loggar är skrivna med den kategorin och det finns ingen loggnivå ovanför den.

## <a name="configure-the-aggregator"></a>Konfigurera aggregatorn

Som nämnts i föregående avsnitt sammanställer körningen data om funktionskörningar under en tidsperiod. Standardperioden är 30 sekunder eller 1 000 körningar, beroende på vilket som inträffar först. Du kan konfigurera den här inställningen i [filen host.json.]  Här är ett exempel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurera sampling

Application Insights har en [samplingsfunktion](../azure-monitor/app/sampling.md) som kan skydda dig från att producera för mycket telemetridata vid slutförda körningar vid hög belastning. När hastigheten för inkommande körningar överskrider ett angivet tröskelvärde börjar Application Insights slumpmässigt ignorera några av de inkommande körningarna. Standardinställningen för maximalt antal körningar per sekund är 20 (fem i version 1.x). Du kan konfigurera sampling i [host.json].  Här är ett exempel:

### <a name="version-2x-and-later"></a>Version 2.x och senare

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20
      }
    }
  }
}
```

### <a name="version-1x"></a>Version 1.x 

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

> [!NOTE]
> [Sampling](../azure-monitor/app/sampling.md) är aktiverat som standard. Om du verkar sakna data kan du behöva justera samplingsinställningarna så att de passar just ditt övervakningsscenario.

## <a name="write-logs-in-c-functions"></a>Skriva loggar i C#-funktioner

Du kan skriva loggar i funktionskoden som visas som spårningar i Application Insights.

### <a name="ilogger"></a>ILogger

Använd en [ILogger-parameter](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) i `TraceWriter` dina funktioner i stället för en parameter. Loggar som skapas med hjälp `TraceWriter` av `ILogger` gå till Application Insights, men låter dig göra [strukturerad loggning](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Med `ILogger` ett objekt `Log<level>` [anropar du förlängningsmetoder på ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) för att skapa loggar. Följande kod skriver `Information` loggar med kategorin "Function.<YOUR_FUNCTION_NAME>. användare."

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturerad loggning

Ordningen på platshållarna, inte deras namn, avgör vilka parametrar som används i loggmeddelandet. Anta att du har följande kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Om du behåller samma meddelandesträng och återför ordningen på parametrarna, skulle den resulterande meddelandetexten ha värdena på fel ställen.

Platshållare hanteras på det här sättet så att du kan göra strukturerad loggning. Application Insights lagrar parameternamn-värde-par och meddelandesträngen. Resultatet är att meddelandeargumenten blir fält som du kan fråga efter.

Om loggermetodanropet ser ut som föregående `customDimensions.prop__rowKey`exempel kan du fråga fältet . Prefixet `prop__` läggs till för att säkerställa att det inte finns några kollisioner mellan fält som körningen lägger till och fält som funktionskoden lägger till.

Du kan också fråga på den ursprungliga meddelandesträngen genom att referera till fältet `customDimensions.prop__{OriginalFormat}`.  

Här är ett exempel JSON representation av `customDimensions` data:

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

### <a name="custom-metrics-logging"></a>Anpassad loggning av mått

I C#-skriptfunktioner kan `LogMetric` du använda `ILogger` tilläggsmetoden för att skapa anpassade mått i Application Insights. Här är ett exempel metodanrop:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Den här koden är `TrackMetric` ett alternativ till att anropa med hjälp av API:et för Application Insights för .NET.

## <a name="write-logs-in-javascript-functions"></a>Skriva loggar i JavaScript-funktioner

Använd `context.log` för att skriva loggar i Node.js. Strukturerad loggning är inte aktiverat.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Anpassad loggning av mått

När du kör på [version 1.x](functions-versions.md#creating-1x-apps) av funktionerna Funktioner kan nod.js-funktionerna använda `context.log.metric` metoden för att skapa anpassade mått i Application Insights. Den här metoden stöds för närvarande inte i version 2.x och senare. Här är ett exempel metodanrop:

```javascript
context.log.metric("TestMetric", 1234);
```

Den här koden är `trackMetric` ett alternativ till att anropa med hjälp av Node.js SDK for Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Logga anpassad telemetri i C#-funktioner

Det finns en funktionsspecifik version av Application Insights SDK som du kan använda för att skicka anpassade telemetridata från dina funktioner till Application Insights: [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Använd följande kommando från kommandotolken för att installera det här paketet:

# <a name="command"></a>[Kommandot](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

I det här `<VERSION>` kommandot ersätter du med en version av det här paketet som stöder din installerade version av [Microsoft.Azure.WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

I följande C#-exempel används det [anpassade telemetri-API:et](../azure-monitor/app/api-custom-events-metrics.md). Exemplet är för ett .NET-klassbibliotek, men Application Insights-koden är densamma för C#-skriptet.

### <a name="version-2x-and-later"></a>Version 2.x och senare

Version 2.x och senare versioner av körningen använder nyare funktioner i Application Insights för att automatiskt korrelera telemetri med den aktuella åtgärden. Du behöver inte ange åtgärden `Id`manuellt, `ParentId`eller `Name` fälten.

```cs
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
using System.Linq;

namespace functionapp0915
{
    public class HttpTrigger2
    {
        private readonly TelemetryClient telemetryClient;

        /// Using dependency injection will guarantee that you use the same configuration for telemetry collected automatically and manually.
        public HttpTrigger2(TelemetryConfiguration telemetryConfiguration)
        {
            this.telemetryClient = new TelemetryClient(telemetryConfiguration);
        }

        [FunctionName("HttpTrigger2")]
        public Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // Parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            this.telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
            this.telemetryClient.TrackMetric(metric);

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
            dependency.Context.User.Id = name;
            this.telemetryClient.TrackDependency(dependency);

            return Task.FromResult<IActionResult>(new OkResult());
        }
    }
}
```

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) är det api som för närvarande rekommenderas för att skapa ett mått.

### <a name="version-1x"></a>Version 1.x

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

            // Parse query parameter
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
        }
        
        // Correlate all telemetry with the current Function invocation
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

Ring inte `TrackRequest` eller `StartOperation<RequestTelemetry>` för att du ser dubblettbegäranden för en funktionsanrop.  Funktionskörningen spårar automatiskt begäranden.

Ställ inte `telemetryClient.Context.Operation.Id`in. Den här globala inställningen orsakar felaktig korrelation när många funktioner körs samtidigt. Skapa i stället en ny`DependencyTelemetry`telemetriinstans ( , `EventTelemetry`) och ändra egenskapen. `Context` Skicka sedan telemetriinstansen `Track` till `TelemetryClient` motsvarande`TrackDependency()` `TrackEvent()`metod `TrackMetric()`på ( , , ). Den här metoden säkerställer att telemetrin har rätt korrelationsinformation för den aktuella funktionsanropet.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Logga anpassad telemetri i JavaScript-funktioner

Här är ett exempel kodavsnitt som skickar anpassad telemetri med [Application Insights Node.js SDK:](https://github.com/microsoft/applicationinsights-node.js)

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

    context.done();
};
```

Parametern `tagOverrides` ställer `operation_Id` in funktionens anrops-ID. Med den här inställningen kan du korrelera all automatiskt genererad och anpassad telemetri för en viss funktionsanrop.

## <a name="dependencies"></a>Beroenden

Funktioner v2 samlar automatiskt in beroenden för HTTP-begäranden, ServiceBus, EventHub och SQL.

Du kan skriva anpassad kod för att visa beroenden. Exempel på exempel finns i exempelkoden i [avsnittet C#-anpassad telemetri](#log-custom-telemetry-in-c-functions). Exempelkoden resulterar i en *programmappning* i Application Insights som ser ut som följande bild:

![Programkarta](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Aktivera Application Insights-integrering

För att en funktionsapp ska kunna skicka data till Application Insights måste den känna till instrumenteringsnyckeln för en Application Insights-resurs. Nyckeln måste finnas i en appinställning med namnet **APPINSIGHTS_INSTRUMENTATIONKEY**.

När du skapar din funktionsapp [i Azure-portalen](functions-create-first-azure-function.md), från kommandoraden med hjälp av [Azure Functions Core Tools](functions-create-first-azure-function-azure-cli.md)eller genom att använda Visual Studio [Code](functions-create-first-function-vs-code.md)aktiveras Integrering av Programinsikter som standard. Application Insights-resursen har samma namn som din funktionsapp och skapas antingen i samma region eller i närmaste region.

### <a name="new-function-app-in-the-portal"></a>Ny funktionsapp i portalen

Om du vill granska application insights-resursen som skapas markerar du den för att expandera fönstret **Application Insights.** Du kan ändra namnet på den **nya resursen** eller välja en annan **plats** i en [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data.

![Aktivera application insights när du skapar en funktionsapp](media/functions-monitoring/enable-ai-new-function-app.png)

När du väljer **Skapa**skapas en application insights-resurs `APPINSIGHTS_INSTRUMENTATIONKEY` med funktionsappen, som har uppsättningen i programinställningarna. Allt är klart.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Lägg till i en befintlig funktionsapp 

När du skapar en funktionsapp med [Visual Studio](functions-create-your-first-function-visual-studio.md)måste du skapa application insights-resursen. Du kan sedan lägga till instrumenteringsnyckeln från den resursen som en programinställning i funktionsappen.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Tidiga versioner av Funktioner används inbyggd övervakning, vilket inte längre rekommenderas. När du aktiverar Integrering av Application Insights för en sådan funktionsapp måste du också [inaktivera inbyggd loggning](#disable-built-in-logging).  

## <a name="report-issues"></a>Rapportera problem

Om du vill rapportera ett problem med Application Insights-integrering i Funktioner eller göra ett förslag eller en begäran [skapar du ett problem i GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Strömmande loggar

När du utvecklar ett program vill du ofta se vad som skrivs till loggarna i nära realtid när du kör i Azure.

Det finns två sätt att visa en ström av loggfiler som genereras av dina funktionskörningar.

* **Inbyggd loggströmning:** Med App Service-plattformen kan du visa en ström av dina programloggfiler. Detta motsvarar utdata som visas när du felsöker dina funktioner under [lokal utveckling](functions-develop-local.md) och när du använder fliken **Test** i portalen. All loggbaserad information visas. Mer information finns i [Strömma loggar](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Den här streamingmetoden stöder endast en enda instans och kan inte användas med en app som körs på Linux i en förbrukningsplan.

* **Live Metrics Stream:** när din funktionsapp är [ansluten till Application Insights](#enable-application-insights-integration)kan du visa loggdata och andra mått i nära realtid i Azure-portalen med hjälp av [Live Metrics Stream](../azure-monitor/app/live-stream.md). Använd den här metoden när du övervakar funktioner som körs på flera instanser eller på Linux i en förbrukningsplan. Den här metoden använder [exempeldata](#configure-sampling).

Loggströmmar kan visas både i portalen och i de flesta lokala utvecklingsmiljöer. 

### <a name="portal"></a>Portalen

Du kan visa båda typerna av loggströmmar i portalen.

#### <a name="built-in-log-streaming"></a>Inbyggd loggströmning

Om du vill visa strömmande loggar i portalen väljer du fliken **Plattformsfunktioner** i funktionsappen. Välj sedan **Monitoring**Logga **direkt**.

![Aktivera strömmande loggar i portalen](./media/functions-monitoring/enable-streaming-logs-portal.png)

Detta ansluter din app till loggströmningstjänsten och programloggar visas i fönstret. Du kan växla mellan **programloggar** och **webbserverloggar**.  

![Visa strömmande loggar i portalen](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live-ström med mätvärden

Om du vill visa livestatistikströmmen för din app väljer du fliken **Översikt för** din funktionsapp. När du har aktiverat Application Insights visas länken **Application Insights** under **Konfigurerade funktioner**. Den här länken tar dig till sidan Programstatistik för din app.

I Application Insights väljer du **Live Metrics Stream**. [Exempelvis loggposter](#configure-sampling) visas under **Exempeltelemetri**.

![Visa livestatistikströmmen i portalen](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio-koden

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Kärnverktyg

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Du kan aktivera direktuppspelningsloggar med hjälp av [Azure CLI](/cli/azure/install-azure-cli). Använd följande kommandon för att logga in, välja din prenumeration och strömma loggfiler:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Du kan aktivera direktuppspelningsloggar med hjälp av [Azure PowerShell](/powershell/azure/overview). För PowerShell använder du följande kommandon för att lägga till ditt Azure-konto, välja din prenumeration och strömma loggfiler:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Inaktivera inbyggd loggning

När du aktiverar Application Insights inaktiverar du den inbyggda loggning som använder Azure Storage. Den inbyggda loggningen är användbar för testning med lätta arbetsbelastningar, men är inte avsedd för produktion med hög belastning. För produktionsövervakning rekommenderar vi Application Insights. Om inbyggd loggning används i produktionen kan loggningsposten vara ofullständig på grund av begränsning på Azure Storage.

Om du vill inaktivera inbyggd `AzureWebJobsDashboard` loggning tar du bort appinställningen. Information om hur du tar bort appinställningar i Azure-portalen finns i avsnittet **Programinställningar** i [Hur du hanterar en funktionsapp](functions-how-to-use-azure-function-app-settings.md#settings). Innan du tar bort appinställningen kontrollerar du att inga befintliga funktioner i samma funktionsapp använder inställningen för Azure Storage-utlösare eller bindningar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core-loggning](/aspnet/core/fundamentals/logging/)

[värd.json]: functions-host-json.md

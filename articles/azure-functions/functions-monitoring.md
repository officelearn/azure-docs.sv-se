---
title: Övervaka Azure Functions
description: Lär dig hur du använder Azure Application Insights med Azure Functions för att övervaka körning av funktionen.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: fecf6759dd7b277dda10fa2656e6ae9407490370
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64922968"
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

[Azure Functions](functions-overview.md) erbjuder inbyggd integrering med [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) att övervaka funktioner. Den här artikeln visar hur du konfigurerar Azure Functions för att skicka systemgenererade loggfiler till Application Insights.

Vi rekommenderar att du använder Application Insights eftersom den samlar in log, prestanda och Feldata. Automatiskt identifierar prestandaavvikelser och inkluderar kraftfulla analysverktyg för att hjälpa dig att diagnostisera problem och förstå hur dina funktioner används. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Du kan även använda Application Insights under lokal funktionen apputvecklingen för projektet. Mer information finns i [vad är Application Insights?](../azure-monitor/app/app-insights-overview.md).

Eftersom nödvändiga Application Insights-instrumentationen är inbyggt i Azure Functions, allt du behöver är en giltig instrumentationsnyckel att ansluta din funktionsapp till en Application Insights-resurs.

## <a name="application-insights-pricing-and-limits"></a>Priser för Application Insights och begränsningar

Du kan testa Application Insights-integrering med Funktionsappar utan kostnad. Det finns en daglig gräns för hur mycket data kan bearbetas utan kostnad. Du kan når gränsen under testningen. Azure tillhandahåller portalen och e-postaviseringar när du närmar sig dagliga gränsen. Om du missar aviseringarna och nått gränsen för, visas inte nya loggar i Application Insights-frågor. Tänk på gränsen för att undvika onödiga tidsåtgången för felsökning. Mer information finns i [hantera priser och datavolymer i Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Aktivera Application Insights-integrering

Den måste ange instrumenteringsnyckeln för en Application Insights-resurs för en funktionsapp kan skicka data till Application Insights. Nyckeln måste vara i en app som inställning med namnet **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Ny funktionsapp i portalen

När du [skapar funktionsappen i Azure-portalen](functions-create-first-azure-function.md), Application Insights-integrering är aktiverad som standard. Application Insights-resursen har samma namn som din funktionsapp och den har skapats i samma region eller i närmaste regionen.

Om du vill granska Application Insights-resursen håller på att skapas, väljer du den för att expandera den **Application Insights** fönster. Du kan ändra den **nytt resursnamn** eller välj en annan **plats** i en [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data.

![Aktivera Application Insights när du skapar en funktionsapp](media/functions-monitoring/enable-ai-new-function-app.png)

När du väljer **skapa**, en Application Insights-resursen har skapats med din funktionsapp, som har den `APPINSIGHTS_INSTRUMENTATIONKEY` anges i inställningarna för programmet. Allt är klart.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Lägga till i en befintlig funktionsapp 

När du skapar en funktionsapp med hjälp de [Azure CLI](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md), eller [Visual Studio Code](functions-create-first-function-vs-code.md), måste du skapa Application Insights-resursen. Du kan sedan lägga till instrumentationsnyckeln från den här resursen som en programinställning i din funktionsapp.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Tidiga versioner av funktioner används inbyggd övervakning, vilket inte längre rekommenderas. När du aktiverar Application Insights-integrering för en funktionsapp måste du också [inaktivera inbyggd loggning](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Visa telemetri i fliken övervakning

Med [Application Insights-integrering aktiverat](#enable-application-insights-integration), du kan visa dessa data i den **övervakaren** fliken.

1. I sidan för funktionsappar, väljer du en funktion som har kört minst en gång när Application Insights har konfigurerats. Välj sedan den **övervakaren** fliken.

   ![Välj fliken övervakning](media/functions-monitoring/monitor-tab.png)

1. Välj **uppdatera** med jämna mellanrum, tills listan funktionsanrop visas.

   Det kan ta upp till fem minuter för en lista visas när telemetriklienten slår ihop data för överföring till servern. (Fördröjningen gäller inte för den [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tjänsten ansluter till Functions värden när du läser in sidan, så att loggarna strömmas direkt till sidan.)

   ![Anrop listan](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Om du vill se loggar för ett visst funktionsanrop i **datum** kolumnlänken för det anropet.

   ![Länka anropsinformation](media/functions-monitoring/invocation-details-link-ai.png)

   Loggning utdata för det anropet visas i en ny sida.

   ![Anropsinformation](media/functions-monitoring/invocation-details-ai.png)

Du kan se att båda sidorna har en **kör i Application Insights** länka till Application Insights Analytics-fråga som hämtar data.

![Kör i Application Insights](media/functions-monitoring/run-in-ai.png)

Följande fråga visas. Du kan se att listan anrop är begränsad till de senaste 30 dagarna. I listan visas mer än 20 rader (`where timestamp > ago(30d) | take 20`). Anrop information om listan är för de senaste 30 dagarna utan gräns.

![Application Insights Analytics anrop listan](media/functions-monitoring/ai-analytics-invocation-list.png)

Mer information finns i [fråga telemetridata](#query-telemetry-data) senare i den här artikeln.

## <a name="view-telemetry-in-application-insights"></a>Visa telemetri i Application Insights

Öppna Application Insights från en funktionsapp i Azure-portalen, gå till funktionsappen **översikt** sidan. Under **konfigurerats funktioner**väljer **Application Insights**.

![Öppna Application Insights från sidan för funktionsappar översikt](media/functions-monitoring/ai-link.png)

Information om hur du använder Application Insights finns i den [dokumentation om Application Insights](https://docs.microsoft.com/azure/application-insights/). Det här avsnittet visas några exempel på hur du visar data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [i avsnittet om hur du konfigurerar och anpassar dessa data](#configure-categories-and-log-levels).

![Fliken för Application Insights, översikt](media/functions-monitoring/metrics-explorer.png)

Följande områden i Application Insights kan vara användbart när du utvärderar beteende, prestanda och fel i dina funktioner:

| Tab | Beskrivning |
| ---- | ----------- |
| **[fel](../azure-monitor/app/asp-net-exceptions.md)** |  Skapa diagram och aviseringar som baseras på funktionen fel och undantag. Den **Åtgärdsnamnet** är funktionsnamnet. Fel i beroenden visas inte om inte du implementera anpassad telemetri för beroenden. |
| **[Prestanda](../azure-monitor/app/performance-counters.md)** | Analysera problem med prestanda. |
| **Servrar** | Visa resursanvändningen och dataflöde per server. Dessa data kan vara användbart för felsökning av scenarier där funktioner bogging ned dina underliggande resurser. Servrar kallas **Cloud rollinstanser**. |
| **[Mått](../azure-monitor/app/metrics-explorer.md)** | Skapa diagram och aviseringar som baseras på mått. Mått är antalet funktionsanrop, körningstid och Slutförandefrekvens. |
| **[Live-ström med mätvärden](../azure-monitor/app/live-stream.md)** | Visa måttdata som den har skapats i realtid. |

## <a name="query-telemetry-data"></a>Telemetridata för fråga

[Application Insights Analytics](../azure-monitor/app/analytics.md) ger dig tillgång till alla telemetridata i form av tabeller i en databas. Analytics tillhandahåller ett frågespråk för extrahering, manipulera och visualisera dina data.

![Välj Analytics](media/functions-monitoring/select-analytics.png)

![Analytics-exempel](media/functions-monitoring/analytics-traces.png)

Här är ett exempel på sökfråga som visar fördelningen av begäranden per worker under de senaste 30 minuterna.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabeller som är tillgängliga som visas i den **schemat** fliken till vänster. Du kan hitta data som genereras av funktionsanrop i följande tabeller:

| Tabell | Beskrivning |
| ----- | ----------- |
| **spårningar** | Loggar som skapats av körningen, av funktionskoden. |
| **Begäranden** | En begäran för varje funktionsanrop. |
| **Undantag** | Alla undantag från körningen. |
| **customMetrics** | Totalt antal lyckade eller misslyckade anrop, Slutförandefrekvens och varaktighet. |
| **customEvents** | Händelser som spåras av körning, till exempel: HTTP-begäranden som utlöser en funktion. |
| **performanceCounters** | Information om prestanda för de servrar som funktionerna som körs på. |

De andra tabellerna är för tillgänglighetstester och telemetri för klienten och webbläsare. Du kan implementera anpassade telemetri för att lägga till data i dem.

Inom varje tabell är några av specifika funktioner i en `customDimensions` fält.  Till exempel följande fråga hämtar alla spårningar som har Loggnivå `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Körningen ger den `customDimensions.LogLevel` och `customDimensions.Category` fält. Du kan ange ytterligare fält i loggarna som du skriver i funktionskoden. Se [strukturerad loggning](#structured-logging) senare i den här artikeln.

## <a name="configure-categories-and-log-levels"></a>Konfigurera kategorier och logga nivåer

Du kan använda Application Insights utan anpassad konfiguration. Standardkonfigurationen kan leda till stora mängder data. Om du använder ett Visual Studio-prenumerationen kan du uppnått datakapaciteten för Application Insights. Senare i den här artikeln får du lära dig hur du konfigurerar och anpassar de data som dina funktioner skicka till Application Insights. För en funktionsapp loggning har konfigurerats i den [host.json] fil.

### <a name="categories"></a>Categories

Azure Functions-loggaren innehåller en *kategori* för varje logg. Kategorin som anger vilken del av runtime-kod eller Funktionskoden skrev loggen. 

Functions-körning skapar loggar med en kategori som börjar med ”värd”. ”Funktionen igång”, ”funktionen körs” och ”slutfört” funktionsloggar har kategorin ”Host.Executor”. 

Om du skriver loggar i Funktionskoden är sina kategorin ”-funktion”.

### <a name="log-levels"></a>Loggningsnivåerna

Azure Functions-loggaren innehåller också en *certifikatutfärdarnivå* med varje logg. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) är en uppräkning och heltal koden visar relativa prioriteten:

|LogLevel    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritisk    | 5 |
|Ingen        | 6 |

Certifikatutfärdarnivå `None` förklaras i nästa avsnitt. 

### <a name="log-configuration-in-hostjson"></a>Konfigurationen av loggen i host.json

Den [host.json] filen konfigurerar hur mycket loggning som en funktionsapp skickar till Application Insights. För varje kategori, kan du ange den lägsta loggningsnivån för att skicka. Det finns två exempel: den första exemplet är riktad mot den [funktionskörningen version 2.x](functions-versions.md#version-2x) (.NET Core) och det andra exemplet avser version 1.x-körningen.

### <a name="version-2x"></a>Version 2.x

V2.x runtime använder den [.NET Core loggning Filterhierarkin](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

Det här exemplet ställer in följande regler:

* För loggar med kategori `Host.Results` eller `Function`, skicka endast `Error` nivå och högre till Application Insights. Loggar för `Warning` nivå och nedan ignoreras.
* För loggar med kategori `Host.Aggregator`, skicka alla loggar till Application Insights. Den `Trace` loggningsnivån är samma som det vissa tangenttryckningar kallar `Verbose`, men använda `Trace` i den [host.json] fil.
* För alla andra loggar Skicka endast `Information` nivå och högre till Application Insights.

Kategori-värdet i [host.json] styr loggning för alla kategorier som börjar med samma värde. `Host` i [host.json] kontroller loggning för `Host.General`, `Host.Executor`, `Host.Results`och så vidare.

Om [host.json] innehåller flera kategorier som börjar med samma sträng, de längre som matchas. Anta att du vill att allt från runtime utom `Host.Aggregator` att logga in på `Error` nivå, men du vill att `Host.Aggregator` att logga in på den `Information` nivå:

### <a name="version-2x"></a>Version 2.x 

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

Om du inte alla loggar för en kategori, kan du använda Loggnivå `None`. Inga loggar är skrivna med den kategorin och det finns inga Loggnivå ovanför.

I följande avsnitt beskrivs olika kategorier av loggar som körningen skapar. 

### <a name="category-hostresults"></a>Category Host.Results

De här loggarna visas som ”requests” i Application Insights. De anger lyckad eller misslyckad för en funktion.

![Begäranden-diagram](media/functions-monitoring/requests-chart.png)

Alla dessa loggar är skrivna på `Information` nivå. Om du filtrerar på `Warning` eller senare, visas inte någon av dessa data.

### <a name="category-hostaggregator"></a>Kategori Host.Aggregator

Dessa loggar ge antal och medelvärden funktionsanrop via en [konfigurerbara](#configure-the-aggregator) period tid. Standardvärdet är 30 sekunder eller 1 000 resultat, beroende på vilket som inträffar först. 

Loggarna är tillgängliga i den **customMetrics** tabellen i Application Insights. Exempel är antalet körningar, Slutförandefrekvens och varaktighet.

![customMetrics fråga](media/functions-monitoring/custom-metrics-query.png)

Alla dessa loggar är skrivna på `Information` nivå. Om du filtrerar på `Warning` eller senare, visas inte någon av dessa data.

### <a name="other-categories"></a>Kategorier

Alla loggar för kategorier än som redan i listan är tillgängliga i den **spårningar** tabellen i Application Insights.

![spårningar fråga](media/functions-monitoring/analytics-traces.png)

Alla loggar med kategorier som börjar med `Host` skrivs av Functions-körningen. ”Funktion igång” och ”slutfört” funktionsloggar har kategorin `Host.Executor`. För lyckade körningar dessa loggar är `Information` nivå. Undantag loggas på `Error` nivå. Körningen skapar även `Warning` nivå loggfiler, till exempel: köa meddelanden som skickas till skadliga kön.

Loggar som skrivits av Funktionskoden har kategori `Function` och kan vara valfri nivå.

## <a name="configure-the-aggregator"></a>Konfigurera aggregatorn

Enligt vad som anges i föregående avsnitt, aggregerar körningen data om funktionskörningar under en viss tidsperiod. Standardvärdet är 30 sekunder eller 1 000 körs, beroende på vilket som kommer först. Du kan konfigurera den här inställningen i den [host.json] fil.  Här är ett exempel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurera sampling

Application Insights har en [sampling](../azure-monitor/app/sampling.md) funktion som kan skydda dig från att producera för mycket telemetridata på slutförts körningar vid tidpunkter med hög belastning. När mängden inkommande körningar överskrider ett angivet tröskelvärde, startar Application Insights att ignorera slumpmässigt några av de inkommande körningar. Standardinställningen för maximalt antal körningar per sekund är 20 (fem i version 1.x). Du kan konfigurera linjer i [host.json].  Här är ett exempel:

### <a name="version-2x"></a>Version 2.x 

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
> [Sampling](../azure-monitor/app/sampling.md) är aktiverat som standard. Om du verkar sakna data, kan du behöva justera inställningarna för sampling så att de passar din övervakning scenariot.

## <a name="write-logs-in-c-functions"></a>Sparar loggar på C#-funktioner

Du kan skriva loggar i Funktionskoden som visas som spårningar i Application Insights.

### <a name="ilogger"></a>ILogger

Använd en [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) parameter i dina funktioner i stället för en `TraceWriter` parametern. Loggar som skapats med hjälp av `TraceWriter` går du till Application Insights, men `ILogger` kan du göra [strukturerad loggning](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Med en `ILogger` objekt kan du anropa `Log<level>` [tilläggsmetoder på ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) att skapa loggar. I följande kod skrivningar `Information` loggar med kategori ”-funktion”.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturerad loggning

Ordningen på platshållare, inte namnen avgör vilka parametrar som används i loggmeddelandet. Anta att du har följande kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Om du behålla samma meddelande sträng och vända på ordningen på parametrarna skulle resulterande meddelandetexten ha värden på fel platser.

Platshållare hanteras på så sätt så att du kan göra strukturerad loggning. Application Insights lagrar parametern namn / värde-par och sträng för meddelandet. Resultatet är att argumenten meddelandet blir fält som du kan fråga på.

Om din logger-metodanrop ser ut som i föregående exempel, kan du fråga fältet `customDimensions.prop__rowKey`. Den `prop__` prefix läggs lägger till att säkerställa att det finns inga konflikter mellan fälten körningen lägger till och fält funktionskoden.

Du kan också fråga på den ursprungliga Meddelandesträngen genom att referera till fältet `customDimensions.prop__{OriginalFormat}`.  

Här är en exempel-JSON-representation av `customDimensions` data:

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

### <a name="custom-metrics-logging"></a>Anpassade mått loggning

I C#-skript-funktioner, kan du använda den `LogMetric` tilläggsmetod på `ILogger` att skapa anpassade mått i Application Insights. Här är ett exempel metodanrop:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Den här koden är ett alternativ till att anropa `TrackMetric` med hjälp av Application Insights API för .NET.

## <a name="write-logs-in-javascript-functions"></a>Sparar loggar på JavaScript-funktioner

I Node.js-funktion, använder `context.log` loggfiler ska skrivas. Strukturerad loggning är inte aktiverad.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Anpassade mått loggning

När du kör på [version 1.x](functions-versions.md#creating-1x-apps) Functions-körning Node.js-funktioner kan använda den `context.log.metric` metod för att skapa anpassade mått i Application Insights. Den här metoden stöds inte för närvarande i version 2.x. Här är ett exempel metodanrop:

```javascript
context.log.metric("TestMetric", 1234);
```

Den här koden är ett alternativ till att anropa `trackMetric` med hjälp av Node.js-SDK för Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Logga in anpassad telemetri C# funktioner

Du kan använda den [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) nuget för att skicka telemetri om anpassade data till Application Insights. Följande C# exemplet används den [telemetri om anpassade API: et](../azure-monitor/app/api-custom-events-metrics.md). I exemplet avser en .NET-klassbiblioteket, men den Application Insights-koden är detsamma för C#-skript.

### <a name="version-2x"></a>Version 2.x

Version 2.x-körningen använder nyare funktioner i Application Insights för att korrelera automatiskt telemetri med den aktuella åtgärden. Behöver du inte ange manuellt igen `Id`, `ParentId`, eller `Name` fält.

```cs
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;

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

Anropa inte `TrackRequest` eller `StartOperation<RequestTelemetry>` ser du dubbla begäranden för ett funktionsanrop.  Functions-körning spårar automatiskt begäranden.

Konfigurerar inte `telemetryClient.Context.Operation.Id`. Den här globala inställningen gör felaktiga korrelation när många funktioner som körs samtidigt. I stället skapar en ny instans av telemetri (`DependencyTelemetry`, `EventTelemetry`) och ändra dess `Context` egenskapen. Sedan skickar telemetri-instans till motsvarande `Track` metoden på `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Den här metoden garanterar att telemetri har rätt Korrelations-informationen för den aktuella funktionsanrop.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Log anpassad telemetri i JavaScript-funktioner

Den [Application Insights Node.js SDK](https://www.npmjs.com/package/applicationinsights) håller på att beta. Här är exempelkod som skickar anpassad telemetri till Application Insights:

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

Den `tagOverrides` parameteruppsättningar den `operation_Id` till funktionens anrops-ID. Den här inställningen kan du kombinera alla automatiskt genererade och anpassad telemetri för en viss funktionsanrop.

## <a name="dependencies"></a>Beroenden

Functions v2 samlar automatiskt in beroenden för HTTP-begäranden, ServiceBus och SQL.

Du kan skriva anpassad kod för att visa beroenden. Exempel finns i exempelkoden i den [ C# anpassad telemetri avsnittet](#log-custom-telemetry-in-c-functions). Exempelkoden resulterar i en *programkartan* i Application Insights som ser ut som följande bild:

![Programkarta](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Rapportera problem

Rapportera ett problem med Application Insights-integrering i funktioner och ge förslag eller begäranden, [skapa ett ärende i GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="streaming-logs"></a>Direktuppspelningsloggar

När du utvecklar ett program, kan det ofta vara användbart att visa loggningsinformation i nära realtid. Du kan visa en dataström med loggfiler som skapas av dina funktioner i Azure portal eller i en kommandorad session på den lokala datorn.

Detta motsvarar utdata visas när du felsöker dina funktioner under [lokal utveckling](functions-develop-local.md). Mer information finns i [så strömma loggar](../app-service/troubleshoot-diagnostic-logs.md#streamlogs).

> [!NOTE]
> Direktuppspelningsloggar stöder endast en instans av Functions-värden. När din funktion skalas till flera instanser visas inte data från andra instanser i strömmen log. Den [Live Metrics Stream](../azure-monitor/app/live-stream.md) i Application Insights stöd för flera instanser. Medan även i nära realtid, strömningsanalyser även baseras på [exempeldata](#configure-sampling).

### <a name="portal"></a>Portalen

Om du vill visa direktuppspelningsloggarna i portal, Välj den **plattformsfunktioner** fliken i din funktionsapp. Sedan, under **övervakning**, Välj **loggströmningen**.

![Aktivera direktuppspelningsloggarna i portalen](./media/functions-monitoring/enable-streaming-logs-portal.png)

Det här ansluter din app till loggen strömningstjänst och programloggar visas i fönstret. Du kan växla mellan **programloggar** och **Web serverloggar**.  

![Visa direktuppspelningsloggarna i portalen](./media/functions-monitoring/streaming-logs-window.png)

### <a name="azure-cli"></a>Azure CLI

Du kan aktivera direktuppspelningsloggarna med hjälp av den [Azure CLI](/cli/azure/install-azure-cli). Använd följande kommandon för att logga in, Välj din prenumeration och loggfiler för stream:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Du kan aktivera direktuppspelningsloggarna med hjälp av [Azure PowerShell](/powershell/azure/overview). För PowerShell, använder du följande kommandon för att lägga till ditt Azure-konto, väljer din prenumeration och loggfiler för stream:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="disable-built-in-logging"></a>Inaktivera inbyggd loggning

När du aktiverar Application Insights kan du inaktivera inbyggd loggning som använder Azure Storage. Inbyggd loggning är användbart för att testa med lätta arbetsbelastningar, men är inte avsett för användning i produktion för hög belastning. Vi rekommenderar Application Insights för övervakning av produktionen. Om du använder inbyggd loggning i produktion, kan loggning-posten vara ofullständiga på grund av begränsningar i Azure Storage.

Om du vill inaktivera inbyggd loggning, ta bort den `AzureWebJobsDashboard` appinställningen. Information om hur du tar bort app-inställningar i Azure-portalen finns i den **programinställningar** delen av [hantera en funktionsapp](functions-how-to-use-azure-function-app-settings.md#settings). Innan du tar bort appinställningen, kontrollera att inga befintliga funktioner i samma funktionsapp använda inställningen för Azure Storage-utlösare och bindningar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core-loggning](/aspnet/core/fundamentals/logging/)

[host.json]: functions-host-json.md

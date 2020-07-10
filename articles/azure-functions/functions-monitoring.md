---
title: Övervaka Azure Functions
description: Lär dig hur du använder Azure Application insikter med Azure Functions för att övervaka funktions körning.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 04/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5560d24601b8aef0d8a4058cc2c04e27e9c86362
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170419"
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

[Azure Functions](functions-overview.md) erbjuder inbyggd integrering med [Azure Application insikter](../azure-monitor/app/app-insights-overview.md) för att övervaka funktioner. Den här artikeln visar hur du konfigurerar Azure Functions att skicka systemgenererade loggfiler till Application Insights.

Vi rekommenderar att du använder Application Insights eftersom den samlar in logg-, prestanda-och fel data. Den identifierar automatiskt prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina funktioner används. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Du kan även använda Application Insights under utveckling av program projekt i lokal funktion. Mer information finns i [Vad är Application Insights?](../azure-monitor/app/app-insights-overview.md).

Eftersom den nödvändiga Application Insights Instrumentation är inbyggd i Azure Functions, är allt du behöver en giltig Instrumentation-nyckel för att ansluta din app till en Application Insights resurs. Instrumentation-nyckeln ska läggas till i program inställningarna när din Function-app-resurs skapas i Azure. Om din Function-app inte redan har den här nyckeln kan du [Ange den manuellt](#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Application Insights priser och begränsningar

Du kan prova Application Insights integration med Azure Functions kostnads fritt. Det finns en daglig gräns för hur mycket data som kan bearbetas kostnads fritt. Du kan trycka på den här gränsen under testningen. Azure tillhandahåller Portal-och e-postaviseringar när du närmar dig den dagliga gränsen. Om du saknar dessa aviseringar och nått gränsen visas inte nya loggar i Application Insights frågor. Var medveten om gränsen för att undvika onödig fel söknings tid. Mer information finns [i hantera priser och data volymer i Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights har en [samplings](../azure-monitor/app/sampling.md) funktion som kan skydda dig från att skapa för mycket telemetri-data vid slutförda körningar vid tider med hög belastning. Sampling är aktiverat som standard. Om du verkar saknas data kan du behöva justera samplings inställningarna så att de passar just ditt övervaknings scenario. Läs mer i [Konfigurera sampling](#configure-sampling).

Den fullständiga listan med Application Insights funktioner som är tillgängliga för din Function-app beskrivs i [Application Insights för Azure Functions funktioner som stöds](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="view-telemetry-in-monitor-tab"></a>Visa telemetri på fliken övervaka

Med [Application Insights integrering aktive rad](#enable-application-insights-integration)kan du Visa telemetridata på fliken **övervaka** .

1. På sidan Function-app väljer du en funktion som har körts minst en gång efter Application Insights har kon figurer ATS. Välj sedan **övervaka** i det vänstra fönstret. Välj **Uppdatera** regelbundet, tills listan över funktions anrop visas.

   ![Lista med anrop](media/functions-monitoring/monitor-tab-ai-invocations.png)

    > [!NOTE]
    > Det kan ta upp till fem minuter innan listan visas medan telemetri-klienten registrerar data för överföring till servern. Fördröjningen gäller inte för [Live Metrics Stream](../azure-monitor/app/live-stream.md). Den här tjänsten ansluter till Functions-värden när du läser in sidan, så loggarna strömmas direkt till sidan.

1. Om du vill se loggar för ett visst funktions anrop, väljer du kolumn länken **date (UTC)** för detta anrop. Loggnings resultatet för det anropet visas på en ny sida.

   ![Information om anrop](media/functions-monitoring/invocation-details-ai.png)

1. Välj **Kör i Application Insights** om du vill visa källan till frågan som hämtar Azure Monitor loggdata i Azure log. Om det här är första gången du använder Azure Log Analytics i din prenumeration, uppmanas du att aktivera det.

1. När du har aktiverat Log Analytics visas följande fråga. Du kan se att frågeresultaten är begränsade till de senaste 30 dagarna ( `where timestamp > ago(30d)` ). Dessutom visar resultatet högst 20 rader ( `take 20` ). Listan med information om anrop för din funktion är däremot under de senaste 30 dagarna utan begränsning.

   ![Lista med Application Insights analys anrop](media/functions-monitoring/ai-analytics-invocation-list.png)

Mer information finns i [query telemetri-data](#query-telemetry-data) senare i den här artikeln.

## <a name="view-telemetry-in-application-insights"></a>Visa telemetri i Application Insights

Om du vill öppna Application Insights från en Function-app i Azure Portal väljer du **Application Insights** under **Inställningar** på den vänstra sidan. Om det här är första gången du använder Application Insights med din prenumeration uppmanas du att aktivera den: Välj **aktivera Application Insights**och välj sedan **Använd** på nästa sida.

![Öppna Application Insights från översikts sidan för Function-appen](media/functions-monitoring/ai-link.png)

Information om hur du använder Application Insights finns i Application Insights- [dokumentationen](https://docs.microsoft.com/azure/application-insights/). I det här avsnittet visas några exempel på hur du kan visa data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [avsnitten om hur du konfigurerar och anpassar telemetridata](#configure-categories-and-log-levels).

![Fliken Application Insights översikt](media/functions-monitoring/metrics-explorer.png)

Följande områden i Application Insights kan vara användbara när du ska utvärdera beteende, prestanda och fel i dina funktioner:

| Undersök
 | Beskrivning |
| ---- | ----------- |
| **[Fel](../azure-monitor/app/asp-net-exceptions.md)** |  Skapa diagram och aviseringar baserat på funktions fel och Server undantag. **Åtgärds namnet** är funktions namnet. Felen i beroenden visas inte om du inte implementerar anpassad telemetri för beroenden. |
| **[Prestanda](../azure-monitor/app/performance-counters.md)** | Analysera prestanda problem genom att Visa resursutnyttjande och data flöde per **moln roll instanser**. Dessa data kan vara användbara för fel sökning av scenarier där funktioner är bogging de underliggande resurserna. |
| **[Mått](../azure-monitor/app/metrics-explorer.md)** | Skapa diagram och aviseringar som baseras på mått. Mått inkluderar antalet funktions anrop, körnings tid och lyckade kostnader. |
| **[Live-mått](../azure-monitor/app/live-stream.md)** | Visa mått data när de skapas i nära real tid. |

## <a name="query-telemetry-data"></a>Fråga telemetri-data

[Application Insights Analytics](../azure-monitor/app/analytics.md) ger dig åtkomst till alla telemetridata i form av tabeller i en databas. Analytics innehåller ett frågespråk för extrahering, manipulering och visualisering av data. 

Välj **loggar** för att utforska eller fråga efter loggade händelser.

![Analys exempel](media/functions-monitoring/analytics-traces.png)

Här är ett exempel på en fråga som visar distributionen av förfrågningar per arbetare under de senaste 30 minuterna.

<pre>
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
</pre>

De tabeller som är tillgängliga visas på fliken **schema** till vänster. Du kan hitta data som genererats av funktions anrop i följande tabeller:

| Tabell | Beskrivning |
| ----- | ----------- |
| **Anden** | Loggar som skapats av körningen och med funktions kod. |
| **begäran** | En begäran för varje funktions anrop. |
| **undantag** | Eventuella undantag som har utlösts av körningen. |
| **customMetrics** | Antalet lyckade och misslyckade anrop, lyckade kostnader och varaktighet. |
| **customEvents** | Händelser som spåras av körningen, till exempel: HTTP-begäranden som utlöser en funktion. |
| **performanceCounters** | Information om prestanda för de servrar som funktionerna körs på. |

De andra tabellerna är för tillgänglighets test och telemetri för klienter och webbläsare. Du kan implementera anpassad telemetri för att lägga till data i dem.

I varje tabell finns vissa av de funktioner som är aktuella i ett `customDimensions` fält.  Följande fråga hämtar till exempel alla spår som har loggnings nivå `Error` .

<pre>
traces 
| where customDimensions.LogLevel == "Error"
</pre>

Körningen innehåller `customDimensions.LogLevel` fälten och `customDimensions.Category` . Du kan ange ytterligare fält i loggar som du skriver i funktions koden. Se [strukturerad loggning](#structured-logging) senare i den här artikeln.

## <a name="configure-categories-and-log-levels"></a>Konfigurera kategorier och logg nivåer

Du kan använda Application Insights utan någon anpassad konfiguration. Standard konfigurationen kan resultera i stora data mängder. Om du använder en Azure-prenumeration från Visual Studio kan du träffa din data Kap för Application Insights. Senare i den här artikeln får du lära dig hur du konfigurerar och anpassar de data som dina funktioner skickar till Application Insights. För en Function-app konfigureras loggning i [host.js] i filen.

### <a name="categories"></a>Kategorier

Azure Functions loggen innehåller en *kategori* för varje logg. Kategorin visar vilken del av körnings koden eller din funktions kod som skrev loggen. Följande diagram beskriver de huvudsakliga kategorierna för loggar som körs av körnings miljön. 

| Kategori | Beskrivning |
| ----- | ----- | 
| Host.Results | Loggarna visar som **begär Anden** i Application Insights. De indikerar en funktion som lyckats eller misslyckats. Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar på `Warning` eller ovanför visas inte någon av dessa data. |
| Host. Aggregator | Dessa loggar ger räknare och medelvärden för funktions anrop under en [konfigurerbar](#configure-the-aggregator) tids period. Standard perioden är 30 sekunder eller 1 000 resultat, beroende på vilket som kommer först. Loggarna är tillgängliga i tabellen **customMetrics** i Application Insights. Exempel är antalet körningar, lyckade kostnader och varaktighet. Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar på `Warning` eller ovanför visas inte någon av dessa data. |

Alla loggar för andra kategorier än dessa är tillgängliga i tabellen **spårning** i Application Insights.

Alla loggar med kategorier som börjar med `Host` skrivs av Functions-körningen. **Funktionen startad** och **Function slutförda** loggar har en kategori `Host.Executor` . För lyckade körningar är dessa loggar `Information` nivå. Undantagen har loggats på `Error` nivå. Körningen skapar också `Warning` nivå loggar, till exempel: köa meddelanden som skickas till en Poison-kö.

Funktions körningen skapar loggar med en kategori som börjar med "värd". I version 1. x `function started` har-, `function executed` -och- `function completed` loggarna kategorin `Host.Executor` . Från och med version 2. x har dessa loggar kategorin `Function.<YOUR_FUNCTION_NAME>` .

Om du skriver loggar i funktions koden är kategorin `Function.<YOUR_FUNCTION_NAME>.User` och kan vara vilken logg nivå som helst. I version 1. x av Functions-körningen är kategorin `Function` .

### <a name="log-levels"></a>Loggnivåer

Azure Functions loggen innehåller också en *logg nivå* med varje logg. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) är en uppräkning och heltals koden indikerar relativ prioritet:

|Loggnivå    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritiskt    | 5 |
|Inget        | 6 |

Logg nivån `None` förklaras i nästa avsnitt. 

### <a name="log-configuration-in-hostjson"></a>Logg konfiguration i host.jspå

[host.jspå] fil konfigurerar hur mycket loggning av en Function-app som ska skickas till Application Insights. För varje kategori anger du den lägsta logg nivå som ska skickas. Det finns två exempel: det första exemplet riktar sig till [version 2. x och senare](functions-versions.md#version-2x) av Functions-körningen (med .net Core) och det andra exemplet är för version 1. x-körningsmiljön.

### <a name="version-2x-and-higher"></a>Version 2. x och högre

Version v2. x och senare versioner av Functions-körningen använder [.net Core Logging filter-hierarkin](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering). 

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

### <a name="version-1x"></a>Version 1. x

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

* För loggar med kategori `Host.Results` eller `Function` , skicka endast `Error` nivån och över till Application Insights. Loggar för `Warning` nivån och nedan ignoreras.
* `Host.Aggregator`Skicka alla loggar till Application Insights för loggar med kategori. `Trace`Logg nivån är samma som för vissa loggar `Verbose` , men används `Trace` i [host.jsi] filen.
* För alla andra loggar skickar du bara `Information` nivån och över den till Application Insights.

Kategori värdet i [host.jspå] kontrollerar loggningen för alla kategorier som börjar med samma värde. `Host`i [host.jspå] kontrollerar loggningen för `Host.General` , `Host.Executor` , `Host.Results` och så vidare.

Om [host.jspå] innehåller flera kategorier som börjar med samma sträng matchas de längre först. Anta att du vill ha allt från körnings miljön förutom `Host.Aggregator` att logga på `Error` nivå, men du vill `Host.Aggregator` Logga på `Information` nivån:

### <a name="version-2x-and-later"></a>Version 2. x och senare

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

### <a name="version-1x"></a>Version 1. x 

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

Om du vill ignorera alla loggar för en kategori kan du använda loggnings nivå `None` . Inga loggar skrivs med den kategorin och det finns ingen loggnings nivå ovanför den.

## <a name="configure-the-aggregator"></a>Konfigurera Aggregator

Enligt vad som anges i föregående avsnitt sammanställer körnings miljön data om funktions körningar under en viss tids period. Standard perioden är 30 sekunder eller 1 000 körningar, beroende på vilket som kommer först. Du kan konfigurera den här inställningen i [host.jspå] filen.  Här är ett exempel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurera sampling

Application Insights har en [samplings](../azure-monitor/app/sampling.md) funktion som kan skydda dig från att skapa för mycket telemetri-data vid slutförda körningar vid tider med hög belastning. När antalet inkommande körningar överskrider ett angivet tröskelvärde börjar Application Insights att ignorera några av de inkommande körningarna slumpmässigt. Standardvärdet för maximalt antal körningar per sekund är 20 (fem i version 1. x). Du kan konfigurera sampling i [host.jspå](https://docs.microsoft.com/azure/azure-functions/functions-host-json#applicationinsights).  Här är ett exempel:

### <a name="version-2x-and-later"></a>Version 2. x och senare

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request"
      }
    }
  }
}
```

I version 2. x kan du undanta vissa typer av telemetri från sampling. I exemplet ovan undantas data av typen `Request` från sampling. Detta säkerställer att *alla* funktions körningar (begär Anden) loggas medan andra typer av telemetri är underkastade sampling.

### <a name="version-1x"></a>Version 1. x 

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

Du kan skriva loggar i funktions koden som visas som spår i Application Insights.

### <a name="ilogger"></a>ILogger

Använd en [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) -parameter i dina funktioner i stället för en `TraceWriter` parameter. Loggar som skapats med hjälp av `TraceWriter` gå till Application Insights, men `ILogger` du kan göra [strukturerad loggning](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Med ett `ILogger` objekt anropar du `Log<level>` [tilläggs metoder på ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) för att skapa loggar. Följande kod skriver `Information` loggarna med kategorin "function. <YOUR_FUNCTION_NAME>. Användare. "

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturerad loggning

Ordningen på plats hållare, inte deras namn, avgör vilka parametrar som används i logg meddelandet. Anta att du har följande kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Om du behåller samma meddelande sträng och ändrar ordningen på parametrarna, skulle den resulterande meddelande texten ha värdena på fel platser.

Plats hållare hanteras på det här sättet så att du kan göra strukturerad loggning. Application Insights lagrar parameter namn-värde-par och meddelande strängen. Resultatet är att meddelande argumenten blir fält som du kan fråga på.

Om ditt loggnings metod anrop ser ut som i föregående exempel, kan du fråga fältet `customDimensions.prop__rowKey` . `prop__`Prefixet läggs till för att se till att det inte finns några kollisioner mellan fält som runtime lägger till och fält som funktions koden lägger till.

Du kan också fråga på den ursprungliga meddelande strängen genom att referera till fältet `customDimensions.prop__{OriginalFormat}` .  

Här är ett exempel på en JSON-representation av `customDimensions` data:

```json
{
  "customDimensions": {
    "prop__{OriginalFormat}":"C# Queue trigger function processed: {message}",
    "Category":"Function",
    "LogLevel":"Information",
    "prop__message":"c9519cbf-b1e6-4b9b-bf24-cb7d10b1bb89"
  }
}
```

### <a name="custom-metrics-logging"></a>Anpassad mått loggning

I C#-skript funktioner kan du använda `LogMetric` tilläggs metoden för `ILogger` att skapa anpassade mått i Application Insights. Här är ett exempel på metod anrop:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Den här koden är ett alternativ till att anropa `TrackMetric` med hjälp av Application Insights-API för .net.

## <a name="write-logs-in-javascript-functions"></a>Skriva loggar i JavaScript-funktioner

Använd `context.log` för att skriva loggar i Node.js funktioner. Strukturerad loggning är inte aktiverat.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Anpassad mått loggning

När du kör på [version 1. x](functions-versions.md#creating-1x-apps) av Functions-körningen kan Node.js Functions använda `context.log.metric` metoden för att skapa anpassade mått i Application Insights. Den här metoden stöds inte för närvarande i version 2. x och senare. Här är ett exempel på metod anrop:

```javascript
context.log.metric("TestMetric", 1234);
```

Den här koden är ett alternativ till att anropa `trackMetric` med hjälp av Node.js SDK för Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Logga anpassad telemetri i C#-funktioner

Det finns en Functions-speciell version av Application Insights SDK som du kan använda för att skicka anpassade telemetridata från dina funktioner till Application Insights: [Microsoft. Azure. WebJobs. logging. ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights). Använd följande kommando från kommando tolken för att installera det här paketet:

# <a name="command"></a>[Kommando](#tab/cmd)

```cmd
dotnet add package Microsoft.Azure.WebJobs.Logging.ApplicationInsights --version <VERSION>
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <VERSION>
```

---

I det här kommandot ersätter `<VERSION>` du med en version av det här paketet som stöder den installerade versionen av [Microsoft. Azure. WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs/). 

I följande C#-exempel används [API för anpassad telemetri](../azure-monitor/app/api-custom-events-metrics.md). Exemplet är för ett .NET-klass bibliotek, men Application Insights koden är samma för C#-skript.

### <a name="version-2x-and-later"></a>Version 2. x och senare

Version 2. x och senare versioner av körningen använder nyare funktioner i Application Insights för att automatiskt korrelera telemetri med den aktuella åtgärden. Du behöver inte ange Operations `Id` -, `ParentId` -eller- `Name` fälten manuellt.

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

[GetMetric](../azure-monitor/app/api-custom-events-metrics.md#getmetric) är den rekommenderade API: n för att skapa ett mått.

### <a name="version-1x"></a>Version 1. x

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

Anropa inte `TrackRequest` eller `StartOperation<RequestTelemetry>` eftersom du ser dubbla begär Anden för ett funktions anrop.  Functions-körningen spårar automatiskt begär Anden.

Ange inte `telemetryClient.Context.Operation.Id` . Den här globala inställningen orsakar felaktig korrelation när många funktioner körs samtidigt. Skapa i stället en ny telemetri-instans ( `DependencyTelemetry` , `EventTelemetry` ) och ändra dess `Context` egenskap. Skicka sedan telemetri-instansen till motsvarande `Track` metod på `TelemetryClient` ( `TrackDependency()` , `TrackEvent()` , `TrackMetric()` ). Den här metoden säkerställer att Telemetrin har rätt korrelations information för det aktuella funktions anropet.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Logga anpassad telemetri i JavaScript-funktioner

Här är exempel kods tycken som skickar anpassad telemetri med [Application Insights Node.js SDK](https://github.com/microsoft/applicationinsights-node.js):

### <a name="version-2x-and-later"></a>Version 2. x och senare

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride);
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

### <a name="version-1x"></a>Version 1. x

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride);
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

`tagOverrides`Parametern ställer in `operation_Id` till funktionens anrops-ID. Med den här inställningen kan du korrelera alla automatiskt genererade och anpassade telemetri för ett angivet funktions anrop.

## <a name="dependencies"></a>Beroenden

Functions v2 samlar automatiskt in beroenden för HTTP-begäranden, Service Bus, EventHub och SQL.

Du kan skriva anpassad kod för att Visa beroenden. Exempel finns i exempel koden i [avsnittet anpassad telemetri för C#](#log-custom-telemetry-in-c-functions). Exempel koden resulterar i en *program karta* i Application Insights som ser ut som på följande bild:

![Programkarta](./media/functions-monitoring/app-map.png)

## <a name="enable-application-insights-integration"></a>Aktivera Application Insights-integrering

För att en Function-app ska skicka data till Application Insights måste den känna till Instrumentation-nyckeln för en Application Insights resurs. Nyckeln måste vara i en app-inställning med namnet **APPINSIGHTS_INSTRUMENTATIONKEY**.

När du skapar en Function-app [i Azure Portal](functions-create-first-azure-function.md), från kommando raden med hjälp av [Azure Functions Core tools](functions-create-first-azure-function-azure-cli.md)eller genom att använda [Visual Studio Code](functions-create-first-function-vs-code.md), aktive ras Application Insights integration som standard. Application Insights resursen har samma namn som din Function-app, och den skapas antingen i samma region eller i den närmaste regionen.

### <a name="new-function-app-in-the-portal"></a>Ny function-app i portalen

Om du vill granska Application Insights resurs som skapas väljer du den för att expandera fönstret **Application Insights** . Du kan ändra det **nya resurs namnet** eller välja en annan **plats** i ett [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data.

![Aktivera Application Insights när du skapar en Function-app](media/functions-monitoring/enable-ai-new-function-app.png)

När du väljer **skapa**skapas en Application Insights resurs med din Function-app, som har `APPINSIGHTS_INSTRUMENTATIONKEY` inställningen ange i program inställningar. Allt är klart att sätta igång.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Lägg till i en befintlig Function-app 

När du skapar en Function-app med [Visual Studio](functions-create-your-first-function-visual-studio.md)måste du skapa Application Insights resursen. Du kan sedan lägga till Instrumentation-nyckeln från resursen som en [program inställning](functions-how-to-use-azure-function-app-settings.md#settings) i din Function-app.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Tidiga versioner av Functions använde inbyggd övervakning, som inte längre rekommenderas. När du aktiverar Application Insights-integrering för en sådan Function-app måste du också [inaktivera inbyggd loggning](#disable-built-in-logging).  

## <a name="report-issues"></a>Rapportera problem

[Skapa ett ärende i GitHub](https://github.com/Azure/Azure-Functions/issues/new)för att rapportera ett problem med Application Insights-integrering i functions eller för att göra ett förslag eller en begäran.

## <a name="streaming-logs"></a>Strömmande loggar

När du utvecklar ett program vill du ofta se vad som skrivs till loggarna i nära real tid när de körs i Azure.

Det finns två sätt att visa en ström med loggfiler som genereras av dina funktions körningar.

* **Inbyggd logg strömning**: app Services plattformen gör att du kan visa en ström med dina program loggs-filer. Detta motsvarar de utdata som visas när du felsöker dina funktioner under [lokal utveckling](functions-develop-local.md) och när du använder fliken **test** i portalen. All logg-baserad information visas. Mer information finns i [Stream-loggar](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Den här streaming-metoden har endast stöd för en enda instans och kan inte användas med en app som körs på Linux i en förbruknings plan.

* **Live Metrics Stream**: när din Function-app är [ansluten till Application Insights](#enable-application-insights-integration)kan du Visa loggdata och andra mått i nära real tid i Azure Portal med hjälp av [Live Metrics Stream](../azure-monitor/app/live-stream.md). Använd den här metoden när du övervakar funktioner som körs på flera instanser eller i Linux i en förbruknings plan. Den här metoden använder [exempel data](#configure-sampling).

Logg strömmar kan visas både i portalen och i de flesta lokala utvecklings miljöer. 

### <a name="portal"></a>Portalen

Du kan visa båda typerna av logg strömmar i portalen.

#### <a name="built-in-log-streaming"></a>Inbyggd logg strömning

Om du vill visa strömmande loggar i portalen väljer du fliken **plattforms funktioner** i din Function-app. Välj sedan **logg strömning**under **övervakning**.

![Aktivera strömmande loggar i portalen](./media/functions-monitoring/enable-streaming-logs-portal.png)

Detta ansluter din app till logg strömnings tjänsten och program loggar visas i fönstret. Du kan växla mellan **program loggar** och **webb server loggar**.  

![Visa strömmande loggar i portalen](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live-ström med mätvärden

Om du vill visa Live Metrics Stream för din app väljer du fliken **Översikt** i din Function-app. När du har Application Insights aktiverar kan du se en **Application Insights** länk under **konfigurerade funktioner**. Den här länken leder till Application Insights sidan för din app.

I Application Insights väljer du **Live Metrics Stream**. [Exempel logg poster](#configure-sampling) visas under **exempel telemetri**.

![Visa Live Metrics Stream i portalen](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

### <a name="core-tools"></a>Kärn verktyg

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

### <a name="azure-cli"></a>Azure CLI

Du kan aktivera strömmande loggar med hjälp av [Azure CLI](/cli/azure/install-azure-cli). Använd följande kommandon för att logga in, välja din prenumeration och strömma loggfiler:

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

### <a name="azure-powershell"></a>Azure PowerShell

Du kan aktivera strömnings loggar med [Azure PowerShell](/powershell/azure/overview). För PowerShell använder du följande kommandon för att lägga till ditt Azure-konto, väljer din prenumeration och strömma loggfiler:

```powershell
Add-AzAccount
Get-AzSubscription
Get-AzSubscription -SubscriptionName "<subscription name>" | Select-AzSubscription
Get-AzWebSiteLog -Name <FUNCTION_APP_NAME> -Tail
```

## <a name="scale-controller-logs-preview"></a>Skalnings styrenhets loggar (för hands version)

Den här funktionen är en förhandsversion. 

Den [Azure Functions skalnings styrenheten](./functions-scale.md#runtime-scaling) övervakar instanser av Azure Functions värden som appen körs på. Den här kontrollanten fattar beslut om när du ska lägga till eller ta bort instanser utifrån aktuella prestanda. Du kan låta skalnings styrenheten generera loggar till antingen Application Insights eller till Blob Storage för att bättre förstå de beslut som skalnings styrenheten gör för din Function-app.

Om du vill aktivera den här funktionen lägger du till en ny program inställning med namnet `SCALE_CONTROLLER_LOGGING_ENABLED` . Värdet för den här inställningen måste ha formatet `<DESTINATION>:<VERBOSITY>` , baserat på följande:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Följande Azure CLI-kommando aktiverar till exempel utförlig loggning från skalnings styrenheten till Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

I det här exemplet ersätter `<FUNCTION_APP_NAME>` och `<RESOURCE_GROUP_NAME>` med namnet på din Function-app och resurs gruppens namn. 

Följande Azure CLI-kommando inaktiverar loggning genom att ange utförlighet till `None` :

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

Du kan också inaktivera loggning genom att ta bort `SCALE_CONTROLLER_LOGGING_ENABLED` inställningen med följande Azure CLI-kommando:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

## <a name="disable-built-in-logging"></a>Inaktivera inbyggd loggning

När du aktiverar Application Insights inaktiverar du den inbyggda loggning som använder Azure Storage. Den inbyggda loggningen är användbar för testning med lätta arbets belastningar, men är inte avsedd för användning med hög belastnings produktion. För produktions övervakning rekommenderar vi Application Insights. Om den inbyggda loggningen används i produktionen kan loggnings posten vara ofullständig på grund av begränsning på Azure Storage.

Om du vill inaktivera den inbyggda loggningen tar du bort `AzureWebJobsDashboard` appens inställning. Information om hur du tar bort inställningar för appar i Azure Portal finns i avsnittet **program inställningar** i [hantera en Function-app](functions-how-to-use-azure-function-app-settings.md#settings). Innan du tar bort appens inställning ska du se till att inga befintliga funktioner i samma Function-app använder inställningen för Azure Storage utlösare eller bindningar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core loggning](/aspnet/core/fundamentals/logging/)

[host.jspå]: functions-host-json.md

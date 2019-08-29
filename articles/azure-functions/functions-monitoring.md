---
title: Övervaka Azure Functions
description: Lär dig hur du använder Azure Application insikter med Azure Functions för att övervaka funktions körning.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: glenga
ms.openlocfilehash: 7220c72625697490d9c42dab7e18ca4bf64d3f43
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085610"
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

[Azure Functions](functions-overview.md) erbjuder inbyggd integrering med [Azure Application](../azure-monitor/app/app-insights-overview.md) insikter för att övervaka funktioner. Den här artikeln visar hur du konfigurerar Azure Functions att skicka systemgenererade loggfiler till Application Insights.

Vi rekommenderar att du använder Application Insights eftersom den samlar in logg-, prestanda-och fel data. Den identifierar automatiskt prestanda avvikelser och innehåller kraftfulla analys verktyg som hjälper dig att diagnostisera problem och förstå hur dina funktioner används. Den är avsedd för utvecklare och för att hjälpa dig att kontinuerligt förbättra prestanda och användbarhet. Du kan även använda Application Insights under utveckling av program projekt i lokal funktion. Mer information finns i [Vad är Application Insights?](../azure-monitor/app/app-insights-overview.md).

Eftersom den nödvändiga Application Insights Instrumentation är inbyggd i Azure Functions, är allt du behöver en giltig Instrumentation-nyckel för att ansluta din app till en Application Insights resurs.

## <a name="application-insights-pricing-and-limits"></a>Application Insights priser och begränsningar

Du kan prova Application Insights integration med Function-appar kostnads fritt. Det finns en daglig gräns för hur mycket data som kan bearbetas kostnads fritt. Du kan trycka på den här gränsen under testningen. Azure tillhandahåller Portal-och e-postaviseringar när du närmar dig den dagliga gränsen. Om du saknar dessa aviseringar och nått gränsen visas inte nya loggar i Application Insights frågor. Var medveten om gränsen för att undvika onödig fel söknings tid. Mer information finns [i hantera priser och data volymer i Application Insights](../azure-monitor/app/pricing.md).

## <a name="enable-application-insights-integration"></a>Aktivera Application Insights-integrering

För att en Function-app ska skicka data till Application Insights måste den känna till Instrumentation-nyckeln för en Application Insights resurs. Nyckeln måste vara i en app-inställning med namnet **APPINSIGHTS_INSTRUMENTATIONKEY**.

### <a name="new-function-app-in-the-portal"></a>Ny function-app i portalen

När du [skapar en Function-app i Azure Portal](functions-create-first-azure-function.md)aktive ras Application Insights integration som standard. Application Insights resursen har samma namn som din Function-app, och den skapas antingen i samma region eller i närmaste region.

Om du vill granska Application Insights resurs som skapas väljer du den för att expandera fönstret **Application Insights** . Du kan ändra det **nya resurs namnet** eller välja en annan **plats** i ett [Azure-geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill lagra dina data.

![Aktivera Application Insights när du skapar en Function-app](media/functions-monitoring/enable-ai-new-function-app.png)

När du väljer **skapa**skapas en Application Insights resurs med din Function-app, som har `APPINSIGHTS_INSTRUMENTATIONKEY` inställningen ange i program inställningar. Allt är klart att sätta igång.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Lägg till i en befintlig Function-app 

När du skapar en Function-app med hjälp av [Azure CLI](functions-create-first-azure-function-azure-cli.md), [Visual Studio](functions-create-your-first-function-visual-studio.md)eller [Visual Studio Code](functions-create-first-function-vs-code.md)måste du skapa Application Insights resursen. Du kan sedan lägga till Instrumentation-nyckeln från resursen som en program inställning i din Function-app.

[!INCLUDE [functions-connect-new-app-insights.md](../../includes/functions-connect-new-app-insights.md)]

Tidiga versioner av Functions använde inbyggd övervakning, som inte längre rekommenderas. När du aktiverar Application Insights-integrering för en sådan Function-app måste du också [inaktivera inbyggd loggning](#disable-built-in-logging).  

## <a name="view-telemetry-in-monitor-tab"></a>Visa telemetri på fliken övervaka

Med [Application Insights integrering aktive rad](#enable-application-insights-integration)kan du Visa telemetridata på fliken **övervaka** .

1. På sidan Function-app väljer du en funktion som har körts minst en gång efter Application Insights har kon figurer ATS. Välj sedan fliken **övervaka** .

   ![Välj fliken övervakare](media/functions-monitoring/monitor-tab.png)

1. Välj **Uppdatera** regelbundet, tills listan över funktions anrop visas.

   Det kan ta upp till fem minuter innan listan visas medan telemetri-klienten registrerar data för överföring till servern. (Fördröjningen gäller inte för [Live Metrics Stream](../azure-monitor/app/live-stream.md). Tjänsten ansluter till Functions-värden när du läser in sidan, så loggarna strömmas direkt till sidan.)

   ![Lista med anrop](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Om du vill se loggarna för ett visst funktions anrop väljer du länken **datum** kolumn för det anropet.

   ![Länk till information om anrop](media/functions-monitoring/invocation-details-link-ai.png)

   Loggnings resultatet för det anropet visas på en ny sida.

   ![Information om anrop](media/functions-monitoring/invocation-details-ai.png)

Du kan se att båda sidorna har en **körning i Application Insights** länk till den Application Insights Analytics-fråga som hämtar data.

![Kör i Application Insights](media/functions-monitoring/run-in-ai.png)

Följande fråga visas. Du kan se att anrops listan är begränsad till de senaste 30 dagarna. Listan innehåller högst 20 rader (`where timestamp > ago(30d) | take 20`). Listan med information om anrop är under de senaste 30 dagarna utan begränsning.

![Lista med Application Insights analys anrop](media/functions-monitoring/ai-analytics-invocation-list.png)

Mer information finns i [query telemetri-data](#query-telemetry-data) senare i den här artikeln.

## <a name="view-telemetry-in-application-insights"></a>Visa telemetri i Application Insights

Öppna Application Insights från en Function-app i Azure Portal genom att gå till sidan med funktions appens **Översikt** . Under **konfigurerade funktioner**väljer du **Application Insights**.

![Öppna Application Insights från översikts sidan för Function-appen](media/functions-monitoring/ai-link.png)

Information om hur du använder Application Insights finns i Application Insights- [dokumentationen](https://docs.microsoft.com/azure/application-insights/). I det här avsnittet visas några exempel på hur du kan visa data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [avsnitten om hur du konfigurerar och anpassar telemetridata](#configure-categories-and-log-levels).

![Fliken Application Insights översikt](media/functions-monitoring/metrics-explorer.png)

Följande områden i Application Insights kan vara användbara när du ska utvärdera beteende, prestanda och fel i dina funktioner:

| Tab | Beskrivning |
| ---- | ----------- |
| **[Fel](../azure-monitor/app/asp-net-exceptions.md)** |  Skapa diagram och aviseringar baserat på funktions fel och Server undantag. **Åtgärds namnet** är funktions namnet. Felen i beroenden visas inte om du inte implementerar anpassad telemetri för beroenden. |
| **[Historik](../azure-monitor/app/performance-counters.md)** | Analysera prestanda problem. |
| **Servrar** | Visa resursutnyttjande och data flöde per server. Dessa data kan vara användbara för fel sökning av scenarier där funktioner är bogging de underliggande resurserna. Servrar kallas för **moln roll instanser**. |
| **[Mått](../azure-monitor/app/metrics-explorer.md)** | Skapa diagram och aviseringar som baseras på mått. Mått inkluderar antalet funktions anrop, körnings tid och lyckade kostnader. |
| **[Live-ström med mätvärden](../azure-monitor/app/live-stream.md)** | Visa mått data när de skapas i real tid. |

## <a name="query-telemetry-data"></a>Fråga telemetri-data

[Application Insights Analytics](../azure-monitor/app/analytics.md) ger dig åtkomst till alla telemetridata i form av tabeller i en databas. Analytics innehåller ett frågespråk för extrahering, manipulering och visualisering av data.

![Välj analys](media/functions-monitoring/select-analytics.png)

![Analys exempel](media/functions-monitoring/analytics-traces.png)

Här är ett exempel på en fråga som visar distributionen av förfrågningar per arbetare under de senaste 30 minuterna.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

De tabeller som är tillgängliga visas på fliken **schema** till vänster. Du kan hitta data som genererats av funktions anrop i följande tabeller:

| Tabell | Beskrivning |
| ----- | ----------- |
| **Anden** | Loggar som skapats av körningen och med funktions kod. |
| **autentiseringsbegäran** | En begäran för varje funktions anrop. |
| **undantag** | Eventuella undantag som har utlösts av körningen. |
| **customMetrics** | Antalet lyckade och misslyckade anrop, lyckade kostnader och varaktighet. |
| **customEvents** | Händelser som spåras av körningen, till exempel: HTTP-begäranden som utlöser en funktion. |
| **performanceCounters** | Information om prestanda för de servrar som funktionerna körs på. |

De andra tabellerna är för tillgänglighets test och telemetri för klienter och webbläsare. Du kan implementera anpassad telemetri för att lägga till data i dem.

I varje tabell finns vissa av de funktioner som är aktuella i ett `customDimensions` fält.  Följande fråga hämtar till exempel alla spår som har loggnings nivå `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Körningen innehåller `customDimensions.LogLevel` fälten och `customDimensions.Category` . Du kan ange ytterligare fält i loggar som du skriver i funktions koden. Se [strukturerad loggning](#structured-logging) senare i den här artikeln.

## <a name="configure-categories-and-log-levels"></a>Konfigurera kategorier och logg nivåer

Du kan använda Application Insights utan någon anpassad konfiguration. Standard konfigurationen kan resultera i stora data mängder. Om du använder en Azure-prenumeration från Visual Studio kan du träffa din data Kap för Application Insights. Senare i den här artikeln får du lära dig hur du konfigurerar och anpassar de data som dina funktioner skickar till Application Insights. För en Function-app konfigureras loggning i [Host. JSON] -filen.

### <a name="categories"></a>Categories

Azure Functions loggen innehåller en *kategori* för varje logg. Kategorin visar vilken del av körnings koden eller din funktions kod som skrev loggen. 

Funktions körningen skapar loggar med en kategori som börjar med "värd". I version `function started`1. x har- `function executed`,-och `function completed` -loggarna kategorin `Host.Executor`. Från och med version 2. x har dessa loggar kategorin `Function.<YOUR_FUNCTION_NAME>`.

Om du skriver loggar i funktions koden är `Function` kategorin i version 1. x i functions-körningen. I version 2. x är `Function.<YOUR_FUNCTION_NAME>.User`kategorin.

### <a name="log-levels"></a>Logg nivåer

Azure Functions loggen innehåller också en *logg nivå* med varje logg. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) är en uppräkning och heltals koden indikerar relativ prioritet:

|LogLevel    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsöka       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritiskt    | 5 |
|Inga        | 6 |

Logg nivån `None` förklaras i nästa avsnitt. 

### <a name="log-configuration-in-hostjson"></a>Logg konfiguration i Host. JSON

[Host. JSON] -filen konfigurerar hur mycket loggning av en Function-app som skickar till Application Insights. För varje kategori anger du den lägsta logg nivå som ska skickas. Det finns två exempel: det första exemplet är riktat till [funktionerna version 2. x runtime](functions-versions.md#version-2x) (.net Core) och det andra exemplet är för version 1. x-körningsmiljön.

### <a name="version-2x"></a>Version 2.x

V2. x-körningsmiljön använder [.net Core Logging filter](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering)-hierarkin. 

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

* För loggar med kategori `Host.Results` eller `Function`, skicka endast `Error` nivån och över till Application Insights. Loggar för `Warning` nivån och nedan ignoreras.
* Skicka alla loggar till `Host.Aggregator`Application Insights för loggar med kategori. Logg nivån är samma som för alla loggar som anropar `Verbose`, men används `Trace` i [Host. JSON] -filen. `Trace`
* För alla andra loggar skickar du bara `Information` nivån och över den till Application Insights.

Kategori värdet i [Host. JSON] kontrollerar loggningen för alla kategorier som börjar med samma värde. `Host`i [Host. JSON] kontrollerar loggningen `Host.General`för `Host.Executor`, `Host.Results`, och så vidare.

Om [Host. JSON] innehåller flera kategorier som börjar med samma sträng matchas de längre först. Anta att du vill ha allt `Host.Aggregator` från körnings miljön förutom `Error` att logga på nivå, `Host.Aggregator` men du `Information` vill logga på nivån:

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

Om du vill ignorera alla loggar för en kategori kan du använda loggnings nivå `None`. Inga loggar skrivs med den kategorin och det finns ingen loggnings nivå ovanför den.

I följande avsnitt beskrivs de huvudsakliga kategorierna med loggar som körs. 

### <a name="category-hostresults"></a>Kategori värd. resultat

Loggarna visas som "begär Anden" i Application Insights. De indikerar en funktion som lyckats eller misslyckats.

![Diagram över begär Anden](media/functions-monitoring/requests-chart.png)

Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar `Warning` på eller ovanför visas inte någon av dessa data.

### <a name="category-hostaggregator"></a>Kategori Host. Aggregator

Dessa loggar ger räknare och medelvärden för funktions anrop under en [konfigurerbar](#configure-the-aggregator) tids period. Standard perioden är 30 sekunder eller 1 000 resultat, beroende på vilket som kommer först. 

Loggarna är tillgängliga i tabellen **customMetrics** i Application Insights. Exempel är antalet körningar, lyckade kostnader och varaktighet.

![customMetrics-fråga](media/functions-monitoring/custom-metrics-query.png)

Alla dessa loggar skrivs på `Information` nivå. Om du filtrerar `Warning` på eller ovanför visas inte någon av dessa data.

### <a name="other-categories"></a>Andra kategorier

Alla loggar för andra kategorier än de som redan finns i listan är tillgängliga i tabellen **spårning** i Application Insights.

![spåra fråga](media/functions-monitoring/analytics-traces.png)

Alla loggar med kategorier som börjar med `Host` skrivs av Functions-körningen. Loggarna "Function Started" och "Function Complete" har `Host.Executor`en kategori. För lyckade körningar är `Information` dessa loggar nivå. Undantagen har loggats på `Error` nivå. Körningen skapar `Warning` också nivå loggar, till exempel: köa meddelanden som skickas till en Poison-kö.

Loggar som skrivits av funktions koden har en `Function` kategori och kan vara vilken logg nivå som helst.

## <a name="configure-the-aggregator"></a>Konfigurera Aggregator

Enligt vad som anges i föregående avsnitt sammanställer körnings miljön data om funktions körningar under en viss tids period. Standard perioden är 30 sekunder eller 1 000 körningar, beroende på vilket som kommer först. Du kan konfigurera den här inställningen i [Host. JSON] -filen.  Här är ett exempel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurera sampling

Application Insights har en [samplings](../azure-monitor/app/sampling.md) funktion som kan skydda dig från att skapa för mycket telemetri-data vid slutförda körningar vid tider med hög belastning. När antalet inkommande körningar överskrider ett angivet tröskelvärde börjar Application Insights att ignorera några av de inkommande körningarna slumpmässigt. Standardvärdet för maximalt antal körningar per sekund är 20 (fem i version 1. x). Du kan konfigurera sampling i [Host. JSON].  Här är ett exempel:

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
> [Sampling](../azure-monitor/app/sampling.md) är aktiverat som standard. Om du verkar saknas data kan du behöva justera samplings inställningarna så att de passar just ditt övervaknings scenario.

## <a name="write-logs-in-c-functions"></a>Skriv loggar i C# Functions

Du kan skriva loggar i funktions koden som visas som spår i Application Insights.

### <a name="ilogger"></a>ILogger

Använd en [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) -parameter i dina funktioner i stället `TraceWriter` för en parameter. Loggar som skapats med `TraceWriter` hjälp av gå till Application Insights `ILogger` , men du kan göra [strukturerad loggning](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Med ett `ILogger` objekt anropar `Log<level>` du [tilläggs metoder på ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) för att skapa loggar. Följande kod skriver `Information` loggar med kategorin "funktion".

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

Om ditt loggnings metod anrop ser ut som i föregående exempel, kan du fråga `customDimensions.prop__rowKey`fältet. `prop__` Prefixet läggs till för att se till att det inte finns några kollisioner mellan fält som runtime lägger till och fält som funktions koden lägger till.

Du kan också fråga på den ursprungliga meddelande strängen genom att referera till fältet `customDimensions.prop__{OriginalFormat}`.  

Här är ett exempel på en JSON `customDimensions` -representation av data:

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

### <a name="custom-metrics-logging"></a>Anpassad mått loggning

I C# skript funktioner kan du använda `LogMetric` tilläggs metoden för `ILogger` att skapa anpassade mått i Application Insights. Här är ett exempel på metod anrop:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Den här koden är ett alternativ till `TrackMetric` att anropa med hjälp av Application Insights-API för .net.

## <a name="write-logs-in-javascript-functions"></a>Skriva loggar i JavaScript-funktioner

Använd `context.log` för att skriva loggar i Node. js-funktioner. Strukturerad loggning är inte aktiverat.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="custom-metrics-logging"></a>Anpassad mått loggning

När du kör på [version 1. x](functions-versions.md#creating-1x-apps) av Functions-körningen kan Node. js-funktioner använda `context.log.metric` metoden för att skapa anpassade mått i Application Insights. Den här metoden stöds inte för närvarande i version 2. x. Här är ett exempel på metod anrop:

```javascript
context.log.metric("TestMetric", 1234);
```

Den här koden är ett alternativ till `trackMetric` att anropa med hjälp av Node. js SDK för Application Insights.

## <a name="log-custom-telemetry-in-c-functions"></a>Logga anpassad telemetri i C# funktioner

Du kan använda [Microsoft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) NuGet-paketet för att skicka anpassade telemetridata till Application Insights. I följande C# exempel används [API för anpassad telemetri](../azure-monitor/app/api-custom-events-metrics.md). Exemplet är för ett .NET-klass bibliotek, men Application Insights koden är samma för C# skript.

### <a name="version-2x"></a>Version 2.x

Version 2. x-körningsmiljön använder nyare funktioner i Application Insights för att automatiskt korrelera telemetri med den aktuella åtgärden. Du behöver inte ange Operations `Id`- `ParentId`,-eller `Name` -fälten manuellt.

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

Anropa `TrackRequest` inte eller `StartOperation<RequestTelemetry>` eftersom du ser dubbla begär Anden för ett funktions anrop.  Functions-körningen spårar automatiskt begär Anden.

Ange `telemetryClient.Context.Operation.Id`inte. Den här globala inställningen orsakar felaktig korrelation när många funktioner körs samtidigt. Skapa i stället en ny telemetri-instans`DependencyTelemetry`( `EventTelemetry`,) och ändra `Context` dess egenskap. Skicka sedan telemetri-instansen till motsvarande `Track` metod på `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Den här metoden säkerställer att Telemetrin har rätt korrelations information för det aktuella funktions anropet.

## <a name="log-custom-telemetry-in-javascript-functions"></a>Logga anpassad telemetri i JavaScript-funktioner

Här är ett exempel kods tycke som skickar anpassad telemetri med [Application Insights Node. js SDK](https://github.com/microsoft/applicationinsights-node.js):

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

`tagOverrides` Parametern ställerintillfunktionens`operation_Id` anrops-ID. Med den här inställningen kan du korrelera alla automatiskt genererade och anpassade telemetri för ett angivet funktions anrop.

## <a name="dependencies"></a>Beroenden

Functions v2 samlar automatiskt in beroenden för HTTP-begäranden, Service Bus och SQL.

Du kan skriva anpassad kod för att Visa beroenden. Exempel finns i exempel koden i [ C# avsnittet anpassad telemetri](#log-custom-telemetry-in-c-functions). Exempel koden resulterar i en *program karta* i Application Insights som ser ut som på följande bild:

![Programkarta](./media/functions-monitoring/app-map.png)

## <a name="report-issues"></a>Rapportera problem

[Skapa ett ärende i GitHub](https://github.com/Azure/Azure-Functions/issues/new)för att rapportera ett problem med Application Insights-integrering i functions eller för att göra ett förslag eller en begäran.

## <a name="streaming-logs"></a>Direktuppspelningsloggar

När du utvecklar ett program vill du ofta använda vad som skrivs till loggarna i nära real tid när de körs i Azure.

Det finns två sätt att visa en ström med loggfiler som genereras av dina funktions körningar.

* **Inbyggd logg strömning**: app Services plattformen gör att du kan visa en ström med dina program loggs-filer. Detta motsvarar de utdata som visas när du felsöker dina funktioner under [lokal utveckling](functions-develop-local.md) och när du använder fliken **test** i portalen. All logg-baserad information visas. Mer information finns i [så här strömmar du loggar](../app-service/troubleshoot-diagnostic-logs.md#streamlogs). Den här streaming-metoden har endast stöd för en enda instans och kan inte användas med en app som körs på Linux i en förbruknings plan.

* **Live Metrics Stream**: när din Function-app är [ansluten till Application Insights](#enable-application-insights-integration)kan du Visa loggdata och andra mått i nära real tid i Azure Portal med hjälp av [Live Metrics Stream](../azure-monitor/app/live-stream.md). Använd den här metoden när du övervakar funktioner som körs på flera instanser eller i Linux i en förbruknings plan. Den här metoden använder [exempel data](#configure-sampling).

Logg strömmar kan visas både i portalen och i de flesta lokala utvecklings miljöer. 

### <a name="portal"></a>Portalen

Du kan visa båda typerna av logg strömmar i portalen.

#### <a name="built-in-log-streaming"></a>Inbyggd logg strömning

Om du vill visa strömmande loggar i portalen väljer du fliken **plattforms funktioner** i din Function-app. Välj sedan **logg strömning**under **övervakning**.

![Aktivera strömmande loggar i portalen](./media/functions-monitoring/enable-streaming-logs-portal.png)

Detta ansluter din app till logg strömnings tjänsten och program loggar visas i fönstret. Du kan växla mellan **program loggar** och **webb server loggar**.  

![Visa strömmande loggar i portalen](./media/functions-monitoring/streaming-logs-window.png)

#### <a name="live-metrics-stream"></a>Live Metrics Stream

Om du vill visa Live Metrics Stream för din app väljer du fliken **Översikt** i din Function-app. När du har Application Insights aktiverar kan du se en **Application Insights** länk under **konfigurerade funktioner**. Den här länken leder till Application Insights sidan för din app.

I Application Insights väljer du **Live Metrics Stream**. [Exempel logg poster](#configure-sampling) visas under **exempel telemetri**.

![Visa Live Metrics Stream i portalen](./media/functions-monitoring/live-metrics-stream.png) 

### <a name="visual-studio-code"></a>Visual Studio-koden

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

## <a name="disable-built-in-logging"></a>Inaktivera inbyggd loggning

När du aktiverar Application Insights inaktiverar du den inbyggda loggning som använder Azure Storage. Den inbyggda loggningen är användbar för testning med lätta arbets belastningar, men är inte avsedd för användning med hög belastnings produktion. För produktions övervakning rekommenderar vi Application Insights. Om den inbyggda loggningen används i produktionen kan loggnings posten vara ofullständig på grund av begränsning på Azure Storage.

Om du vill inaktivera den `AzureWebJobsDashboard` inbyggda loggningen tar du bort appens inställning. Information om hur du tar bort inställningar för appar i Azure Portal finns i avsnittet **program inställningar** i [hantera en Function-app](functions-how-to-use-azure-function-app-settings.md#settings). Innan du tar bort appens inställning ska du se till att inga befintliga funktioner i samma Function-app använder inställningen för Azure Storage utlösare eller bindningar.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core loggning](/aspnet/core/fundamentals/logging/)

[Host. JSON]: functions-host-json.md

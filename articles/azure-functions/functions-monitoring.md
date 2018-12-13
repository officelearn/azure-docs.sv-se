---
title: Övervaka Azure Functions
description: Lär dig hur du använder Azure Application Insights med Azure Functions för att övervaka körning av funktion.
services: functions
author: ggailey777
manager: jeconnoc
keywords: azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: glenga
ms.openlocfilehash: aba3d9f33d179c09708464975fa2a929a8bb68d0
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876527"
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

[Azure Functions](functions-overview.md) erbjuder inbyggd integrering med [Azure Application Insights](../application-insights/app-insights-overview.md) för övervakningsfunktionerna. Den här artikeln visar hur du konfigurerar funktioner för att skicka systemgenererade loggfiler till Application Insights.

![Application Insights Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Functions har också [inbyggd övervakning som inte använder Application Insights](#monitoring-without-application-insights). Vi rekommenderar Application Insights eftersom det ger mer data och bättre sätt att analysera data.

## <a name="application-insights-pricing-and-limits"></a>Priser för Application Insights och begränsningar

Du kan testa Application Insights-integrering med Funktionsappar utan kostnad. Men det finns en daglig gräns för hur mycket data kan bearbetas kostnadsfritt och du kan når gränsen under testningen. Azure tillhandahåller portalen och e-postaviseringar när du närmar sig dagliga gränsen.  Men om du missar aviseringarna och nått gränsen för nya loggar visas inte i Application Insights-frågor. Så var medveten om gränsen för att undvika onödiga tidsåtgången för felsökning. Mer information finns i [hantera priser och datavolymer i Application Insights](../application-insights/app-insights-pricing.md).

## <a name="enable-app-insights-integration"></a>Aktivera App Insights-integrering

Den måste ange instrumenteringsnyckeln för en Application Insights-resurs för en funktionsapp kan skicka data till Application Insights. Nyckeln måste vara i en app som inställning med namnet **APPINSIGHTS_INSTRUMENTATIONKEY**.

Du kan ställa in den här anslutningen i den [Azure-portalen](https://portal.azure.com):

* [Automatiskt för en ny funktionsapp](#new-function-app)
* [Ansluta en App Insights-resurs manuellt](#manually-connect-an-app-insights-resource)

### <a name="new-function-app"></a>Ny funktionsapp

1. Gå till funktionsappen **skapa** sidan.

1. Ange den **Programinsikter** växla **på**.

1. Välj en **Application Insights-plats**. Välj den region som ligger närmast din funktionsapp region i en [Azure geografi](https://azure.microsoft.com/global-infrastructure/geographies/) där du vill att data ska lagras.

   ![Aktivera Application Insights när du skapar en funktionsapp](media/functions-monitoring/enable-ai-new-function-app.png)

1. Ange den begärda informationen och välj **skapa**.

Nästa steg är att [inaktivera inbyggd loggning](#disable-built-in-logging).

### <a name="manually-connect-an-app-insights-resource"></a>Ansluta en App Insights-resurs manuellt 

1. Skapa Application Insights-resursen. Ange programtypen **Allmänt**.

   ![Skapa en Application Insights-resurs skriver Allmänt](media/functions-monitoring/ai-general.png)

1. Kopiera instrumenteringsnyckeln från den **Essentials** för Application Insights-resursen. Hovra över slut visas nyckelvärdet för att få en **Klicka om du vill kopiera** knappen.

   ![Kopiera instrumenteringsnyckeln Application Insights](media/functions-monitoring/copy-ai-key.png)

1. I funktionsappen **programinställningar** sidan [lägga till en appinställning](functions-how-to-use-azure-function-app-settings.md#settings) genom att klicka på **Lägg till ny inställning**. Namnge den nya inställningen APPINSIGHTS_INSTRUMENTATIONKEY och klistra in den kopierade instrumenteringsnyckeln.

   ![Lägg till instrumentationsnyckel appinställningar](media/functions-monitoring/add-ai-key.png)

1. Klicka på **Spara**.

## <a name="disable-built-in-logging"></a>Inaktivera inbyggd loggning

När du aktiverar Application Insights kan du inaktivera den [inbyggd loggning som använder Azure storage](#logging-to-storage). Inbyggd loggning är användbart för att testa med lätta arbetsbelastningar, men är inte avsedd för användning i produktion för hög belastning. För produktion övervakning, rekommenderas Programinsikter. Om du använder inbyggd loggning i produktion, kan posten loggning vara ofullständiga på grund av begränsningar i Azure Storage.

Om du vill inaktivera inbyggd loggning, ta bort den `AzureWebJobsDashboard` appinställningen. Information om hur du tar bort app-inställningar i Azure-portalen finns i den **programinställningar** delen av [hantera en funktionsapp](functions-how-to-use-azure-function-app-settings.md#settings). Innan du tar bort appinställningen, se till att inga befintliga funktioner i samma funktionsapp använder den för Azure Storage-utlösare och bindningar.

## <a name="view-telemetry-in-monitor-tab"></a>Visa telemetri i fliken övervakning

När du har konfigurerat Application Insights-integrering som visas i föregående avsnitt, kan du visa dessa data i den **övervakaren** fliken.

1. På sidan för funktionsappar, väljer du en funktion som har kört minst en gång när Application Insights har konfigurerade och välj sedan den **övervakaren** fliken.

   ![Välj fliken övervakning](media/functions-monitoring/monitor-tab.png)

1. Välj **uppdatera** regelbundet tills listan funktionsanrop visas.

   Det kan ta upp till 5 minuter för en lista visas, beroende på sättet telemetridata för klienten som batchar för överföring till servern. (Den här fördröjningen gäller inte för den [Live Metrics Stream](../application-insights/app-insights-live-stream.md). Tjänsten ansluter till Functions värden när du läser in sidan, så att loggarna strömmas direkt till sidan.)

   ![Anrop listan](media/functions-monitoring/monitor-tab-ai-invocations.png)

1. Om du vill se loggar för ett visst funktionsanrop i **datum** kolumnlänken för det anropet.

   ![Länka anropsinformation](media/functions-monitoring/invocation-details-link-ai.png)

   Loggning utdata för det anropet visas i en ny sida.

   ![Anropsinformation](media/functions-monitoring/invocation-details-ai.png)

Båda sidor (anrop listan och information) länka till Application Insights Analytics-frågan som hämtar data:

![Kör i Application Insights](media/functions-monitoring/run-in-ai.png)

![Application Insights Analytics anrop listan](media/functions-monitoring/ai-analytics-invocation-list.png)

Från dessa frågor kan du se att listan anrop är begränsad till de senaste 30 dagarna har fler än 20 rader (`where timestamp > ago(30d) | take 20`) och anrop information om listan är för de senaste 30 dagarna utan gräns.

Mer information finns i [fråga telemetridata](#query-telemetry-data) senare i den här artikeln.

## <a name="view-telemetry-in-app-insights"></a>Visa telemetri i App Insights

Öppna Application Insights från en funktionsapp i Azure portal, Välj den **Application Insights** länken i den **konfigurerats funktioner** avsnitt på funktionsappen **översikt** sidan.

![Application Insights länk-översikt](media/functions-monitoring/ai-link.png)

Information om hur du använder Application Insights finns i den [dokumentation om Application Insights](https://docs.microsoft.com/azure/application-insights/). Det här avsnittet visas några exempel på hur du visar data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [avsnitt om att konfigurera och anpassa telemetridata](#configure-categories-and-log-levels).

I [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md), du kan skapa diagram och aviseringar baserat på mått, till exempel som antalet funktionsanrop körningstid och Slutförandefrekvens.

![Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

På den [fel](../application-insights/app-insights-asp-net-exceptions.md) fliken kan du skapa diagram och aviseringar som baseras på funktionen fel och server undantag. Den **Åtgärdsnamnet** är funktionsnamnet. Fel i beroenden visas inte om inte du implementera [anpassad telemetri](#custom-telemetry-in-c-functions) för beroenden.

![Fel](media/functions-monitoring/failures.png)

På den [prestanda](../application-insights/app-insights-performance-counters.md) fliken du kan analysera problem med prestanda.

![Prestanda](media/functions-monitoring/performance.png)

Den **servrar** fliken visar Resursanvändning och dataflöde per server. Dessa data kan vara användbart för felsökning av scenarier där funktioner bogging ned dina underliggande resurser. Servrar kallas **Cloud rollinstanser**.

![Servrar](media/functions-monitoring/servers.png)

Den [Live Metrics Stream](../application-insights/app-insights-live-stream.md) fliken visar måttdata som den har skapats i realtid.

![Direktsänd ström](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Telemetridata för fråga

[Application Insights Analytics](../application-insights/app-insights-analytics.md) ger dig tillgång till alla dessa data i form av tabeller i en databas. Analytics tillhandahåller ett frågespråk för extrahering, manipulera och visualisera dina data.

![Välj Analytics](media/functions-monitoring/select-analytics.png)

![Analytics-exempel](media/functions-monitoring/analytics-traces.png)

Här är ett exempel på sökfråga som visar fördelningen av begäranden per worker under de senaste 30 minuterna.

```
requests
| where timestamp > ago(30m) 
| summarize count() by cloud_RoleInstance, bin(timestamp, 1m)
| render timechart
```

Tabeller som är tillgängliga som visas i den **schemat** fliken i det vänstra fönstret. Du kan hitta data som genereras av funktionsanrop i följande tabeller:

* **spårningar** -loggar som skapats av körningen, av funktionskoden.
* **begäranden** – en för varje funktionsanrop.
* **undantag** – alla undantag från körningen.
* **customMetrics** -antal lyckade och misslyckade anrop, Slutförandefrekvens, varaktighet.
* **customEvents** -händelser spåras av körning, till exempel: HTTP-begäranden som utlöser en funktion.
* **performanceCounters** -information om prestanda för de servrar som funktionerna som körs på.

De andra tabellerna är för tillgänglighetstester och klientwebbläsaren/telemetri. Du kan implementera anpassade telemetri för att lägga till data i dem.

Inom varje tabell är några av specifika funktioner i en `customDimensions` fält.  Till exempel följande fråga hämtar alla spårningar som har Loggnivå `Error`.

```
traces 
| where customDimensions.LogLevel == "Error"
```

Körningen ger `customDimensions.LogLevel` och `customDimensions.Category`. Du kan ange ytterligare fält i loggarna som du skriver i funktionskoden. Se [strukturerad loggning](#structured-logging) senare i den här artikeln.

## <a name="configure-categories-and-log-levels"></a>Konfigurera kategorier och logga nivåer

Du kan använda Application Insights utan anpassad konfiguration, men standardkonfigurationen kan leda till stora mängder data. Om du använder ett Visual Studio-prenumerationen kan du uppnått datakapaciteten för Application Insights. Resten av den här artikeln visar hur du konfigurerar och anpassar de data som dina funktioner skicka till Application Insights.

### <a name="categories"></a>Kategorier

Azure Functions-loggaren innehåller en *kategori* för varje logg. Kategorin som anger vilken del av runtime-kod eller Funktionskoden skrev loggen. 

Functions-körning skapar loggar som har en kategori som börjar med ”värd”. Till exempel ”funktionen igång”, ”funktionen körs” och ”slutfört” funktionsloggar har kategorin ”Host.Executor”. 

Om du sparar loggar på Funktionskoden är deras kategori ”funktionen”.

### <a name="log-levels"></a>Loggningsnivåerna

Azure functions-loggaren innehåller också en *certifikatutfärdarnivå* med varje logg. [LogLevel](/dotnet/api/microsoft.extensions.logging.loglevel) är en uppräkning och heltal koden visar relativa prioriteten:

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

### <a name="configure-logging-in-hostjson"></a>Konfigurera loggning i host.json

Den *[host.json](functions-host-json.md)* filen konfigurerar hur mycket loggning som en funktionsapp skickar till Application Insights. För varje kategori, kan du ange den lägsta loggningsnivån för att skicka. Det finns två exempel, något som riktar sig mot den [funktionskörningen version 2.x](functions-versions.md#version-2x) (.NET Core) och en för version 1.x-körningen.

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

1. För loggar med kategori `Host.Results` **` or `** `Function`, skicka endast `Error` nivå och högre till Application Insights. Loggar för `Warning` nivå och nedan ignoreras.
2. För loggar med kategori `Host.Aggregator`, skicka alla loggar till Application Insights. Den `Trace` loggningsnivån är samma som det vissa tangenttryckningar kallar `Verbose`, men använda `Trace` i den [host.json](functions-host-json.md) fil.
3. För alla andra loggar Skicka endast `Information` nivå och högre till Application Insights.

Kategori-värdet i [host.json](functions-host-json.md) styr loggning för alla kategorier som börjar med samma värde. Till exempel `Host` i [host.json](functions-host-json.md) kontroller loggning för `Host.General`, `Host.Executor`, `Host.Results`, och så vidare.

Om [host.json](functions-host-json.md) innehåller flera kategorier som börjar med samma sträng, de längre som matchas. Anta exempelvis att du vill att allt från runtime utom `Host.Aggregator` att logga in på `Error` nivå, men du vill att `Host.Aggregator` att logga in på den `Information` nivå:

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

### <a name="category-hostresults"></a>Kategori Host.Results

De här loggarna visas som ”requests” i Application Insights. De anger lyckad eller misslyckad för en funktion.

![Begäranden-diagram](media/functions-monitoring/requests-chart.png)

Alla dessa loggar är skrivna på `Information` nivå, så om du filtrera i `Warning` eller senare, visas inte någon av dessa data.

### <a name="category-hostaggregator"></a>Kategori Host.Aggregator

Dessa loggar ge antal och medelvärden funktionsanrop via en [konfigurerbara](#configure-the-aggregator) period tid. Standardvärdet är 30 sekunder eller 1 000 resultat, beroende på vilket som inträffar först. 

Loggarna är tillgängliga i den **customMetrics** tabellen i Application Insights. Exempel är antalet körningar, Slutförandefrekvens och varaktighet.

![customMetrics fråga](media/functions-monitoring/custom-metrics-query.png)

Alla dessa loggar är skrivna på `Information` nivå, så om du filtrera i `Warning` eller senare, visas inte någon av dessa data.

### <a name="other-categories"></a>Kategorier

Alla loggar för kategorier än som redan i listan är tillgängliga i den **spårningar** tabellen i Application Insights.

![spårningar fråga](media/functions-monitoring/analytics-traces.png)

Alla loggar med kategorier som börjar med ”värd” är skrivna med Functions-körning. ”Funktion igång” och ”funktionen slutfört” loggar har du kategorin ”Host.Executor”. För lyckade körningar dessa loggar är `Information` nivån; undantag loggas på `Error` nivå. Körningen skapar även `Warning` nivå loggfiler, till exempel: köa meddelanden som skickas till skadliga kön.

Loggar som skrivits av Funktionskoden har kategorin ”funktion” och kan vara valfri nivå.

## <a name="configure-the-aggregator"></a>Konfigurera aggregatorn

Enligt vad som anges i föregående avsnitt, aggregerar körningen data om funktionskörningar under en viss tidsperiod. Standardvärdet är 30 sekunder eller 1 000 körs, beroende på vilket som kommer först. Du kan konfigurera den här inställningen i den [host.json](functions-host-json.md) fil.  Här är ett exempel:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Konfigurera sampling

Application Insights har en [sampling](../application-insights/app-insights-sampling.md) funktion som kan skydda dig från att producera för mycket telemetridata vid tidpunkter med hög belastning. När mängden inkommande telemetri överskrider ett angivet tröskelvärde, startar Application Insights att ignorera slumpmässigt några av de inkommande objekt. Standardinställningen för maximalt antal objekt per sekund är 5. Du kan konfigurera linjer i [host.json](functions-host-json.md).  Här är ett exempel:

### <a name="version-2x"></a>Version 2.x 

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 5
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
> [Sampling](../application-insights/app-insights-sampling.md) är aktiverat som standard. Om du verkar sakna data, kan du bara behöva justera inställningarna för sampling så att de passar din övervakning scenariot.

## <a name="write-logs-in-c-functions"></a>Sparar loggar på C#-funktioner

Du kan skriva loggar i Funktionskoden som visas som spårningar i Application Insights.

### <a name="ilogger"></a>ILogger

Använd en [ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) parameter i dina funktioner i stället för en `TraceWriter` parametern. Loggar som skapats med hjälp av `TraceWriter` går du till Application Insights, men `ILogger` kan du göra [strukturerad loggning](https://softwareengineering.stackexchange.com/questions/312197/benefits-of-structured-logging-vs-basic-logging).

Med en `ILogger` objekt kan du anropa `Log<level>` [tilläggsmetoder på ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.loggerextensions#methods) att skapa loggar. Till exempel följande kod skrivningar `Information` loggar med kategori ”funktionen”.

```cs
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger logger)
{
    logger.LogInformation("Request for item with key={itemKey}.", id);
```

### <a name="structured-logging"></a>Strukturerad loggning

Ordningen på platshållare, inte namnen avgör vilka parametrar som används i loggmeddelandet. Anta exempelvis att du har följande kod:

```csharp
string partitionKey = "partitionKey";
string rowKey = "rowKey";
logger.LogInformation("partitionKey={partitionKey}, rowKey={rowKey}", partitionKey, rowKey);
```

Om du behålla samma meddelande sträng och vända på ordningen på parametrarna skulle resulterande meddelandetexten ha värden på fel platser.

Platshållare hanteras på så sätt så att du kan göra strukturerad loggning. Application Insights lagrar parametern namn / värde-par förutom meddelande-sträng. Resultatet är att argumenten meddelandet blir fält som du kan fråga på.

Exempel: om din logger-metodanrop ser ut som i föregående exempel, du kan fråga fältet `customDimensions.prop__rowKey`. Den `prop__` prefix läggs lägger till att säkerställa att det finns inga konflikter mellan fälten körningen lägger till och fält funktionskoden.

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

### <a name="logging-custom-metrics"></a>Anpassade mått för loggning  

I C#-skript-funktioner, kan du använda den `LogMetric` tilläggsmetod på `ILogger` att skapa anpassade mått i Application Insights. Här är ett exempel metodanrop:

```csharp
logger.LogMetric("TestMetric", 1234); 
```

Den här koden är ett alternativ till att anropa `TrackMetric` med [Application Insights API för .NET](#custom-telemetry-in-c-functions).

## <a name="write-logs-in-javascript-functions"></a>Sparar loggar på JavaScript-funktioner

I Node.js-funktion, använder `context.log` loggfiler ska skrivas. Strukturerad loggning har inte aktiverats.

```
context.log('JavaScript HTTP trigger function processed a request.' + context.invocationId);
```

### <a name="logging-custom-metrics"></a>Anpassade mått för loggning  

I Node.js-funktion, kan du använda den `context.log.metric` metod för att skapa anpassade mått i Application Insights. Här är ett exempel metodanrop:

```javascript
context.log.metric("TestMetric", 1234); 
```

Den här koden är ett alternativ till att anropa `trackMetric` med [Node.js-SDK: N för Application Insights](#custom-telemetry-in-javascript-functions).

## <a name="custom-telemetry-in-c-functions"></a>Anpassad telemetri i C#-funktioner

Du kan använda den [Microsoft.ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) nuget för att skicka telemetri om anpassade data till Application Insights. Följande C# exemplet används den [telemetri om anpassade API: et](../application-insights/app-insights-api-custom-events-metrics.md). I exemplet avser en .NET-klassbiblioteket, men den Application Insights-koden är detsamma för C#-skript.

### <a name="version-2x"></a>Version 2.x

Version 2.x-körningen använder nyare funktioner i Application Insights för att korrelera automatiskt telemetri med den aktuella åtgärden. Behöver du inte ange manuellt igen `Id`, `ParentId`, eller `Name`.

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
    public static class HttpTrigger2
    {
        // In Functions v2, TelemetryConfiguration.Active is initialized with the InstrumentationKey
        // from APPINSIGHTS_INSTRUMENTATIONKEY. Creating a default TelemetryClient like this will 
        // automatically use that key for all telemetry. It will also enable telemetry correlation
        // with the current operation.
        // If you require a custom TelemetryConfiguration, create it initially with
        // TelemetryConfiguration.CreateDefault() to include this automatic correlation.
        private static TelemetryClient telemetryClient = new TelemetryClient();

        [FunctionName("HttpTrigger2")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", Route = null)]
            HttpRequest req, ExecutionContext context, ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");
            DateTime start = DateTime.UtcNow;

            // parse query parameter
            string name = req.Query
                .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
                .Value;

            // Track an Event
            var evt = new EventTelemetry("Function called");
            evt.Context.User.Id = name;
            telemetryClient.TrackEvent(evt);

            // Track a Metric
            var metric = new MetricTelemetry("Test Metric", DateTime.Now.Millisecond);
            metric.Context.User.Id = name;
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
            dependency.Context.User.Id = name;
            telemetryClient.TrackDependency(dependency);

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

Anropa inte `TrackRequest` eller `StartOperation<RequestTelemetry>`, ser du dubbla begäranden för ett funktionsanrop.  Functions-körning spårar automatiskt begäranden.

Konfigurerar inte `telemetryClient.Context.Operation.Id`. Detta är en global inställning och kommer att orsaka fel korrelation när många funktioner som körs samtidigt. I stället skapar en ny instans av telemetri (`DependencyTelemetry`, `EventTelemetry`) och ändra dess `Context` egenskapen. Sedan skickar telemetri-instans till motsvarande `Track` metoden på `TelemetryClient` (`TrackDependency()`, `TrackEvent()`). Detta säkerställer att telemetri har rätt korrelationsinformation för den aktuella funktionsanrop.

## <a name="custom-telemetry-in-javascript-functions"></a>Anpassad telemetri i JavaScript-funktioner

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

Den `tagOverrides` parameteruppsättningar `operation_Id` till funktionens anrops-ID. Den här inställningen kan du kombinera alla automatiskt genererade och anpassad telemetri för en viss funktionsanrop.

## <a name="known-issues"></a>Kända problem

### <a name="dependencies"></a>Beroenden

Beroenden som funktionen har till andra tjänster visas inte automatiskt, men du kan skriva anpassad kod för att visa beroenden. Exempelkoden i den [C# anpassad telemetri avsnittet](#custom-telemetry-in-c-functions) visar hur. Exempelkoden resulterar i en *programkartan* i Application Insights som ser ut så här:

![Programkarta](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Rapportera problem

Rapportera ett problem med Application Insights-integrering i funktioner och ge förslag eller begäranden, [skapa ett ärende i GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Övervakning utan Application Insights

Vi rekommenderar Application Insights för övervakningsfunktionerna eftersom det ger mer data och bättre sätt att analysera data. Men om du vill inbyggd loggning-system som använder Azure Storage kan du kan fortsätta att använda den.

### <a name="logging-to-storage"></a>Loggning till storage

Inbyggd loggning använder storage-kontot som anges av anslutningssträngen i den `AzureWebJobsDashboard` appinställningen. Välj en funktion i en funktionen app-sida och välj sedan den **övervakaren** fliken och väljer att behålla i klassiskt läge.

![Växla till klassisk vy](media/functions-monitoring/switch-to-classic-view.png)

 Du får en lista över funktionskörningar. Välj körning av en funktion att granska varaktighet, indata, fel och associerade loggfilerna.

Om du har aktiverat Application Insights tidigare, men nu vill du gå tillbaka till inbyggd loggning inaktivera Application Insights manuellt och väljer sedan den **övervakaren** fliken. Ta bort appinställningen APPINSIGHTS_INSTRUMENTATIONKEY om du vill inaktivera Application Insights-integrering.

Även om den **övervakaren** fliken visar Application Insights-data kan du se loggdata i filsystemet om du inte gjort [inaktiverad inbyggd loggning](#disable-built-in-logging). I Storage-resurs, går du till filer, väljer du tjänsten för funktionen och gå sedan till `LogFiles > Application > Functions > Function > your_function` till finns i loggfilen.

### <a name="real-time-monitoring"></a>Övervakning i realtid

Du kan strömma loggfiler till en kommandorad session på en lokal arbetsstation med hjälp av den [Azure kommandoradsgränssnitt (CLI)](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).  

För Azure CLI, använder du följande kommandon att logga in, väljer din prenumeration och loggfiler för stream:

```azurecli
az login
az account list
az account set <subscriptionNameOrId>
az webapp log tail --resource-group <resource group name> --name <function app name>
```

För Azure PowerShell använder du följande kommandon för att lägga till ditt Azure-konto, väljer din prenumeration och loggfiler för stream:

```powershell
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "<subscription name>" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name <function app name> -Tail
```

Mer information finns i [så strömma loggar](../app-service/web-sites-enable-diagnostic-log.md#streamlogs).

### <a name="viewing-log-files-locally"></a>Visa loggfiler lokalt

[!INCLUDE [functions-local-logs-location](../../includes/functions-local-logs-location.md)]

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core-loggning](/aspnet/core/fundamentals/logging/)

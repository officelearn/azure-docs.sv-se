---
title: "Övervaka Azure Functions"
description: "Lär dig hur du använder Azure Application Insights med Azure Functions för att övervaka körning av funktionen."
services: functions
author: tdykstra
manager: cfowler
editor: 
tags: 
keywords: "azure-funktioner, funktioner, händelsebearbetning, webhooks, dynamisk beräkning, serverlös arkitektur"
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/15/2017
ms.author: tdykstra
ms.openlocfilehash: 355cb2cef52b5dfecddae228d0cc24a069d3b695
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-functions"></a>Övervaka Azure Functions

## <a name="overview"></a>Översikt 

[Azure Functions](functions-overview.md) erbjuder inbyggda integreringen med [Azure Application Insights](../application-insights/app-insights-overview.md) för övervakning av funktioner. Den här artikeln visar hur du konfigurerar funktioner för att skicka telemetridata till Application Insights.

![Application Insights Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

Funktioner har också inbyggd övervakning som inte använder Application Insights. Vi rekommenderar Application Insights eftersom det ger mer data och bättre sätt att analysera data. Information om inbyggd övervakning finns i [i den här artikeln för senaste](#monitoring-without-application-insights).

## <a name="enable-application-insights-integration"></a>Aktivera Application Insights-integrering

Den måste veta instrumentation nyckeln för en instans av Application Insights för en funktionsapp att skicka data till Application Insights. Det finns två sätt att göra anslutningen som det [Azure-portalen](https://portal.azure.com):

* [Skapa en ansluten Application Insights-instans när du skapar appen funktionen](#new-function-app).
* [Ansluta en Application Insights-instans till en befintlig funktionsapp](#existing-function-app).
 
### <a name="new-function-app"></a>Ny funktionsapp

Aktivera Application Insights i funktionen appen **skapa** sidan:

1. Ange den **Programinsikter** växla **på**.

2. Välj en **Application Insights plats**.

   ![Aktivera Application Insights när du skapar en funktionsapp](media/functions-monitoring/enable-ai-new-function-app.png)

### <a name="existing-function-app"></a>Befintliga funktionsapp

Hämta en instrumentation nyckel och spara den i en funktionsapp:

1. Skapa Application Insights-instans. Ange program **allmänna**.

   ![Skapa en instans av Application Insights, Skriv allmänna](media/functions-monitoring/ai-general.png)

2. Kopiera instrumentation nyckeln från den **Essentials** sidan av Application Insights-instansen. Hovra över slutet av värdet för nyckeln visas att hämta en **Klicka om du vill kopiera** knappen.

   ![Kopiera nyckeln som Application Insights instrumentation](media/functions-monitoring/copy-ai-key.png)

1. I appen funktionen **programinställningar** sidan [lägga till en appinställning](functions-how-to-use-azure-function-app-settings.md#settings) med namnet APPINSIGHTS_INSTRUMENTATIONKEY och klistra in nyckeln instrumentation.

   ![Lägg till nyckel för instrumentation appinställningar](media/functions-monitoring/add-ai-key.png)

1. Klicka på **Spara**.

## <a name="view-telemetry-data"></a>Visa telemetridata

För att navigera till Application Insights från en funktionsapp i portalen, Välj den **Programinsikter** länk på funktionsapp **översikt** sidan.

Information om hur du använder Application Insights finns i [Application Insights dokumentationen](https://docs.microsoft.com/azure/application-insights/). Detta avsnitt visar några exempel på hur du visar data i Application Insights. Om du redan är bekant med Application Insights kan du gå direkt till [avsnitt om att konfigurera och anpassa telemetridata](#configure-categories-and-log-levels).

I [Metrics Explorer](../application-insights/app-insights-metrics-explorer.md), du kan skapa diagram och aviseringar baserat på mått sådana som antal funktionsanrop, körningstid och lyckade resultat.

![Metrics Explorer](media/functions-monitoring/metrics-explorer.png)

På den [fel](../application-insights/app-insights-asp-net-exceptions.md) fliken kan du skapa diagram och aviseringar baserat på funktionen fel och server undantag. Den **åtgärdsnamn** är namnet på funktionen. Fel i beroenden visas inte om du implementerar [telemetri om anpassade](#custom-telemetry-in-c-functions) för beroenden.

![fel](media/functions-monitoring/failures.png)

På den [prestanda](../application-insights/app-insights-performance-counters.md) kan du analysera prestandaproblem.

![Prestanda](media/functions-monitoring/performance.png)

Den **servrar** visar resursutnyttjande och genomströmning per server. Dessa data kan vara användbart för felsökning av scenarier där funktioner bogging ned underliggande resurserna. Servrar kallas *molnet rollinstanser*. 

![Servrar](media/functions-monitoring/servers.png)

Den [direktsänd dataström med mått](../application-insights/app-insights-live-stream.md) mätvärdesdata visar när den skapas i realtid.

![Direktsänd ström](media/functions-monitoring/live-stream.png)

## <a name="query-telemetry-data"></a>Fråga telemetridata

[Application Insights Analytics](../application-insights/app-insights-analytics.md) ger dig tillgång till alla telemetridata i form av tabeller i en databas. Analytics ger ett frågespråk för att extrahera och manipulera data.

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

Du kan använda Application Insights utan någon anpassad konfiguration, men standardkonfigurationen kan leda till stora mängder data. Om du använder en Visual Studio-Azure-prenumeration kan du träffa dina data cap för App Insights. Resten av den här artikeln visar hur du konfigurerar och anpassa de data som dina funktioner skickar till Application Insights.

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
|kritiska    | 5 |
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

1. Efter loggar med kategorin ”Host.Results” eller ”funktionen” Skicka endast `Error` nivå och högre till Application Insights. Loggar för `Information` nivå och nedanför ignoreras.
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

Loggarna visas som ”customMetrics” i Application Insights. Exempel är antalet körs, lyckade resultat och varaktighet.

![customMetrics fråga](media/functions-monitoring/custom-metrics-query.png)

Alla dessa loggar skrivs på `Information` nivå, så om du filtrerar på `Warning` eller senare, visas inte någon av dessa data.

### <a name="other-categories"></a>Andra kategorier

Alla loggar för kategorier än som finns redan visas som ”spår” i Application Insights.

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

Application Insights har en [provtagning](../application-insights/app-insights-sampling.md) funktion som kan skydda dig från producerar för mycket telemetridata ibland av belastning. När antalet objekt som telemetri överskrider en angiven hastighet, startar Application Insights att ignorera slumpmässigt vissa inkommande objekt. Du kan konfigurera provtagning i *host.json*.  Här är ett exempel:

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

Till exempel om loggaren-metodanrop ser ut som i föregående exempel, du kan fråga fältet `customDimensions.prop__rowKey`. Prefixet läggs så att det inte finns några konflikter mellan körningsmiljön lägger till och fält som lägger till funktionskoden.

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

        private static TelemetryClient telemetry = 
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

            telemetry.Context.Operation.Id = context.InvocationId.ToString();
            telemetry.Context.Operation.Name = "cs-http";
            if (!String.IsNullOrEmpty(name))
            {
                telemetry.Context.User.Id = name;
            }
            telemetry.TrackEvent("Function called");
            telemetry.TrackMetric("Test Metric", DateTime.Now.Millisecond);
            telemetry.TrackDependency("Test Dependency", 
                "swapi.co/api/planets/1/", 
                start, DateTime.UtcNow - start, true);

            return name == null
                ? req.CreateResponse(HttpStatusCode.BadRequest, 
                    "Please pass a name on the query string or in the request body")
                : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
        }
    }
}
```

Anropa inte `TrackRequest` eller `StartOperation<RequestTelemetry>`, ser du dubbla begäranden för ett funktionsanrop.  Functions-runtime spårar automatiskt begäranden.

Ange `telemetry.Context.Operation.Id` att anropa ID varje gång som din funktion har startats. Detta gör det möjligt att korrelera alla telemetri objekt för ett givet funktionsanrop.

```cs
telemetry.Context.Operation.Id = context.InvocationId.ToString();
```

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

Beroenden visas inte automatiskt, men du kan skriva anpassade kod för att visa beroenden. Exempelkoden i den [C# telemetri om anpassade avsnittet](#custom-telemetry-in-c-functions) visar hur. Exempelkoden resulterar i en *programavbildningen* i Application Insights ser ut så här:

![Programkarta](media/functions-monitoring/app-map.png)

### <a name="report-issues"></a>Rapporten problem

Rapportera ett problem med Application Insights integrering i funktioner och ge förslag eller begäran, [skapar ett problem i GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="monitoring-without-application-insights"></a>Övervakning utan Application Insights

Vi rekommenderar Application Insights för övervakningsfunktionerna eftersom det ger mer data och bättre sätt att analysera data. Men du kan också hitta telemetri och loggningsdata på Azure portal-sidorna för en funktionsapp. 

Välj den **övervakaren** fliken för en funktion och du hämta en lista över funktionen körningar. Välj en funktion körning ska granska varaktighet, indata, fel och associerade loggfilerna.

> [!IMPORTANT]
> När du använder den [förbrukning som värd för planen](functions-overview.md#pricing) för Azure Functions i **övervakning** sida vid sida i appen funktionen visas inte några data. Detta beror på att plattformen dynamiskt skalar och hanterar compute-instanser för dig. De här måtten är inte meningsfull på en plan för användning.

### <a name="real-time-monitoring"></a>Realtidsövervakning

Realtidsövervakning är tillgänglig genom att klicka på **live händelseströmmen** på funktionen **övervakaren** fliken. Dataströmmen direktsänd händelse visas i ett diagram i en ny flik i webbläsaren

> [!NOTE]
> Det finns ett känt problem som kan orsaka att data ska kunna fyllas i. Du kan behöva stänga fliken som innehåller dataströmmen direktsänd händelse och klicka sedan på **live händelseströmmen** igen för att göra det möjligt att fylla i din händelsedata dataströmmen korrekt. 

Statistiken är realtid men den faktiska grafiska Körningsdata kanske cirka 10 sekunder svarstid.

### <a name="monitor-log-files-from-a-command-line"></a>Övervaka loggfilerna från en kommandorad

Du kan strömma loggfiler till en kommandorad session på en lokal arbetsstation med Azure kommandoradsgränssnittet (CLI) 1.0 eller PowerShell.

### <a name="monitor-function-app-log-files-with-the-azure-cli-10"></a>Övervaka funktionen app-loggfiler med Azure CLI 1.0

Du kommer igång [installera Azure CLI 1.0](../cli-install-nodejs.md) och [logga in på Azure](../xplat-cli-connect.md).

Använd följande kommandon för att aktivera klassiska Service Management-läge, väljer din prenumeration och strömma loggfiler:

```
azure config mode asm
azure account list
azure account set <subscriptionNameOrId>
azure site log tail -v <function app name>
```

### <a name="monitor-function-app-log-files-with-powershell"></a>Övervaka funktionen app-loggfiler med PowerShell

Du kommer igång [installera och konfigurera Azure PowerShell](/powershell/azure/overview).

Använd följande kommandon för att lägga till ditt Azure-konto, väljer din prenumeration och strömma loggfiler:

```
PS C:\> Add-AzureAccount
PS C:\> Get-AzureSubscription
PS C:\> Get-AzureSubscription -SubscriptionName "MyFunctionAppSubscription" | Select-AzureSubscription
PS C:\> Get-AzureWebSiteLog -Name MyFunctionApp -Tail
```

Mer information finns i [så här: strömma loggar för web apps](../app-service/web-sites-enable-diagnostic-log.md#streamlogs). 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Mer information om Application Insights](https://docs.microsoft.com/azure/application-insights/)

> [!div class="nextstepaction"]
> [Mer information om loggning ramen som använder funktioner](https://docs.microsoft.com/aspnet/core/fundamentals/logging?tabs=aspnetcore2x)

---
title: Application Insights API för anpassade händelser och mått | Microsoft Docs
description: Infoga några rader med kod i din enhet eller desktop-appen eller webbsidan service för att spåra användning och diagnostisera problem.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/16/2018
ms.author: mbullwin
ms.openlocfilehash: 7422e7145acfece9c9da3277c8d92c949220dbab
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/04/2019
ms.locfileid: "54039324"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API för anpassade händelser och mått

Infoga några rader med kod i ditt program för att ta reda på vad användarna gör med den eller för att diagnostisera problem. Du kan skicka telemetri från enheter och skrivbordsenheter appar, webbklienter och servrar. Använd den [Azure Application Insights](../../application-insights/app-insights-overview.md) core telemetriska API: N att skicka anpassade händelser och mått och din egen version av standardmoduler. Detta API är som standard datainsamlare för Application Insights använder samma API.

> [!NOTE]
> `TrackMetric()` är inte längre den bästa metoden för att skicka anpassade mått för din .NET-baserade program. I [version 2,60 beta 3](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/develop/CHANGELOG.md#version-260-beta3) av Application Insights .NET SDK en ny metod [ `TelemetryClient.GetMetric()` ](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) introducerades. Från och med Application Insights .NET SDK [version 2.72](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.getmetric?view=azure-dotnet) den här funktionen är nu en del av den stabila versionen.

## <a name="api-summary"></a>API-sammanfattning

Core API är uniform på alla plattformar förutom några få varianter som `GetMetric`(.NET).

| Metod | Används för |
| --- | --- |
| [`TrackPageView`](#page-views) |Sidor, skärmar, blad eller formulär. |
| [`TrackEvent`](#trackevent) |Användaråtgärder och andra händelser. Används för att spåra användarnas beteende eller för att övervaka prestanda. |
| [`GetMetric`](#getmetric) |Noll och flerdimensionella mått, centralt konfigurerade aggregering, C# endast. |
| [`TrackMetric`](#trackmetric) |Prestandamått, till exempel kölängder inte relaterade till specifika händelser. |
| [`TrackException`](#trackexception) |Loggning undantag för diagnos. Spåra där de sker i förhållande till andra händelser och undersöka stackspårningar. |
| [`TrackRequest`](#trackrequest) |Loggning av frekvens och varaktighet för servern för serverbegäranden för prestandaanalys. |
| [`TrackTrace`](#tracktrace) |Diagnostiklogg meddelanden. Du kan hämta loggar från tredje part. |
| [`TrackDependency`](#trackdependency) |Loggning varaktighet och frekvens för anrop till externa komponenter som din app är beroende av. |

Du kan [bifoga egenskaper och mätvärden](#properties) till de flesta av dessa telemetri-anrop.

## <a name="prep"></a>Innan du börjar

Om du inte ännu inte har en referens i Application Insights SDK:

* Lägg till Application Insights SDK i projektet:

  * [ASP.NET-projekt](../../azure-monitor/app/asp-net.md)
  * [Java-projekt](../../azure-monitor/app/java-get-started.md)
  * [Node.js-projekt](../../azure-monitor/app/nodejs.md)
  * [JavaScript i varje webbsida](../../azure-monitor/app/javascript.md) 
* Inkludera följande i din enhets eller webbservers kod:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`

    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Hämta en TelemetryClient-instans

Hämta en instans av `TelemetryClient` (förutom i JavaScript i webbsidor):

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```

*Visual Basic*

```vb
Private Dim telemetry As New TelemetryClient
```

*Java*

```java
private TelemetryClient telemetry = new TelemetryClient();
``` 

*Node.js*

```javascript
var telemetry = applicationInsights.defaultClient;
```

TelemetryClient är trådsäkra.

Inkommande HTTP-begäranden som automatiskt avbildas för ASP.NET och Java-projekt. Du kanske vill skapa flera instanser av TelemetryClient för andra modulen av din app. Du kan exempelvis ha en TelemetryClient-instans i klassen mellanprogram till rapporten affärshändelser logic. Du kan ange egenskaper, till exempel användar-ID och DeviceId för att identifiera datorn. Den här informationen är kopplat till alla händelser som skickar instansen. 

*C#*

```csharp
TelemetryClient.Context.User.Id = "...";
TelemetryClient.Context.Device.Id = "...";
```

*Java*

```java
telemetry.getContext().getUser().setId("...");
telemetry.getContext().getDevice().setId("...");
```

Du kan använda i Node.js-projekt, `new applicationInsights.TelemetryClient(instrumentationKey?)` att skapa en ny instans, men detta rekommenderas endast för scenarier som kräver isolerade konfigurationen från singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent

I Application Insights, en *anpassad händelse* är en datapunkt som du kan visa i [Måttutforskaren](../../azure-monitor/app/metrics-explorer.md) ett aggregerat antal och i [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md) som enskilda förekomster. (Det är inte relaterade till MVC eller andra ramverk ”händelser”.)

Infoga `TrackEvent` anropar i din kod att räkna olika händelser. Hur ofta användarna välja en viss funktion, hur ofta de uppnå specifika mål eller kanske är hur ofta de gör att vissa typer av misstag.

I en spel-app kan till exempel skicka en händelse varje gång en användare vinner:

*JavaScript*

```javascript
appInsights.trackEvent("WinGame");
```

*C#*

```csharp
telemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
telemetry.TrackEvent("WinGame")
```

*Java*

```java
telemetry.trackEvent("WinGame");
```

*Node.js*

```javascript
telemetry.trackEvent({name: "WinGame"});
```

### <a name="custom-events-in-analytics"></a>Anpassade händelser i Analytics

Telemetri är tillgänglig i den `customEvents` tabellen i [Application Insights Analytics](analytics.md). Varje rad som representerar ett anrop till `trackEvent(..)` i din app.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. För exempel itemCount == 10 sätt att för 10 anrop till trackEvent() processen sampling endast skickas en av dem. För att få en korrekt antal anpassade händelser, bör du använda därför använda koden som `customEvents | summarize sum(itemCount)`.

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Exempel

*C#*

```csharp
#pragma warning disable CA1716  // Namespace naming

namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using TraceSeveretyLevel = Microsoft.ApplicationInsights.DataContracts.SeverityLevel;

    /// <summary>
    /// Most simple cases are one-liners.
    /// This is all possible without even importing an additional namespace.
    /// </summary>

    public class Sample01
    {
        /// <summary />
        public static void Exec()
        {
            // *** SENDING METRICS ***

            // Recall how you send custom telemetry with Application Insights in other cases, e.g. Events.
            // The following will result in an EventTelemetry object to be sent to the cloud right away.
            TelemetryClient client = new TelemetryClient();
            client.TrackEvent("SomethingInterestingHappened");

            // Metrics work very similar. However, the value is not sent right away.
            // It is aggregated with other values for the same metric, and the resulting summary (aka "aggregate" is sent automatically every minute.
            // To mark this difference, we use a pattern that is similar, but different from the established TrackXxx(..) pattern that sends telemetry right away:

            client.GetMetric("CowsSold").TrackValue(42);

            // *** MULTI-DIMENSIONAL METRICS ***

            // The above example shows a zero-dimensional metric.
            // Metrics can also be multi-dimensional.
            // In the initial version we are supporting up to 2 dimensions, and we will add support for more in the future as needed.
            // Here is an example for a one-dimensional metric:

            Metric animalsSold = client.GetMetric("AnimalsSold", "Species");

            animalsSold.TrackValue(42, "Pigs");
            animalsSold.TrackValue(24, "Horses");

            // The values for Pigs and Horses will be aggregated separately from each other and will result in two distinct aggregates.
            // You can control the maximum number of number data series per metric (and thus your resource usage and cost).
            // The default limits are no more than 1000 total data series per metric, and no more than 100 different values per dimension.
            // We discuss elsewhere how to change them.
            // We use a common .Net pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", TraceSeveretyLevel.Error);
            }

            // You can inspect a metric object to reason about its current state. For example:
            int currentNumberOfSpecies = animalsSold.GetDimensionValues(1).Count;
        }

        private static void ResetDataStructure()
        {
            // Do stuff
        }

        private static Tuple<int, string> ReadSpeciesFromUserInput()
        {
            return Tuple.Create(18, "Cows");
        }

        private static int AddItemsToDataStructure()
        {
            // Do stuff
            return 5;
        }
    }
}
```

## <a name="trackmetric"></a>TrackMetric

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric är inaktuell i .NET SDK. Mått ska alltid vara preaggregeras över en tidsperiod innan de skickas. Använd någon av GetMetric(..) överlagringar för att hämta ett mått-objekt för att komma åt funktionerna för SDK-aggregering. Om du implementerar din egen logik för aggregering, kan du använda metoden spåra (ITelemetry metricTelemetry) för att skicka resulterande aggregeringar. Om ditt program måste du skicka en separat telemetriobjekt vid olika tillfällen utan aggregering över tid, har du förmodligen ett användningsfall för händelsetelemetri; Se TelemetryClient.TrackEvent (Microsoft.Applicationlnsights.DataContracts.EventTelemetry).

Application Insights kan skapa diagram över mått som inte är kopplade till specifika händelser. Exempelvis kan du övervaka en Kölängd med jämna mellanrum. De enskilda mätningarna är mindre intressanta än varianter och trender med mätvärden, och så statistiska stapeldiagram är användbara.

Du kan använda för att skicka mått till Application Insights i `TrackMetric(..)` API. Det finns två sätt att skicka ett mått:

* Enskilt värde. Varje gång du gör ett mått i ditt program, kan du skicka motsvarande värde till Application Insights. Anta att du har ett mått som beskriver antalet objekt i en behållare. Du först lägger till tre objekt i behållaren och sedan du tar bort två objekt under en viss tidsperiod. Du skulle därför anropar `TrackMetric` två gånger: först skicka värdet `3` och sedan värdet `-2`. Application Insights lagrar båda värdena för din räkning.

* Aggregering. När du arbetar med mått, är data som sällan varje enskild mätning av intresse. I stället är det viktigt med en sammanfattning av vad som hände under en viss tidsperiod. Sådana sammanfattning kallas _aggregering_. I exemplet ovan är summan av mått för den aktuella tidsperioden `1` och antal mått värdena är `2`. När du använder den aggregering-metoden kan du bara anropa `TrackMetric` en gång per tidsperiod och skicka de sammanlagda värdena. Det här är den rekommenderade metoden eftersom den kan avsevärt minska kostnader och prestanda försämras genom att skicka färre datapunkter till Application Insights, samtidigt som samlar fortfarande in all relevant information.

### <a name="examples"></a>Exempel

#### <a name="single-values"></a>Enstaka värden

Skicka ett enda värde:

*JavaScript*

 ```javascript
appInsights.trackMetric("queueLength", 42.0);
 ```

*C#*

```csharp
var sample = new MetricTelemetry();
sample.Name = "metric name";
sample.Value = 42.3;
telemetryClient.TrackMetric(sample);
```

*Java*

```java
telemetry.trackMetric("queueLength", 42.0);
```

*Node.js*

 ```javascript
telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

### <a name="custom-metrics-in-analytics"></a>Anpassade mått i Analytics

Telemetri är tillgänglig i den `customMetrics` tabellen i [Application Insights Analytics](analytics.md). Varje rad som representerar ett anrop till `trackMetric(..)` i din app.

* `valueSum` -Detta är summan av mätningar. Om du vill ha medelvärdet, dela med `valueCount`.
* `valueCount` -Antalet mått som har aggregeras till detta `trackMetric(..)` anropa.

## <a name="page-views"></a>Sidvisning

I en enhet eller en webbsida app skickas sidvystelemetri som standard när varje skärmbild eller sida har lästs in. Men du kan ändra det om du vill spåra sidvisningar vid ytterligare eller andra tider. I en app som visar flikarna eller blad, kanske du vill spåra en sida när användaren öppnar ett nytt blad.

Användare och sessioner data skickas som egenskaper tillsammans med sidvisningar, så att användare och sessioner diagrammen kommer alive när det finns telemetri för sidvisningar.

### <a name="custom-page-views"></a>Anpassade sidvisningar

*JavaScript*

```javascript
appInsights.trackPageView("tab1");
```

*C#*

```csharp
telemetry.TrackPageView("GameReviewPage");
```

*Visual Basic*

```vb
telemetry.TrackPageView("GameReviewPage")
```

*Java*

```java
telemetry.trackPageView("GameReviewPage");
```

Om du har flera flikar på olika HTML-sidor, kan du ange URL: en för:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Tidsinställning sidvisningar

Som standard tiderna som rapporteras som **inläsningstid för sidvisning** mäts från när webbläsaren skickar en begäran om, tills webbläsarens sidan händelsen anropas.

I stället kan du antingen:

* Ange ett explicit varaktighet i den [flyttat trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) anropa: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Använda Sidvisning tidsgränsen anrop `startTrackPage` och `stopTrackPage`.

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Det namn som du använder som första parameter associerar start och stopp-anrop. Standard namnet på aktuella sidan.

De resulterande sidan belastningen varaktigheter som visas i Metrics Explorer härleds från intervallet mellan start och stopp-anrop. Det är upp till dig vilka intervall du faktiskt tid.

### <a name="page-telemetry-in-analytics"></a>Sidan telemetri i Analytics

I [Analytics](analytics.md) två tabellerna visar data från funktioner i webbläsaren:

* Den `pageViews` tabellen innehåller data om URL- och rubrik
* Den `browserTimings` tabellen innehåller data om klientens prestanda, till exempel den tid det tar att bearbeta inkommande

Hitta hur lång tid tar för att bearbeta olika sidor på webbläsaren:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

Identifiera popularities av olika webbläsare:

```kusto
pageViews
| summarize count() by client_Browser
```

Om du vill associera sidvisningar AJAX-anrop, Anslut med beroenden:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest

Servern SDK använder TrackRequest för att logga HTTP-begäranden.

Du kan också anropa den själv om du vill simulera begäranden i ett sammanhang där du behöver inte modulen web service körs.

Det rekommenderade sättet att skicka begärandetelemetri om är dock där begäran fungerar som en <a href="#operation-context">åtgärdskontexten</a>.

## <a name="operation-context"></a>Åtgärdskontexten

Du kan jämföra telemetri objekt tillsammans genom att associera dem med åtgärden kontext. Modulen standard spårning av förfrågningar sker för undantag och andra händelser som sänds när en HTTP-begäran bearbetas. I [Search](../../azure-monitor/app/diagnostic-search.md) och [Analytics](analytics.md), hittar du enkelt eventuella händelser som är kopplat till begäran med hjälp av dess åtgärd Id.

Se [telemetrikorrelation i Application Insights](../../azure-monitor/app/correlation.md) för mer information om korrelation.

När du kartlägger telemetri manuellt, det enklaste sättet att se till att telemetrikorrelation med hjälp av det här mönstret:

*C#*

```csharp
// Establish an operation context and associated telemetry item:
using (var operation = telemetryClient.StartOperation<RequestTelemetry>("operationName"))
{
    // Telemetry sent in here will use the same operation ID.
    ...
    telemetryClient.TrackTrace(...); // or other Track* calls
    ...

    // Set properties of containing telemetry item--for example:
    operation.Telemetry.ResponseCode = "200";

    // Optional: explicitly send telemetry item:
    telemetryClient.StopOperation(operation);

} // When operation is disposed, telemetry item is sent.
```

Tillsammans med inställningen en åtgärdskontexten `StartOperation` skapar ett telemetriobjekt av typen som du anger. Den skickar telemetri objektet automatiskt när åtgärden, eller om du explicit anropa `StopOperation`. Om du använder `RequestTelemetry` som telemetrityp av varaktigheten är inställd på tidsinställda intervall mellan start och stopp.

Telemetri artiklar som rapporterats inom ett omfång för åtgärden blir underordnade om du av sådana åtgärder. Åtgärden kontexter kan kapslas.

I Search åtgärdskontexten används för att skapa den **relaterade objekt** lista:

![Relaterade objekt](./media/api-custom-events-metrics/21.png)

Se [spåra anpassade åtgärder med Application Insights SDK för .NET](../../azure-monitor/app/custom-operations-tracking.md) mer information om anpassade åtgärder spårning.

### <a name="requests-in-analytics"></a>Begäranden i Analytics

I [Application Insights Analytics](analytics.md), begär visa upp i den `requests` tabell.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. För exempel itemCount == 10 sätt att för 10 anrop till trackRequest() processen sampling endast skickas en av dem. Hämta rätt antal förfrågningar och genomsnittlig varaktighet uppdelat efter namn på begäran med kod som:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Skicka undantag till Application Insights:

* Att [räkna dem](../../azure-monitor/app/metrics-explorer.md), som en indikation på hur ofta ett problem.
* Att [granska enskilda förekomster](../../azure-monitor/app/diagnostic-search.md).

Rapporterna innehåller stackspårningar.

*C#*

```csharp
try
{
    ...
}
catch (Exception ex)
{
    telemetry.TrackException(ex);
}
```

*Java*

```java
try {
    ...
} catch (Exception ex) {
    telemetry.trackException(ex);
}
```

*JavaScript*

```javascript
try
{
    ...
}
catch (ex)
{
    appInsights.trackException(ex);
}
```

*Node.js*

```javascript
try
{
    ...
}
catch (ex)
{
    telemetry.trackException({exception: ex});
}
```

SDK: erna fånga undantag som många automatiskt, så du behöver alltid att anropa TrackException uttryckligen.

* ASP.NET: [Skriva kod för att fånga undantag från](../../azure-monitor/app/asp-net-exceptions.md).
* J2EE: [Undantag som fångas automatiskt](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Undantag fångas automatiskt. Om du vill inaktivera automatisk insamling till en rad i kodfragmentet som du infogar i dina webbsidor:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Undantag i Analytics

I [Application Insights Analytics](analytics.md), undantag som visas i den `exceptions` tabell.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift, den `itemCount` egenskapen visar ett värde större än 1. För exempel itemCount == 10 sätt att av 10 anrop till trackException() processen sampling endast skickas en av dem. För att få en korrekt antal undantag som uppdelat efter typ av undantag kan du använda koden som:

```kusto
exceptions
| summarize sum(itemCount) by type
```

De flesta av de viktiga Stackinformation redan extraheras till separata variabler, men du kan sätta ihop den `details` struktur och få mer. Eftersom den här strukturen är dynamisk, bör du skicka resultatet för den typ som du förväntar dig. Exempel:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Om du vill associera undantag med relaterade begäranden, använder du en koppling:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace

Använd TrackTrace för att diagnostisera problem genom att skicka ”länken.Tryck” till Application Insights. Du kan skicka mängder diagnostikdata och granska dem i [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md).

I .NET [logga kort](../../azure-monitor/app/asp-net-trace-logs.md) använder den här API för att skicka loggar från tredje part till portalen.

I Java för [Standard tangenttryckningar som Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) använder Application Insights Log4j eller Logback Appenders för att skicka loggar från tredje part till portalen.

*C#*

```csharp
telemetry.TrackTrace(message, SeverityLevel.Warning, properties);
```

*Java*

```java
telemetry.trackTrace(message, SeverityLevel.Warning, properties);
```

*Node.js*

```javascript
telemetry.trackTrace({
    message: message,
    severity: applicationInsights.Contracts.SeverityLevel.Warning,
    properties: properties
});
```

Du kan söka i meddelandeinnehåll, men du kan inte filtrera (till skillnad från egenskapsvärden) på den.

Storleksgränsen för `message` är mycket högre än gränsen för egenskaper.
En fördel med TrackTrace är att du kan publicera relativt lång data i meddelandet. Du kan exempelvis koda det postdata.  

Dessutom kan du lägga till en allvarlighetsgrad ditt meddelande. Och precis som andra telemetri, du kan lägga till egenskapsvärden som hjälper dig att filter eller för olika uppsättningar med spårningar. Exempel:

*C#*

```csharp
var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
telemetry.TrackTrace("Slow database response",
                SeverityLevel.Warning,
                new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```java
Map<String, Integer> properties = new HashMap<>();
properties.put("Database", db.ID);
telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);
```

I [Search](../../azure-monitor/app/diagnostic-search.md), sedan kan du enkelt filtrera ut alla meddelanden från en viss allvarlighetsgrad som är relaterade till en viss databas.

### <a name="traces-in-analytics"></a>Spårningar i Analytics

I [Application Insights Analytics](analytics.md), anrop till TrackTrace som visas i den `traces` tabell.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. Exempel itemCount == 10 innebär att för 10 anrop till `trackTrace()`, sampling processen överförs endast en av dem. För att få en korrekt antal spårning av anrop du bör använda därför kod som `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency

Använd TrackDependency-anrop för att spåra svarstider och slutförandefrekvenser för anrop till ett externt kodavsnitt kod. Resultatet visas i beroendediagrammen i portalen.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("myDependency", "myCall", startTime, timer.Elapsed, success);
     // The call above has been made obsolete in the latest SDK. The updated call follows this format:
     // TrackDependency (string dependencyTypeName, string dependencyName, string data, DateTimeOffset startTime, TimeSpan duration, bool success);
}
```

*Java*

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
}
finally {
    long endTime = System.currentTimeMillis();
    long delta = endTime - startTime;
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    telemetry.setTimeStamp(startTime);
    telemetry.trackDependency(dependencyTelemetry);
}
```

*JavaScript*

```javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({
        dependencyTypeName: "myDependency",
        name: "myCall",
        duration: elapsed,
        success: success
    });
}
```

Kom ihåg att server-SDK: er inkluderar en [beroende modulen](../../azure-monitor/app/asp-net-dependencies.md) som identifierar och spårar vissa beroendeanrop automatiskt – till exempel till databaser och REST API: er. Du måste installera en agent på servern för att göra modulen fungerar. 

I Java, vissa beroendeanrop kan automatiskt spåras med hjälp av [Java-agenten](../../azure-monitor/app/java-agent.md).

Du kan använda det här anropet om du vill spåra anrop som automatiserad spårning inte fångar upp, eller om du inte vill installera agenten.

Om du vill inaktivera modulen standard beroende-spårning i C#, redigera [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) och ta bort referensen till `DependencyCollector.DependencyTrackingTelemetryModule`. I Java, inte installera java-agenten om du inte vill samla in standard beroenden automatiskt.

### <a name="dependencies-in-analytics"></a>Beroenden i Analytics

I [Application Insights Analytics](analytics.md), trackDependency ringer visa upp den `dependencies` tabell.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. För exempel itemCount == 10 sätt att för 10 anrop till trackDependency() processen sampling endast skickas en av dem. För att få en korrekt antal beroenden uppdelat efter mål-komponenten kan du använda kod som:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Om du vill associera beroenden med deras relaterade begäranden, använder du en koppling:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Tömmer data

SDK: N skickar vanligtvis data som ibland valt att minimera påverkan på användaren. Men i vissa fall kan kanske du vill i bufferten – till exempel om du använder SDK: N i ett program som stängs av.

*C#*

 ```csharp
telemetry.Flush();
// Allow some time for flushing before shutdown.
System.Threading.Thread.Sleep(5000);
```

*Java*

```java
telemetry.flush();
//Allow some time for flushing before shutting down
Thread.sleep(5000);
```

*Node.js*

```javascript
telemetry.flush();
```

Observera att funktionen är asynkron för den [server telemetri kanal](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Vi rekommenderar ska flush() metoden användas i aktiviteten avstängning av programmet.

## <a name="authenticated-users"></a>Autentiserade användare

I en webbapp identifieras användare (som standard) av cookies. En användare kan räknas mer än en gång om de har åtkomst till din app från en annan dator eller webbläsare, eller om de tar bort cookies.

Om användare loggar in till din app kan få du en mer exakt antal genom att ange autentiserat användar-ID i webbläsarkoden:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

På en webbplats med ASP.NET MVC-program, till exempel:

*Razor*

```cshtml
@if (Request.IsAuthenticated)
{
    <script>
        appInsights.setAuthenticatedUserContext("@User.Identity.Name
            .Replace("\\", "\\\\")"
            .replace(/[,;=| ]+/g, "_"));
    </script>
}
```

Det är inte nödvändigt att använda användarens faktiska inloggningsnamn. Den innehåller endast ska vara ett ID som är unika för användaren. Namnet får inte innehålla blanksteg eller något av tecknen `,;=|`.

Användar-ID är också i en sessions-cookie och skickas till servern. Om server SDK har installerats skickas autentiserat användar-ID som en del av kontextegenskaperna av både klienten och servern telemetri. Sedan kan du filtrera och söka på den.

Om din app grupperar användare i konton, kan du också ange en identifierare för kontot (med samma teckenbegränsningar).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

I [Måttutforskaren](../../azure-monitor/app/metrics-explorer.md), du kan skapa ett diagram som räknar **autentiserad användare,**, och **användarkonton**.

Du kan också [search](../../azure-monitor/app/diagnostic-search.md) för klienten datapunkter med specifika användarnamn och -konton.

## <a name="properties"></a>Filtrera, söka och segmentera dina data med hjälp av egenskaper

Du kan koppla egenskaper och mätningar till dina händelser (och även bläddra vyer, undantag och andra telemetridata till mått,).

*Egenskaper för* är strängvärden som du kan använda för att filtrera din telemetri i användningsrapporten. Till exempel om din app tillhandahåller flera spel, kan du ansluta spelet till varje händelse så ser du vilka spel som mer populära.

Det finns en gräns på 8192 på strängen. (Om du vill skicka stora mängder data kan använda parametern meddelande av [TrackTrace](#track-trace).)

*Mått* är numeriska värden som kan presenteras grafiskt. Du kanske vill se om det finns en gradvis ökning poängen som spelare uppnå. Diagrammen kan visas efter de egenskaper som skickas till händelsen, så att du kan få separat eller liggande diagram för olika spel.

Mått-värden som ska visas korrekt bör de vara större än eller lika med 0.

Det finns några [gränser för antalet egenskaper och egenskapsvärden mått](#limits) som du kan använda.

*JavaScript*

```javascript
appInsights.trackEvent
    ("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );

appInsights.trackPageView
    ("page name", "http://fabrikam.com/pageurl.html",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric metrics:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
        );
```

*C#*

```csharp
// Set up some properties and metrics:
var properties = new Dictionary <string, string>
    {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
var metrics = new Dictionary <string, double>
    {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

// Send the event:
telemetry.TrackEvent("WinGame", properties, metrics);
```

*Node.js*

```javascript
// Set up some properties and metrics:
var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

// Send the event:
telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});
```

*Visual Basic*

```vb
' Set up some properties:
Dim properties = New Dictionary (Of String, String)
properties.Add("game", currentGame.Name)
properties.Add("difficulty", currentGame.Difficulty)

Dim metrics = New Dictionary (Of String, Double)
metrics.Add("Score", currentGame.Score)
metrics.Add("Opponents", currentGame.OpponentCount)

' Send the event:
telemetry.TrackEvent("WinGame", properties, metrics)
```

*Java*

```java
Map<String, String> properties = new HashMap<String, String>();
properties.put("game", currentGame.getName());
properties.put("difficulty", currentGame.getDifficulty());

Map<String, Double> metrics = new HashMap<String, Double>();
metrics.put("Score", currentGame.getScore());
metrics.put("Opponents", currentGame.getOpponentCount());

telemetry.trackEvent("WinGame", properties, metrics);
```

> [!NOTE]
> Var noga så att inte logga personligt identifierbar information i egenskaperna.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativt sätt att ange egenskaper och mått

Om det är enklare, kan du samla in parametrarna för en händelse i ett separat objekt:

```csharp
var event = new EventTelemetry();

event.Name = "WinGame";
event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
event.Properties["game"] = currentGame.Name;
event.Properties["difficulty"] = currentGame.Difficulty;
event.Metrics["Score"] = currentGame.Score;
event.Metrics["Opponents"] = currentGame.Opponents.Length;

telemetry.TrackEvent(event);
```

> [!WARNING]
> Inte återanvända samma telemetri objekt instans (`event` i det här exemplet) att anropa Track*() flera gånger. Detta kan orsaka telemetri skickas med felaktig konfiguration.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Anpassade mått och egenskaper i Analytics

I [Analytics](analytics.md), anpassade mått och egenskaper som visas i den `customMeasurements` och `customDimensions` attribut för varje post i telemetri.

Till exempel om du har lagt till en egenskap med namnet ”spel” till telemetrin begäran kan den här frågan antal förekomster av olika värden för ”spel” och visar medelvärdet av din anpassade mått ”poäng”:

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Observera att:

* När du extrahera ett värde från customDimensions eller customMeasurements JSON, den har dynamisk typ och så måste du skicka den `tostring` eller `todouble`.
* Med hänsyn till möjligheten att [sampling](../../azure-monitor/app/sampling.md), bör du använda `sum(itemCount)`, inte `count()`.

## <a name="timed"></a> Tidsinställning händelser

Ibland vill du att skapa diagram över hur lång tid det tar för att utföra en åtgärd. Du kanske exempelvis vill veta hur lång tid användare gör att överväga val i ett spel. Du kan använda parametern mätning för detta.

*C#*

```csharp
var stopwatch = System.Diagnostics.Stopwatch.StartNew();

// ... perform the timed action ...

stopwatch.Stop();

var metrics = new Dictionary <string, double>
    {{"processingTime", stopwatch.Elapsed.TotalMilliseconds}};

// Set up some properties:
var properties = new Dictionary <string, string>
    {{"signalSource", currentSignalSource.Name}};

// Send the event:
telemetry.TrackEvent("SignalProcessed", properties, metrics);
```

*Java*

```java
long startTime = System.currentTimeMillis();

// Perform timed action

long endTime = System.currentTimeMillis();
Map<String, Double> metrics = new HashMap<>();
metrics.put("ProcessingTime", endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="defaults"></a>Standardegenskaperna för anpassad telemetri

Om du vill ange standard egenskapsvärden för några av de anpassa händelser som du skriver kan du ange dem i en TelemetryClient-instans. De är kopplade till varje telemetriobjekt som skickas från klienten.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.Properties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.Properties("Game") = currentGame.Name
' Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame")
```

*Java*

```java
import com.microsoft.applicationinsights.TelemetryClient;
import com.microsoft.applicationinsights.TelemetryContext;
...


TelemetryClient gameTelemetry = new TelemetryClient();
TelemetryContext context = gameTelemetry.getContext();
context.getProperties().put("Game", currentGame.Name);

gameTelemetry.TrackEvent("WinGame");
```

*Node.js*

```javascript
var gameTelemetry = new applicationInsights.TelemetryClient();
gameTelemetry.commonProperties["Game"] = currentGame.Name;

gameTelemetry.TrackEvent({name: "WinGame"});
```

Enskilda telemetri-anrop kan åsidosätta standardvärdena i sina ordböcker för egenskapen.

*För JavaScript webbklienter*, [använda JavaScript telemetri-initierare](#js-initializer).

*Att lägga till egenskaper i all telemetri*, inklusive data från standard samling moduler [implementera `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Sampling, filtrering och bearbetning av telemetri

Du kan skriva kod för att bearbeta din telemetri innan den skickas från SDK. Bearbetningen innehåller data som skickas från standardmoduler-moduler, till exempel http-begäran samlingen och beroende samlingen.

[Lägg till egenskaper](../../azure-monitor/app/api-filtering-sampling.md#add-properties) till telemetri genom att implementera `ITelemetryInitializer`. Du kan till exempel lägga till versionsnummer eller värden som beräknats från övriga egenskaper.

[Filtrering](../../azure-monitor/app/api-filtering-sampling.md#filtering) kan ändra eller ta bort telemetri innan det skickas från SDK genom att implementera `ITelemetryProcesor`. Du kan styra vad skickas eller tas bort, men du behöver för effekten på dina mått. Beroende på hur du ta bort objekt, kan du förlora möjligheten att navigera mellan relaterade objekt.

[Sampling](../../azure-monitor/app/api-filtering-sampling.md) är en lösning som är paketerade för att minska mängden data som skickas från din app på portalen. Detta sker utan att påverka mätvärdena som visas. Och detta sker utan att påverka din möjlighet att diagnostisera problem genom att navigera mellan relaterade objekt, till exempel undantag, begäranden och sidvyer.

[Läs mer](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Inaktivera telemetri

Att *dynamiskt stoppa och starta* insamling och överföring av telemetri:

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Att *inaktivera valda standard insamlare*– till exempel prestandaräknare, HTTP-begäranden eller beroenden – ta bort eller kommentera ut de relevanta raderna i [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Du kan göra detta, till exempel om du vill skicka dina egna TrackRequest data.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Att *inaktivera valda standard insamlare*– till exempel prestandaräknare, HTTP-begäranden eller beroenden--vid initiering, kedja konfigurationsmetoder i SDK initieringen koden:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Använd konfigurationsobjektet för att inaktivera dessa po inicializaci: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Utvecklarläget

Under felsökning, är det praktiskt att ha telemetrin expedierade genom pipelinen så att du kan se resultatet direkt. Du kan också hämta aktiveringshanteraren som hjälper dig att spåra eventuella problem med telemetri. Stänga av den i produktion, eftersom det kan sakta ned din app.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

## <a name="ikey"></a> Ange instrumenteringsnyckeln för valda anpassad telemetri

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-ikey"></a> Dynamisk instrumenteringsnyckeln

Om du vill undvika att blanda in telemetri från utveckling, testning och produktionsmiljöer, kan du [skapa separata Application Insights-resurser](../../application-insights/app-insights-create-new-resource.md) och ändrar sina nycklar, beroende på miljön.

Du kan ange den i din kod i stället för att hämta instrumenteringsnyckeln från konfigurationsfilen. Ange nyckeln i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

*C#*

```csharp
protected void Application_Start()
{
    Microsoft.ApplicationInsights.Extensibility.
    TelemetryConfiguration.Active.InstrumentationKey =
        // - for example -
        WebConfigurationManager.Settings["ikey"];
    ...
}
```

*JavaScript*

```javascript
appInsights.config.instrumentationKey = myKey;
```

På webbsidor, kanske du vill ställa in den från webbserverns tillstånd, i stället kodning bokstavligen till skriptet. Till exempel i en webbsida som genererats i en ASP.NET-app:

*JavaScript i Razor*

```cshtml
<script type="text/javascript">
// Standard Application Insights webpage script:
var appInsights = window.appInsights || function(config){ ...
// Modify this part:
}({instrumentationKey:  
    // Generate from server property:
    @Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey;
}) // ...
```

## <a name="telemetrycontext"></a>TelemetryContext

TelemetryClient har en kontextegenskap som innehåller värden som ska skickas tillsammans med alla telemetridata. De normalt har angetts av standardmoduler-moduler, men du kan också ange dem själv. Exempel:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Om du ställer in några av värdena själv, Överväg att ta bort den aktuella raden från [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md), så att dina värden och värden som standard inte bli förvirrad.

* **Komponenten**: Appen och dess version.
* **Enheten**: Data om den enhet där appen körs. (I web apps, detta är den server eller en klientenhet som telemetri som skickas från.)
* **InstrumentationKey**: Application Insights-resurs i Azure var telemetrin visas. Den har vanligtvis hämtats från ApplicationInsights.config.
* **Plats**: Enhetens geografiska plats.
* **Åtgärden**: I web apps, den aktuella HTTP-begäran. I andra typer av appar, kan du ange Gruppera händelser tillsammans.
  * **ID**: Ett genererat värde kopplat till olika händelser, så att när du undersöker en händelse i Diagnostiksökning kan du hitta relaterade objekt.
  * **Namn**: En identifierare vanligtvis URL: en för HTTP-begäran.
  * **SyntheticSource**: Om inte null eller tom, en sträng som anger att källan för begäran har identifierats som en robot eller testa. Som standard är det inte beräkningar i Metrics Explorer.
* **Egenskaper för**: Egenskaper som skickas med alla telemetridata. Den kan åsidosättas i enskilda spåra * anrop.
* **Sessionen**: Användarens session. ID har angetts ett genererat värde som ändras när användaren inte har varit aktiva under en tid.
* **Användaren**: Användarinformation.

## <a name="limits"></a>Begränsningar

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Undvik att träffa gräns för överföringshastigheten i data, använda [sampling](../../azure-monitor/app/sampling.md).

För att bestämma hur länge data bevaras, se [kvarhållning av Data och sekretess](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Referensdokument

* [ASP.NET-referens](https://msdn.microsoft.com/library/dn817570.aspx)
* [Java-Referens](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript-referens](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK-koden

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-paket](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Alla plattformar](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Frågor

* *Vilka undantag kan generera med Track_() anrop?*

    Ingen. Du behöver inte lägga in dem i trycatch-satser. Om SDK påträffar problem, loggas meddelanden i debug konsolens utdata och--om meddelanden får via--i Diagnostiksökning.
* *Finns det en REST-API för att hämta data från portalen?*

    Ja, den [dataåtkomst API](https://dev.applicationinsights.io/). Du kan extrahera data att [exportera från analys till Power BI](../../application-insights/app-insights-export-power-bi.md) och [löpande export](../../azure-monitor/app/export-telemetry.md).

## <a name="next"></a>Nästa steg

* [Sökhändelser och loggar](../../azure-monitor/app/diagnostic-search.md)
* [Felsökning](../../azure-monitor/app/troubleshoot-faq.md)

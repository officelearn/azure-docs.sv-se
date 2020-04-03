---
title: API för programinsikter för anpassade händelser och mått | Microsoft-dokument
description: Infoga några rader kod i enheten eller skrivbordsappen, webbsidan eller tjänsten för att spåra användning och diagnostisera problem.
ms.topic: conceptual
ms.date: 03/27/2019
ms.openlocfilehash: 06bd8bd0958afd26e1256a010b08c908c59aaf7d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585868"
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>API för Application Insights för anpassade händelser och mått

Infoga några rader kod i ditt program för att ta reda på vad användarna gör med det, eller för att diagnostisera problem. Du kan skicka telemetri från enhets- och skrivbordsappar, webbklienter och webbservrar. Använd [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) grundläggande telemetri-API för att skicka anpassade händelser och mått och dina egna versioner av standardtelemetri. Detta API är samma API som standard Application Insights datainsamlare använder.

## <a name="api-summary"></a>API-sammanfattning

Kärnan API är enhetlig över alla plattformar, `GetMetric`bortsett från några varianter som (.NET bara).

| Metod | Används för |
| --- | --- |
| [`TrackPageView`](#page-views) |Sidor, skärmar, blad eller formulär. |
| [`TrackEvent`](#trackevent) |Användaråtgärder och andra händelser. Används för att spåra användarbeteende eller för att övervaka prestanda. |
| [`GetMetric`](#getmetric) |Noll och flerdimensionella mått, centralt konfigurerad aggregering, endast C#. |
| [`TrackMetric`](#trackmetric) |Prestandamätningar, till exempel kölängder som inte är relaterade till specifika händelser. |
| [`TrackException`](#trackexception) |Logga undantag för diagnos. Spåra var de förekommer i förhållande till andra händelser och undersöka stackspår. |
| [`TrackRequest`](#trackrequest) |Logga frekvensen och varaktigheten för serverbegäranden för prestandaanalys. |
| [`TrackTrace`](#tracktrace) |Resursdiagnostikloggmeddelanden. Du kan också fånga loggar från tredje part. |
| [`TrackDependency`](#trackdependency) |Logga varaktigheten och frekvensen för samtal till externa komponenter som appen är beroende av. |

Du kan [koppla egenskaper och mått](#properties) till de flesta av dessa telemetrianrop.

## <a name="before-you-start"></a><a name="prep"></a>Innan du börjar

Om du inte har någon referens på Application Insights SDK ännu:

* Lägg till SDK för programinsikter i projektet:

  * [ASP.NET projekt](../../azure-monitor/app/asp-net.md)
  * [ASP.NET Kärnprojekt](../../azure-monitor/app/asp-net-core.md)
  * [Java-projekt](../../azure-monitor/app/java-get-started.md)
  * [Node.js-projekt](../../azure-monitor/app/nodejs.md)
  * [JavaScript på varje webbsida](../../azure-monitor/app/javascript.md) 
* I enhetens eller webbserverkoden inkluderar du:

    *C#:*`using Microsoft.ApplicationInsights;`

    *Visual Basic:*`Imports Microsoft.ApplicationInsights`

    *Java:*`import com.microsoft.applicationinsights.TelemetryClient;`

    *Nod.js:*`var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Hämta en TelemetryClient-instans

Hämta en `TelemetryClient` instans av (utom i JavaScript på webbsidor):

För [ASP.NET Core-appar](asp-net-core.md#how-can-i-track-telemetry-thats-not-automatically-collected) och [icke HTTP/Worker för .NET/.NET Core-appar](worker-service.md#how-can-i-track-telemetry-thats-not-automatically-collected) rekommenderar `TelemetryClient` vi att du hämtar en instans av från behållaren för beroendeinjektion enligt beskrivningen i respektive dokumentation.

Om du använder AzureFunctions v2+ eller Azure WebJobs v3+ – följer du det här dokumentet:https://docs.microsoft.com/azure/azure-functions/functions-monitoring#version-2x-and-higher

*C#*

```csharp
private TelemetryClient telemetry = new TelemetryClient();
```
För alla som ser denna metod är föråldrade meddelanden besök [microsoft / ApplicationInsights-dotnet # 1152](https://github.com/microsoft/ApplicationInsights-dotnet/issues/1152) för ytterligare information.

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

TelemetriClient är trådsäker.

För ASP.NET- och Java-projekt fångas inkommande HTTP-begäranden automatiskt in. Du kanske vill skapa ytterligare instanser av TelemetryClient för andra modul i appen. Du kan till exempel ha en TelemetryClient-instans i medelklassen för att rapportera affärslogikhändelser. Du kan ange egenskaper som UserId och DeviceId för att identifiera datorn. Den här informationen är kopplad till alla händelser som instansen skickar.

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

I Node.js-projekt kan `new applicationInsights.TelemetryClient(instrumentationKey?)` du använda för att skapa en ny instans, men detta `defaultClient`rekommenderas endast för scenarier som kräver isolerad konfiguration från singleton .

## <a name="trackevent"></a>TrackEvent

I Application Insights är en *anpassad händelse* en datapunkt som du kan visa i [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md) som ett aggregerat antal och i [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md) som enskilda förekomster. (Det är inte relaterat till MVC eller andra ramverk "händelser.")

Infoga `TrackEvent` samtal i koden för att räkna olika händelser. Hur ofta användare väljer en viss funktion, hur ofta de uppnår vissa mål, eller kanske hur ofta de gör vissa typer av misstag.

I en spelapp skickar du till exempel en händelse när en användare vinner spelet:

*JavaScript*

```javascript
appInsights.trackEvent({name:"WinGame"});
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

Telemetrin `customEvents` är tillgänglig i tabellen i [Application Insights Analytics](analytics.md). Varje rad representerar `trackEvent(..)` ett samtal som ska visas i appen.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift visar egenskapen itemCount ett värde som är större än 1. ItemCount==10 innebär till exempel att av 10 anrop till trackEvent() skickade samplingsprocessen bara en av dem. För att få rätt antal anpassade händelser bör `customEvents | summarize sum(itemCount)`du därför använda kod som .

## <a name="getmetric"></a>GetMetric

### <a name="examples"></a>Exempel

*C#*

```csharp
namespace User.Namespace.Example01
{
    using System;
    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.DataContracts;

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
            // We use a common .NET pattern: TryXxx(..) to make sure that the limits are observed.
            // If the limits are already reached, Metric.TrackValue(..) will return False and the value will not be tracked. Otherwise it will return True.
            // This is particularly useful if the data for a metric originates from user input, e.g. a file:

            Tuple<int, string> countAndSpecies = ReadSpeciesFromUserInput();
            int count = countAndSpecies.Item1;
            string species = countAndSpecies.Item2;

            if (!animalsSold.TrackValue(count, species))

            {
                client.TrackTrace($"Data series or dimension cap was reached for metric {animalsSold.Identifier.MetricId}.", SeverityLevel.Error);
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

## <a name="trackmetric"></a>TrackMetric (Spårmetri)

> [!NOTE]
> Microsoft.ApplicationInsights.TelemetryClient.TrackMetric är inte den metod som föredras för att skicka mått. Mått bör alltid pre-aggregeras över en tidsperiod innan de skickas. Använd en av GetMetric(..) överbelastningar för att hämta ett måttobjekt för åtkomst till SDK-pre-aggregeringsfunktioner. Om du implementerar din egen pre-aggregeringslogik kan du använda metoden TrackMetric() för att skicka de resulterande aggregaten. Om ditt program kräver att du skickar ett separat telemetriobjekt vid varje tillfälle utan aggregering över tid, har du förmodligen ett användningsfall för händelsetelemetri. se TelemetryClient.TrackEvent (Microsoft.ApplicationInsights.DataContracts.EventTelemetry).

Application Insights kan kartlägga mått som inte är kopplade till vissa händelser. Du kan till exempel övervaka en kölängd med jämna mellanrum. Med mått är de enskilda mätningarna av mindre intresse än variationer och trender, och därför är statistiska diagram användbara.

För att skicka mått till Application Insights `TrackMetric(..)` kan du använda API:et. Det finns två sätt att skicka ett mått:

* Ett enda värde. Varje gång du utför ett mått i ditt program skickar du motsvarande värde till Application Insights. Anta till exempel att du har ett mått som beskriver antalet objekt i en behållare. Under en viss tidsperiod placerar du först tre objekt i behållaren och tar sedan bort två objekt. Därför skulle du `TrackMetric` ringa två gånger: `3` först passerar `-2`värdet och sedan värdet . Application Insights lagrar båda värdena för din räkning.

* Aggregation. När du arbetar med mått är varje enskild mätning sällan av intresse. I stället är det viktigt med en sammanfattning av vad som hände under en viss tidsperiod. En sådan sammanfattning kallas _aggregering_. I exemplet ovan är `1` den sammanlagda måttsumman för den tidsperioden och antalet måttvärden är `2`. När du använder aggregeringsmetoden `TrackMetric` anropar du bara en gång per tidsperiod och skickar aggregerade värden. Detta är den rekommenderade metoden eftersom det avsevärt kan minska kostnaderna och prestandaomkostnaderna genom att skicka färre datapunkter till Application Insights, samtidigt som all relevant information samlas in.

### <a name="examples"></a>Exempel

#### <a name="single-values"></a>Enskilda värden

Så här skickar du ett enda måttvärde:

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

Telemetrin `customMetrics` är tillgänglig i tabellen i [Application Insights Analytics](analytics.md). Varje rad representerar `trackMetric(..)` ett samtal som ska visas i appen.

* `valueSum`- Det här är summan av mätningarna. För att få medelvärdet, dividera med `valueCount`.
* `valueCount`- Antalet mätningar som aggregerades i det här `trackMetric(..)` anropet.

## <a name="page-views"></a>Sidvisningar

I en enhets- eller webbsida-app skickas telemetri för sidvisning som standard när varje skärm eller sida läses in. Men du kan ändra det för att spåra sidvisningar vid ytterligare eller olika tidpunkter. I en app som visar flikar eller blad kanske du till exempel vill spåra en sida när användaren öppnar ett nytt blad.

Användar- och sessionsdata skickas som egenskaper tillsammans med sidvisningar, så att användar- och sessionsdiagrammen vaknar till liv när det finns sidvisningstelemetri.

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

Om du har flera flikar på olika HTML-sidor kan du också ange webbadressen:

```javascript
appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");
```

### <a name="timing-page-views"></a>Tidstabellsvyer

Som standard mäts de tider som rapporteras som **läsvyinläsningstid** från när webbläsaren skickar begäran tills webbläsarens sidinläsningshändelse anropas.

I stället kan du antingen:

* Ange en explicit varaktighet i [trackPageView-samtalet:](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/API.md#trackpageview) `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Använd tidssamtalen `startTrackPage` `stopTrackPage`för sidvyn och .

*JavaScript*

```javascript
// To start timing a page:
appInsights.startTrackPage("Page1");

...

// To stop timing and log the page:
appInsights.stopTrackPage("Page1", url, properties, measurements);
```

Namnet som du använder som första parameter associerar start- och stoppanrop. Det är standard till det aktuella sidnamnet.

Den resulterande sidans inläsningstider som visas i Metrics Explorer härleds från intervallet mellan start- och stoppsamtalen. Det är upp till dig vilket intervall du faktiskt tid.

### <a name="page-telemetry-in-analytics"></a>Sidelemetri i Analytics

I [Analytics](analytics.md) visar två tabeller data från webbläsaråtgärder:

* Tabellen `pageViews` innehåller data om URL:en och sidrubriken
* Tabellen `browserTimings` innehåller data om klientprestanda, till exempel hur tid det tar att bearbeta inkommande data

Så här tar det för dig hur lång tid det tar att bearbeta olika sidor:

```kusto
browserTimings
| summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name
```

För att upptäcka populariteten av olika webbläsare:

```kusto
pageViews
| summarize count() by client_Browser
```

Om du vill koppla sidvisningar till AJAX-samtal ansluter du till beroenden:

```kusto
pageViews
| join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>SpårRequest

Servern SDK använder TrackRequest för att logga HTTP-begäranden.

Du kan också anropa det själv om du vill simulera begäranden i ett sammanhang där du inte har webbtjänstmodulen igång.

Det rekommenderade sättet att skicka begäran telemetri är dock där begäran fungerar som en <a href="#operation-context">åtgärd kontext</a>.

## <a name="operation-context"></a>Åtgärdskontext

Du kan korrelera telemetriobjekt tillsammans genom att associera dem med operationskontext. Standardmodulen för spårning av begäranr gör detta för undantag och andra händelser som skickas medan en HTTP-begäran bearbetas. I [Sök](../../azure-monitor/app/diagnostic-search.md) och [Analys](analytics.md)kan du enkelt hitta alla händelser som är associerade med begäran med hjälp av dess åtgärds-ID.

Mer information om korrelation [finns i Telemetrikorrelation i Application Insights.](../../azure-monitor/app/correlation.md)

När du spårar telemetri manuellt, det enklaste sättet att säkerställa telemetrikorrelation med hjälp av detta mönster:

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

Tillsammans med att ange `StartOperation` en operationskontext skapar du ett telemetriobjekt av den typ som du anger. Den skickar telemetriobjektet när du gör dig av `StopOperation`med åtgärden, eller om du uttryckligen anropar . Om du `RequestTelemetry` använder som telemetrityp anges dess varaktighet till det tidsblandade intervallet mellan start och stopp.

Telemetriartiklar som rapporteras inom en verksamhetsområde blir "underordnade" av en sådan operation. Åtgärdskontexter kan kapslas.

I Sök används operationskontexten för att skapa listan **Relaterade objekt:**

![Relaterade poster](./media/api-custom-events-metrics/21.png)

Mer information om anpassad verksamhet för anpassad verksamhet [med Application Insights .NET SDK finns i Spåra anpassade åtgärder med Application Insights .NET SDK.](../../azure-monitor/app/custom-operations-tracking.md)

### <a name="requests-in-analytics"></a>Begäranden i Analytics

I [Application Insights Analytics](analytics.md)visas `requests` begäranden i tabellen.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift visar egenskapen itemCount ett värde som är större än 1. ItemCount==10 innebär till exempel att av 10 anrop till trackRequest() skickade samplingsprocessen bara en av dem. Om du vill få rätt antal begäranden och genomsnittlig varaktighet segmenterad av begäransnamn använder du kod som:

```kusto
requests
| summarize count = sum(itemCount), avgduration = avg(duration) by name
```

## <a name="trackexception"></a>TrackException

Skicka undantag till Application Insights:

* För att [räkna dem](../../azure-monitor/app/metrics-explorer.md), som en indikation på frekvensen av ett problem.
* Att [undersöka enskilda händelser](../../azure-monitor/app/diagnostic-search.md).

Rapporterna innehåller stackspårningarna.

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

SDK:erna fångar många undantag automatiskt, så du behöver inte alltid anropa TrackException explicit.

* ASP.NET: Skriv [kod för att fånga undantag](../../azure-monitor/app/asp-net-exceptions.md).
* Java EE: [Undantag fångas automatiskt](../../azure-monitor/app/java-get-started.md#exceptions-and-request-failures).
* JavaScript: Undantag fångas automatiskt. Om du vill inaktivera automatisk samling lägger du till en rad i kodavsnittet som du infogar på webbsidorna:

```javascript
({
    instrumentationKey: "your key",
    disableExceptionTracking: true
})
```

### <a name="exceptions-in-analytics"></a>Undantag i Analytics

I [Application Insights Analytics](analytics.md)visas `exceptions` undantag i tabellen.

Om [sampling](../../azure-monitor/app/sampling.md) är i `itemCount` drift visar egenskapen ett värde som är större än 1. ItemCount==10 innebär till exempel att av 10 anrop till trackException() skickade samplingsprocessen bara en av dem. Om du vill få ett korrekt antal undantag som segmenteras efter typ av undantag använder du kod som:

```kusto
exceptions
| summarize sum(itemCount) by type
```

De flesta av de viktiga stack information är redan extraheras `details` i separata variabler, men du kan dra isär strukturen för att få mer. Eftersom den här strukturen är dynamisk bör du casta resultatet till den typ du förväntar dig. Ett exempel:

```kusto
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Om du vill associera undantag med deras relaterade begäranden använder du en koppling:

```kusto
exceptions
| join (requests) on operation_Id
```

## <a name="tracktrace"></a>TrackTrace (spårTrace)

Använd TrackTrace för att diagnostisera problem genom att skicka ett "sökvägar" till Application Insights. Du kan skicka bitar av diagnostikdata och inspektera dem i [Diagnostiksökning](../../azure-monitor/app/diagnostic-search.md).

I [.NET-loggkort](../../azure-monitor/app/asp-net-trace-logs.md) använder du det här API:et för att skicka loggar från tredje part till portalen.

I Java för [Standard loggers som Log4J, Logback](../../azure-monitor/app/java-trace-logs.md) använda Application Insights Log4j eller Logback Appenders att skicka tredje part loggar till portalen.

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

*JavaScript på klient-/webbläsarsidan*

```javascript
trackTrace(message: string, properties?: {[string]:string}, severityLevel?: SeverityLevel)
```

Logga en diagnostikhändelse som att ange eller lämna en metod.

 Parameter | Beskrivning
---|---
`message` | Diagnostiska data. Kan vara mycket längre än ett namn.
`properties` | Karta över sträng till sträng: Ytterligare data som används för att [filtrera undantag](https://azure.microsoft.com/documentation/articles/app-insights-api-custom-events-metrics/#properties) i portalen. Standarderna är tomma.
`severityLevel` | Värden som stöds: [SeverityLevel.ts](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/shared/AppInsightsCommon/src/Interfaces/Contracts/Generated/SeverityLevel.ts)

Du kan söka efter meddelandeinnehåll, men (till skillnad från egenskapsvärden) kan du inte filtrera på det.

Storleksgränsen `message` för är mycket högre än gränsen för egenskaper.
En fördel med TrackTrace är att du kan lägga relativt långa data i meddelandet. Du kan till exempel koda POST-data där.  

Dessutom kan du lägga till en allvarlighetsgrad i meddelandet. Och precis som annan telemetri kan du lägga till egenskapsvärden som hjälper dig att filtrera eller söka efter olika uppsättningar spårningar. Ett exempel:

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

I [Sök](../../azure-monitor/app/diagnostic-search.md)kan du sedan enkelt filtrera bort alla meddelanden på en viss allvarlighetsgrad som relaterar till en viss databas.

### <a name="traces-in-analytics"></a>Spår i Analytics

I [Application Insights Analytics](analytics.md)visas samtal till `traces` TrackTrace i tabellen.

Om [sampling](../../azure-monitor/app/sampling.md) är i drift visar egenskapen itemCount ett värde som är större än 1. ItemCount==10 innebär till exempel att av `trackTrace()`10 anrop till överförde samplingsprocessen bara en av dem. För att få ett korrekt antal spårningsanrop `traces | summarize sum(itemCount)`bör du därför använda kod som .

## <a name="trackdependency"></a>TrackDependency

Använd TrackDependency-anropet för att spåra svarstider och framgångsfrekvenser för anrop till en extern kod. Resultaten visas i beroendediagrammen i portalen. Kodavsnittet nedan måste läggas till var helst ett beroendeanrop görs.

*C#*

```csharp
var success = false;
var startTime = DateTime.UtcNow;
var timer = System.Diagnostics.Stopwatch.StartNew();
try
{
    success = dependency.Call();
}
catch(Exception ex) 
{
    success = false;
    telemetry.TrackException(ex);
    throw new Exception("Operation went wrong", ex);
}
finally
{
    timer.Stop();
    telemetry.TrackDependency("DependencyType", "myDependency", "myCall", startTime, timer.Elapsed, success);
}
```

*Java*

```java
boolean success = false;
Instant startTime = Instant.now();
try {
    success = dependency.call();
}
finally {
    Instant endTime = Instant.now();
    Duration delta = Duration.between(startTime, endTime);
    RemoteDependencyTelemetry dependencyTelemetry = new RemoteDependencyTelemetry("My Dependency", "myCall", delta, success);
    RemoteDependencyTelemetry.setTimeStamp(startTime);
    RemoteDependencyTelemetry.trackDependency(dependencyTelemetry);
}
```

*Node.js*

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

Kom ihåg att serverns SDK:er innehåller en [beroendemodul](../../azure-monitor/app/asp-net-dependencies.md) som identifierar och spårar vissa beroendeanrop automatiskt – till exempel till databaser och REST-API:er. Du måste installera en agent på servern för att få modulen att fungera. 

I Java kan vissa beroendeanrop spåras automatiskt med [Java-agenten](../../azure-monitor/app/java-agent.md).

Du använder det här samtalet om du vill spåra samtal som den automatiska spårningen inte fångar, eller om du inte vill installera agenten.

Om du vill inaktivera standardmodulen för beroendespårning i C#redigerar du [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) och tar bort referensen till `DependencyCollector.DependencyTrackingTelemetryModule`. I Java ska du inte installera java-agenten om du inte vill samla in standardberoenden automatiskt.

### <a name="dependencies-in-analytics"></a>Beroenden i Analytics

I [Application Insights Analytics](analytics.md)visas trackDependency-anrop i tabellen. `dependencies`

Om [sampling](../../azure-monitor/app/sampling.md) är i drift visar egenskapen itemCount ett värde som är större än 1. ItemCount==10 innebär till exempel att av 10 anrop till trackDependency() skickade samplingsprocessen bara en av dem. Om du vill få ett korrekt antal beroenden som segmenteras efter målkomponent använder du kod som:

```kusto
dependencies
| summarize sum(itemCount) by target
```

Om du vill associera beroenden med deras relaterade begäranden använder du en koppling:

```kusto
dependencies
| join (requests) on operation_Id
```

## <a name="flushing-data"></a>Tömning av data

Normalt skickar SDK data med fasta intervall (vanligtvis 30 sekunder) eller när bufferten är full (vanligtvis 500 objekt). I vissa fall kanske du vill rensa bufferten – till exempel om du använder SDK i ett program som stängs av.

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

Funktionen är asynkron för [serverns telemetrikanal](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Helst bör metoden flush() användas i programmets avstängningsaktivitet.

## <a name="authenticated-users"></a>Autentiserade användare

I en webbapp identifieras användare (som standard) av cookies. En användare kan räknas mer än en gång om de kommer åt din app från en annan dator eller webbläsare, eller om de tar bort cookies.

Om användarna loggar in på din app kan du få en mer exakt räkning genom att ange det autentiserade användar-ID:t i webbläsarkoden:

*JavaScript*

```javascript
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

I ett ASP.NET webb MVC-program, till exempel:

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

Det är inte nödvändigt att använda användarens faktiska inloggningsnamn. Det behöver bara vara ett ID som är unikt för den användaren. Det får inte innehålla blanksteg `,;=|`eller något av tecknen .

Användar-ID:et anges också i en sessionscookie och skickas till servern. Om servern SDK installeras skickas det autentiserade användar-ID:t som en del av kontextegenskaperna för både klient- och servertelemetri. Du kan sedan filtrera och söka på den.

Om din app grupperar användare till konton kan du också skicka en identifierare för kontot (med samma teckenbegränsningar).

```javascript
appInsights.setAuthenticatedUserContext(validatedId, accountId);
```

I [Metrics Explorer](../../azure-monitor/app/metrics-explorer.md)kan du skapa ett diagram som räknar **användare, autentiserade**och **användarkonton**.

Du kan också [söka efter](../../azure-monitor/app/diagnostic-search.md) klientdatapunkter med specifika användarnamn och konton.

## <a name="filtering-searching-and-segmenting-your-data-by-using-properties"></a><a name="properties"></a>Filtrera, söka efter och segmentera data med hjälp av egenskaper

Du kan koppla egenskaper och mått till dina händelser (och även till mått, sidvisningar, undantag och andra telemetridata).

*Egenskaper* är strängvärden som du kan använda för att filtrera telemetrin i användningsrapporterna. Om din app till exempel innehåller flera spel kan du bifoga namnet på spelet till varje händelse så att du kan se vilka spel som är populärare.

Det finns en gräns på 8192 på stränglängden. (Om du vill skicka stora datasegment använder du meddelandeparametern trackTrace.)

*Mått* är numeriska värden som kan presenteras grafiskt. Du kanske till exempel vill se om det sker en gradvis ökning av poängen som dina spelare uppnår. Diagrammen kan segmenteras efter de egenskaper som skickas med händelsen, så att du kan få separata eller staplade grafer för olika spel.

För att måttvärden ska visas korrekt bör de vara större än eller lika med 0.

Det finns vissa [begränsningar för antalet egenskaper, egenskapsvärden och mått](#limits) som du kan använda.

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
> Var noga med att inte logga personligt identifierbar information i fastigheter.
>
>

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativt sätt att ange egenskaper och mått

Om det är bekvämare kan du samla in parametrarna för en händelse i ett separat objekt:

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
> Återanvänd inte samma telemetriobjektinstans`event` (i det här exemplet) för att anropa Track*() flera gånger. Detta kan leda till att telemetri skickas med felaktig konfiguration.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Anpassade mått och egenskaper i Analytics

I [Analytics](analytics.md)visas anpassade mått och `customMeasurements` `customDimensions` egenskaper i attributen för varje telemetripost.

Om du till exempel har lagt till en egenskap med namnet "spel" i din begäran telemetri, räknar den här frågan förekomster av olika värden för "spel", och visar genomsnittet av det anpassade måttet "poäng":

```kusto
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game)
```

Observera att:

* När du extraherar ett värde från customDimensions eller customMeasurements JSON har `tostring` det `todouble`dynamisk typ, och därför måste du casta det eller .
* För att ta hänsyn till möjligheten `sum(itemCount)`till `count()` [provtagning](../../azure-monitor/app/sampling.md)bör du använda , inte .

## <a name="timing-events"></a><a name="timed"></a>Tidshändelser

Ibland vill du kartlägga hur lång tid det tar att utföra en åtgärd. Du kanske till exempel vill veta hur lång tid det tar för användarna att överväga val i ett spel. Du kan använda mätparametern för detta.

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
metrics.put("ProcessingTime", (double)endTime-startTime);

// Setup some properties
Map<String, String> properties = new HashMap<>();
properties.put("signalSource", currentSignalSource.getName());

// Send the event
telemetry.trackEvent("SignalProcessed", properties, metrics);
```

## <a name="default-properties-for-custom-telemetry"></a><a name="defaults"></a>Standardegenskaper för anpassad telemetri

Om du vill ange standardegenskapsvärden för några av de anpassade händelser som du skriver kan du ange dem i en TelemetryClient-instans. De är kopplade till varje telemetriobjekt som skickas från klienten.

*C#*

```csharp
using Microsoft.ApplicationInsights.DataContracts;

var gameTelemetry = new TelemetryClient();
gameTelemetry.Context.GlobalProperties["Game"] = currentGame.Name;
// Now all telemetry will automatically be sent with the context property:
gameTelemetry.TrackEvent("WinGame");
```

*Visual Basic*

```vb
Dim gameTelemetry = New TelemetryClient()
gameTelemetry.Context.GlobalProperties("Game") = currentGame.Name
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

Enskilda telemetrianrop kan åsidosätta standardvärdena i egenskapsordlistorna.

*För JavaScript-webbklienter*använder du JavaScript-telemetriinitierare.

*Om du vill lägga till egenskaper i all telemetri*, inklusive data från standardinsamlingsmoduler, [implementerar `ITelemetryInitializer` ](../../azure-monitor/app/api-filtering-sampling.md#add-properties)du .

## <a name="sampling-filtering-and-processing-telemetry"></a>Kompning, filtrering och bearbetning av telemetri

Du kan skriva kod för att bearbeta din telemetri innan den skickas från SDK. Bearbetningen innehåller data som skickas från standardmodulerna för telemetri, till exempel HTTP-begärandesamling och beroendeinsamling.

[Lägg till egenskaper](../../azure-monitor/app/api-filtering-sampling.md#add-properties) i telemetri genom att `ITelemetryInitializer`implementera . Du kan till exempel lägga till versionsnummer eller värden som beräknas utifrån andra egenskaper.

[Filtrering](../../azure-monitor/app/api-filtering-sampling.md#filtering) kan ändra eller ignorera telemetri innan den skickas från `ITelemetryProcessor`SDK genom att implementera . Du styr vad som skickas eller ignoreras, men du måste ta hänsyn till effekten på dina mått. Beroende på hur du tar bort objekt kan du förlora möjligheten att navigera mellan relaterade objekt.

[Sampling](../../azure-monitor/app/api-filtering-sampling.md) är en paketerad lösning för att minska mängden data som skickas från din app till portalen. Det gör det utan att påverka de visade måtten. Och det gör det utan att påverka din förmåga att diagnostisera problem genom att navigera mellan relaterade objekt som undantag, förfrågningar och sidvisningar.

[Läs mer](../../azure-monitor/app/api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Inaktivera telemetri

Så här stoppar och startar du insamling och överföring av telemetri *dynamiskt:*

*C#*

```csharp
using  Microsoft.ApplicationInsights.Extensibility;

TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```java
telemetry.getConfiguration().setTrackingDisabled(true);
```

Så här inaktiverar du *valda standardinsamlare*– till exempel prestandaräknare, HTTP-begäranden eller beroenden – ta bort eller kommenterar de relevanta raderna i [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md). Du kan till exempel göra detta om du vill skicka egna TrackRequest-data.

*Node.js*

```javascript
telemetry.config.disableAppInsights = true;
```

Om du vill *inaktivera valda standardinsamlare*– till exempel prestandaräknare, HTTP-begäranden eller beroenden – vid initieringstid, kedjar du konfigurationsmetoder till SDK-initieringskoden:

```javascript
applicationInsights.setup()
    .setAutoCollectRequests(false)
    .setAutoCollectPerformance(false)
    .setAutoCollectExceptions(false)
    .setAutoCollectDependencies(false)
    .setAutoCollectConsole(false)
    .start();
```

Om du vill inaktivera dessa samlare efter initieringen använder du konfigurationsobjektet:`applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="developer-mode"></a><a name="debug"></a>Utvecklarläge

Under felsökning är det användbart att ha din telemetri snabbare genom pipelinen så att du kan se resultat direkt. Du får också ytterligare meddelanden som hjälper dig att spåra eventuella problem med telemetrin. Stäng av den i produktionen eftersom den kan göra appen långsammare.

*C#*

```csharp
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;
```

*Visual Basic*

```vb
TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True
```

*Node.js*

För Node.js kan du aktivera utvecklarläge genom `setInternalLogging` att `maxBatchSize` aktivera intern loggning via och inställning till 0, vilket gör att din telemetri skickas så fort den samlas in.

```js
applicationInsights.setup("ikey")
  .setInternalLogging(true, true)
  .start()
applicationInsights.defaultClient.config.maxBatchSize = 0;
```

## <a name="setting-the-instrumentation-key-for-selected-custom-telemetry"></a><a name="ikey"></a>Ställa in instrumenteringsnyckeln för vald anpassad telemetri

*C#*

```csharp
var telemetry = new TelemetryClient();
telemetry.InstrumentationKey = "---my key---";
// ...
```

## <a name="dynamic-instrumentation-key"></a><a name="dynamic-ikey"></a>Dynamisk instrumenteringsnyckel

Om du vill undvika att blanda ihop telemetri från utvecklings-, test- och produktionsmiljöer kan du [skapa separata Application Insights-resurser](../../azure-monitor/app/create-new-resource.md ) och ändra deras nycklar, beroende på miljön.

I stället för att hämta instrumenteringsnyckeln från konfigurationsfilen kan du ange den i koden. Ange nyckeln i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

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

På webbsidor kanske du vill ställa in den från webbserverns tillstånd, i stället för att koda den bokstavligen i skriptet. Till exempel på en webbsida som genereras i en ASP.NET app:

*JavaScript i Rakhyvel*

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

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="telemetrycontext"></a>TelemetriContext

TelemetriClient har egenskapen Context som innehåller värden som skickas tillsammans med alla telemetridata. De ställs normalt in av standardmodulerna för telemetri, men du kan också ställa in dem själv. Ett exempel:

```csharp
telemetry.Context.Operation.Name = "MyOperationName";
```

Om du själv anger något av dessa värden kan du överväga att ta bort den relevanta raden från [ApplicationInsights.config,](../../azure-monitor/app/configuration-with-applicationinsights-config.md)så att dina värden och standardvärdena inte blir förvirrade.

* **Komponent**: Appen och dess version.
* **Enhet**: Data om enheten där appen körs. (I webbappar är detta den server eller klientenhet som telemetrin skickas från.)
* **InstrumentationKey**: Application Insights-resursen i Azure där telemetrin visas. Det brukar plockas upp från ApplicationInsights.config.
* **Plats**: Enhetens geografiska läge.
* **Åtgärd**: I webbappar, den aktuella HTTP-begäran. I andra apptyper kan du ställa in detta så att händelser grupperas tillsammans.
  * **ID**: Ett genererat värde som korrelerar olika händelser, så att du kan hitta relaterade objekt när du inspekterar en händelse i Diagnostiksökning.
  * **Namn**: En identifierare, vanligtvis URL för HTTP-begäran.
  * **SyntheticSource**: Om inte null eller tom, en sträng som anger att källan till begäran har identifierats som en robot eller webbtest. Som standard är det uteslutet från beräkningar i Metrics Explorer.
* **Egenskaper**: Egenskaper som skickas med alla telemetridata. Det kan åsidosättas i enskilda Track * samtal.
* **Session**: Användarens session. ID:n är inställt på ett genererat värde, som ändras när användaren inte har varit aktiv på ett tag.
* **Användare**: Användarinformation.

## <a name="limits"></a>Begränsningar

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

Använd [sampling](../../azure-monitor/app/sampling.md)för att undvika att träffa datahastighetsgränsen .

Information om hur länge data sparas finns i [Datalagring och sekretess](../../azure-monitor/app/data-retention-privacy.md).

## <a name="reference-docs"></a>Referensdokument

* [ASP.NET referens](https://docs.microsoft.com/dotnet/api/overview/azure/insights?view=azure-dotnet)
* [Java-referens](https://docs.microsoft.com/java/api/overview/azure/appinsights?view=azure-java-stable/)
* [JavaScript-referens](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)


## <a name="sdk-code"></a>SDK-kod

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-paket](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="questions"></a>Frågor

* *Vilka undantag kan Track_() anrop kasta?*

    Inga. Du behöver inte slå in dem i try-catch-satser. Om SDK stöter på problem loggas meddelanden i felsökningskonsolutdata och - om meddelandena kommer igenom - i diagnostiksökning.
* *Finns det ett REST API för att hämta data från portalen?*

    Ja, [API:et för dataåtkomst](https://dev.applicationinsights.io/). Andra sätt att extrahera data är [export från Analytics till Power BI](../../azure-monitor/app/export-power-bi.md ) och kontinuerlig [export](../../azure-monitor/app/export-telemetry.md).

## <a name="next-steps"></a><a name="next"></a>Nästa steg

* [Sök händelser och loggar](../../azure-monitor/app/diagnostic-search.md)
* [Felsökning](../../azure-monitor/app/troubleshoot-faq.md)

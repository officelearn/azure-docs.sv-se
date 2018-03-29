---
title: Application Insights API för anpassade händelser och mått | Microsoft Docs
description: Infoga ett fåtal rader med kod i din enhet eller skrivbordet app, webbsida eller tjänsten för att spåra användning och diagnostisera problem.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 80400495-c67b-4468-a92e-abf49793a54d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/17/2017
ms.author: mbullwin
ms.openlocfilehash: ff4b587790872511c7b545233685f5b3ae068291
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
---
# <a name="application-insights-api-for-custom-events-and-metrics"></a>Application Insights API för anpassade händelser och mått

Infoga ett fåtal rader med kod i ditt program att ta reda på vad användarna gör med den eller för att diagnostisera problem. Du kan skicka telemetri från enheten och den stationära appar, webbklienter och webbservrar. Använd den [Azure Application Insights](app-insights-overview.md) core telemetriska API: N att skicka anpassade händelser och mått och egna versioner av standard telemetri. Detta API är samma API som standard Application Insights-datainsamlare använder.

## <a name="api-summary"></a>API-sammanfattning
API: et är enhetligt för alla plattformar förutom några mindre variationer.

| Metod | Används för |
| --- | --- |
| [`TrackPageView`](#page-views) |Sidor, skärmar, blad eller formulär. |
| [`TrackEvent`](#trackevent) |Användaråtgärder och andra händelser. Används för att spåra användarens beteende eller att övervaka prestanda. |
| [`TrackMetric`](#trackmetric) |Prestandamått, till exempel kölängderna som inte är relaterade till specifika händelser. |
| [`TrackException`](#trackexception) |Loggning undantag för diagnos. Spåra där de inträffa i förhållande till andra händelser och undersöka stackspår. |
| [`TrackRequest`](#trackrequest) |Loggning av frekvens och varaktighet för serverbegäranden för prestandaanalys. |
| [`TrackTrace`](#tracktrace) |Diagnostiska loggmeddelanden. Du kan också samla in loggar från tredje part. |
| [`TrackDependency`](#trackdependency) |Loggning varaktighet och frekvens för anrop till externa komponenter som är beroende av din app. |

Du kan [bifoga egenskaper och mått](#properties) för de flesta av dessa telemetri-anrop.

## <a name="prep"></a>Innan du börjar
Om du inte har en referens i Application Insights SDK ännu:

* Lägg till Application Insights SDK i projektet:

  * [ASP.NET-projekt](app-insights-asp-net.md)
  * [Java-projekt](app-insights-java-get-started.md)
  * [Node.js-projekt](app-insights-nodejs.md)
  * [JavaScript i varje webbsida](app-insights-javascript.md) 
* Inkludera i din enhet eller web serverkod:

    *C#:* `using Microsoft.ApplicationInsights;`

    *Visual Basic:* `Imports Microsoft.ApplicationInsights`

    *Java:* `import com.microsoft.applicationinsights.TelemetryClient;`
    
    *Node.js:* `var applicationInsights = require("applicationinsights");`

## <a name="get-a-telemetryclient-instance"></a>Hämta en TelemetryClient-instans
Hämta en instans av `TelemetryClient` (förutom i JavaScript i webbsidor):

*C#*

    private TelemetryClient telemetry = new TelemetryClient();

*Visual Basic*

    Private Dim telemetry As New TelemetryClient

*Java*

    private TelemetryClient telemetry = new TelemetryClient();
    
*Node.js*

    var telemetry = applicationInsights.defaultClient;


TelemetryClient är trådsäkra.

Inkommande HTTP-begäranden som automatiskt avbildas för ASP.NET och Java-projekt. Du kanske vill skapa ytterligare instanser av TelemetryClient för andra modulen av din app. Du kan exempelvis har en TelemetryClient-instans i klassen mellanprogram till rapporten affärshändelser logik. Du kan ange egenskaper, till exempel användar-ID och DeviceId för att identifiera datorn. Den här informationen är ansluten till alla händelser som instace skickar. 

*C#*

    TelemetryClient.Context.User.Id = "...";
    TelemetryClient.Context.Device.Id = "...";

*Java*

    telemetry.getContext().getUser().setId("...);
    telemetry.getContext().getDevice().setId("...");

Du kan använda i Node.js-projekt, `new applicationInsights.TelemetryClient(instrumentationKey?)` att skapa en ny instans, men detta rekommenderas endast för scenarier som kräver isolerade konfigurationen från singleton `defaultClient`.

## <a name="trackevent"></a>TrackEvent
I Application Insights en *anpassade händelsen* är en datapunkt som du kan visa i [Metrics Explorer](app-insights-metrics-explorer.md) som en sammansatt antal och i [diagnostiska Sök](app-insights-diagnostic-search.md) som individuella förekomster. (Det är inte relaterade till MVC eller andra framework ”händelser”.)

Infoga `TrackEvent` anropar i koden för att räkna olika händelser. Hur ofta användare välja en viss funktion, hur ofta de uppnå specifika mål eller kanske hur ofta de göra vissa typer av misstag.

Till exempel skicka en händelse när en användare vinner i spelet app:

*JavaScript*

    appInsights.trackEvent("WinGame");

*C#*

    telemetry.TrackEvent("WinGame");

*Visual Basic*

    telemetry.TrackEvent("WinGame")

*Java*

    telemetry.trackEvent("WinGame");
    
*Node.js*

    telemetry.trackEvent({name: "WinGame"});

### <a name="view-your-events-in-the-microsoft-azure-portal"></a>Visa händelserna i Microsoft Azure-portalen
Om du vill se en uppräkning av händelserna, öppna en [Metrics Explorer](app-insights-metrics-explorer.md) bladet Lägg till ett nytt diagram och välj **händelser**.  

![Finns ett antal anpassade händelser](./media/app-insights-api-custom-events-metrics/01-custom.png)

Om du vill jämföra antalet olika händelser sägs diagramtypen **rutnätet**, och som händelsenamn:

![Ange diagramtyp och gruppering](./media/app-insights-api-custom-events-metrics/07-grid.png)

Klicka på rutnätet, via ett händelsenamn för att visa enskilda förekomster av händelsen. Om du vill se mer detaljer - klickar du på någon av förekomsterna i listan.

![Detaljvisning händelser](./media/app-insights-api-custom-events-metrics/03-instances.png)

Ange händelsenamn som du är intresserad i bladet filter för att fokusera på specifika händelser i sökning eller Metrics Explorer:

![Öppna filter, expanderar händelsenamnet och välj ett eller flera värden](./media/app-insights-api-custom-events-metrics/06-filter.png)

### <a name="custom-events-in-analytics"></a>Anpassade händelser i Analytics

Telemetrin är tillgängliga i den `customEvents` tabell i [Application Insights Analytics](app-insights-analytics.md). Varje rad representerar ett anrop till `trackEvent(..)` i din app. 

Om [provtagning](app-insights-sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. För exempel itemCount == 10 innebär att 10 anrop till trackevent () provtagning processen bara skickas en av dem. För att få en korrekt antal anpassade händelser, bör du använda därför använda kod som `customEvent | summarize sum(itemCount)`.


## <a name="trackmetric"></a>TrackMetric

Application Insights kan diagram mått som inte är kopplade till specifika händelser. Exempelvis kan du övervaka en Kölängd med jämna mellanrum. De enskilda mätningarna är lika intressant variationer och trender med statistik, och så statistiska stapeldiagram är användbara.

Du kan använda för att skicka mått till Application Insights i `TrackMetric(..)` API. Det finns två sätt att skicka ett mått: 

* Enstaka värde. Varje gång du utför ett mått i ditt program kan skicka du det motsvarande värdet till Application Insights. Anta att du har ett mått som beskriver antalet objekt i en behållare. Du först lägga till tre objekt i behållaren och sedan ta bort två objekt under en viss tidsperiod. Därför bör du anropa `TrackMetric` två gånger: först skicka värdet `3` och sedan värdet `-2`. Application Insights lagrar båda värdena för din räkning. 

* Aggregering. När du arbetar med mått är sällan varje enskild mätning av intresse. I stället är en sammanfattning av vad som hände under en viss tidsperiod viktigt. Sådana sammanfattning kallas _aggregering_. I exemplet ovan är summan av mått för tidsperioden `1` och antalet mått värden är `2`. När du använder metoden Aggregeringen kan du bara anropa `TrackMetric` en gång per tidsperiod och skicka de sammanlagda värdena. Detta är den rekommenderade metoden eftersom den avsevärt minska kostnaderna och prestanda försämras genom att skicka färre datapunkter till Application Insights vid fortfarande insamling av alla relevanta uppgifter.

### <a name="examples"></a>Exempel:

#### <a name="single-values"></a>Enstaka värden

Så här skickar du ett enda värde:

*JavaScript*

 ```Javascript
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

```Java
    
    telemetry.trackMetric("queueLength", 42.0);

```

*Node.js*

 ```Javascript
     telemetry.trackMetric({name: "queueLength", value: 42.0});
 ```

#### <a name="aggregating-metrics"></a>Sammanställa mått

Det rekommenderas att aggregera mått innan de skickas från din app, för att minska bandbredd, kostnad och för att förbättra prestanda.
Här är ett exempel på sammanställa kod:

*C#*

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.DataContracts;

namespace MetricAggregationExample
{
    /// <summary>
    /// Aggregates metric values for a single time period.
    /// </summary>
    internal class MetricAggregator
    {
        private SpinLock _trackLock = new SpinLock();

        public DateTimeOffset StartTimestamp    { get; }
        public int Count                        { get; private set; }
        public double Sum                       { get; private set; }
        public double SumOfSquares              { get; private set; }
        public double Min                       { get; private set; }
        public double Max                       { get; private set; }
        public double Average                   { get { return (Count == 0) ? 0 : (Sum / Count); } }
        public double Variance                  { get { return (Count == 0) ? 0 : (SumOfSquares / Count)
                                                                                  - (Average * Average); } }
        public double StandardDeviation         { get { return Math.Sqrt(Variance); } }

        public MetricAggregator(DateTimeOffset startTimestamp)
        {
            this.StartTimestamp = startTimestamp;
        }

        public void TrackValue(double value)
        {
            bool lockAcquired = false;

            try
            {
                _trackLock.Enter(ref lockAcquired);

                if ((Count == 0) || (value < Min))  { Min = value; }
                if ((Count == 0) || (value > Max))  { Max = value; }
                Count++;
                Sum += value;
                SumOfSquares += value * value;
            }
            finally
            {
                if (lockAcquired)
                {
                    _trackLock.Exit();
                }
            }
        }
    }   // internal class MetricAggregator

    /// <summary>
    /// Accepts metric values and sends the aggregated values at 1-minute intervals.
    /// </summary>
    public sealed class Metric : IDisposable
    {
        private static readonly TimeSpan AggregationPeriod = TimeSpan.FromSeconds(60);

        private bool _isDisposed = false;
        private MetricAggregator _aggregator = null;
        private readonly TelemetryClient _telemetryClient;

        public string Name { get; }

        public Metric(string name, TelemetryClient telemetryClient)
        {
            this.Name = name ?? "null";
            this._aggregator = new MetricAggregator(DateTimeOffset.UtcNow);
            this._telemetryClient = telemetryClient ?? throw new ArgumentNullException(nameof(telemetryClient));

            Task.Run(this.AggregatorLoopAsync);
        }

        public void TrackValue(double value)
        {
            MetricAggregator currAggregator = _aggregator;
            if (currAggregator != null)
            {
                currAggregator.TrackValue(value);
            }
        }

        private async Task AggregatorLoopAsync()
        {
            while (_isDisposed == false)
            {
                try
                {
                    // Wait for end end of the aggregation period:
                    await Task.Delay(AggregationPeriod).ConfigureAwait(continueOnCapturedContext: false);

                    // Atomically snap the current aggregation:
                    MetricAggregator nextAggregator = new MetricAggregator(DateTimeOffset.UtcNow);
                    MetricAggregator prevAggregator = Interlocked.Exchange(ref _aggregator, nextAggregator);

                    // Only send anything is at least one value was measured:
                    if (prevAggregator != null && prevAggregator.Count > 0)
                    {
                        // Compute the actual aggregation period length:
                        TimeSpan aggPeriod = nextAggregator.StartTimestamp - prevAggregator.StartTimestamp;
                        if (aggPeriod.TotalMilliseconds < 1)
                        {
                            aggPeriod = TimeSpan.FromMilliseconds(1);
                        }

                        // Construct the metric telemetry item and send:
                        var aggregatedMetricTelemetry = new MetricTelemetry(
                                Name,
                                prevAggregator.Count,
                                prevAggregator.Sum,
                                prevAggregator.Min,
                                prevAggregator.Max,
                                prevAggregator.StandardDeviation);
                        aggregatedMetricTelemetry.Properties["AggregationPeriod"] = aggPeriod.ToString("c");

                        _telemetryClient.Track(aggregatedMetricTelemetry);
                    }
                }
                catch(Exception ex)
                {
                    // log ex as appropriate for your application
                }
            }
        }

        void IDisposable.Dispose()
        {
            _isDisposed = true;
            _aggregator = null;
        }
    }   // public sealed class Metric
}
```

### <a name="custom-metrics-in-metrics-explorer"></a>Anpassade mått i Metrics Explorer

Om du vill se resultatet öppna Metrics Explorer och Lägg till ett nytt diagram. Redigera diagram om du vill visa dina mått.

> [!NOTE]
> Din anpassade mått kan ta flera minuter innan den visas i listan över tillgängliga mått.
>

![Lägg till ett nytt schema eller välj ett diagram och under anpassad, väljer du din mått](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

### <a name="custom-metrics-in-analytics"></a>Anpassade mått i Analytics

Telemetrin är tillgängliga i den `customMetrics` tabell i [Application Insights Analytics](app-insights-analytics.md). Varje rad representerar ett anrop till `trackMetric(..)` i din app.
* `valueSum` -Detta är summan av mått. Om du vill ha medelvärdet dividera med `valueCount`.
* `valueCount` -Antalet mått som har aggregerats till detta `trackMetric(..)` anropa.

## <a name="page-views"></a>Sidvisning
I en enhet eller en webbsida app skickas sidan Visa telemetri som standard när varje skärmen eller sidan har lästs in. Men du kan ändra det om du vill spåra sidvisningar vid ytterligare eller olika tidpunkter. I en app som visar flikarna eller blad, kanske du vill spåra en sida när användaren öppnar ett nytt blad.

![Användning linsen på bladet översikt](./media/app-insights-api-custom-events-metrics/appinsights-47usage-2.png)

Användar-och sessionen skickas som egenskaper tillsammans med sidvyer så diagram användar- och session kommer alive när sidan Visa telemetri.

### <a name="custom-page-views"></a>Anpassade sidvisningar
*JavaScript*

    appInsights.trackPageView("tab1");

*C#*

    telemetry.TrackPageView("GameReviewPage");

*Java*

    telemetry.trackPageView("GameReviewPage");

*Visual Basic*

    telemetry.TrackPageView("GameReviewPage")


Om du har flera flikar i olika HTML-sidor kan ange du URL: en för:

    appInsights.trackPageView("tab1", "http://fabrikam.com/page1.htm");

### <a name="timing-page-views"></a>Tidsinställning sidvisningar
Som standard, vilka tider som ska rapporteras som **inläsningstid för sidvisning** mäts från när webbläsaren skickar en begäran förrän händelsen i webbläsarens sidan anropas.

I stället kan du antingen:

* Ange en explicit varaktighet i den [trackPageView](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md#trackpageview) anropa: `appInsights.trackPageView("tab1", null, null, null, durationInMilliseconds);`.
* Använd vyn sida tidsuppgifterna anrop `startTrackPage` och `stopTrackPage`.

*JavaScript*

    // To start timing a page:
    appInsights.startTrackPage("Page1");

...

    // To stop timing and log the page:
    appInsights.stopTrackPage("Page1", url, properties, measurements);

Det namn som du använder som den första parametern associerar start och stopp-anrop. Standard namnet på aktuella sidan.

De resulterande sidan belastningen varaktighet som visas i Metrics Explorer härleds från intervallet mellan start och stopp-anrop. Det är upp till dig vilka intervall som du faktiskt tid.

### <a name="page-telemetry-in-analytics"></a>Sidan telemetri i Analytics

I [Analytics](app-insights-analytics.md) två tabeller visar data från funktioner i webbläsaren:

* Den `pageViews` tabellen innehåller information om URL: en och sidrubriken
* Den `browserTimings` tabellen innehåller information om klientens prestanda, till exempel den tid det tar att bearbeta inkommande data

Söka efter hur lång tid tar webbläsaren för att behandla olika sidor:

```
browserTimings | summarize avg(networkDuration), avg(processingDuration), avg(totalDuration) by name 
```

För att identifiera popularities av olika webbläsare:

```
pageViews | summarize count() by client_Browser
```

Om du vill associera sidvisningar för AJAX-anrop, ansluta med beroenden:

```
pageViews | join (dependencies) on operation_Id 
```

## <a name="trackrequest"></a>TrackRequest
Server-SDK använder TrackRequest för att logga HTTP-begäranden.

Du kan också anropa den själv om du vill simulera begäranden i ett sammanhang där du inte har modulen web service körs.

Det rekommenderade sättet att skicka begärandetelemetri om är dock där begäran fungerar som en <a href="#operation-context">åtgärdssammanhang</a>.

## <a name="operation-context"></a>Åtgärden kontext
Du kan jämföra telemetri objekt tillsammans genom att associeras med operationen kontext. Modulen standard spårning av förfrågningar sker för undantag och andra händelser som sänds när en HTTP-begäran bearbetas. I [Sök](app-insights-diagnostic-search.md) och [Analytics](app-insights-analytics.md), kan du lätt hitta eventuella händelser som är kopplad till begäran med hjälp av dess åtgärd Id.

Se [korrelation telemetri i Application Insights](application-insights-correlation.md) för mer information om korrelation.

När spårning telemetri manuellt, det enklaste sättet att se till att telemetri korrelation med hjälp av det här mönstret:

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

Tillsammans med inställningen en åtgärdssammanhang `StartOperation` skapar ett telemetri objekt av typen som du anger. Den skickar telemetri objektet automatiskt när igen, eller om du explicit anropa `StopOperation`. Om du använder `RequestTelemetry` som telemetri typ, varaktigheten har angetts till tidsintervall mellan start och stopp.

Telemetri objekt som rapporterats inom en omfattning åtgärden bli underordnade om du för denna åtgärd. Åtgärden kontexter kan vara kapslade. 

I sökning, kontext för åtgärden för att skapa den **relaterade objekt** lista:

![Relaterade objekt](./media/app-insights-api-custom-events-metrics/21.png)

Se [spåra anpassade åtgärder med Application Insights SDK för .NET](application-insights-custom-operations-tracking.md) mer information om anpassade åtgärder spårning.

### <a name="requests-in-analytics"></a>Begäranden i Analytics 

I [Application Insights Analytics](app-insights-analytics.md), begär visa upp i den `requests` tabellen.

Om [provtagning](app-insights-sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. För exempel itemCount == 10 innebär att 10 anrop till trackRequest() provtagning processen bara skickas en av dem. För att få en korrekt antal begäranden och genomsnittlig varaktighet segmenterat per begäran namn kan använda koden som:

```AIQL
requests | summarize count = sum(itemCount), avgduration = avg(duration) by name
```


## <a name="trackexception"></a>TrackException
Skicka undantag till Application Insights:

* Att [räkna](app-insights-metrics-explorer.md), som en indikation på hur ofta ett problem.
* Att [undersöka enskilda förekomster](app-insights-diagnostic-search.md).

Rapporterna innehåller stackspårningar.

*C#*

    try
    {
        ...
    }
    catch (Exception ex)
    {
       telemetry.TrackException(ex);
    }

*Java*

    try {
        ...
    } catch (Exception ex) {
        telemetry.trackException(ex);
    }

*JavaScript*

    try
    {
       ...
    }
    catch (ex)
    {
       appInsights.trackException(ex);
    }
    
*Node.js*

    try
    {
       ...
    }
    catch (ex)
    {
       telemetry.trackException({exception: ex});
    }

SDK: erna fånga undantag som många automatiskt, så att du inte alltid behöver anropa TrackException explicit.

* ASP.NET: [skriva kod för att fånga undantag från](app-insights-asp-net-exceptions.md).
* J2EE: [undantag noteras automatiskt](app-insights-java-get-started.md#exceptions-and-request-failures).
* JavaScript: Undantag noteras automatiskt. Lägg till en rad kodfragmentet som infogas i dina webbsidor om du vill inaktivera automatisk insamling:

    ```
    ({
      instrumentationKey: "your key"
      , disableExceptionTracking: true
    })
    ```

### <a name="exceptions-in-analytics"></a>Undantag i Analytics

I [Application Insights Analytics](app-insights-analytics.md), undantag visas i den `exceptions` tabellen.

Om [provtagning](app-insights-sampling.md) är i drift, den `itemCount` egenskapen visar ett värde större än 1. För exempel itemCount == 10 innebär att 10 anrop till trackException() provtagning processen bara skickas en av dem. Använda koden som för att få rätt antal undantag som är segmenterat av typ av undantag:

```
exceptions | summarize sum(itemCount) by type
```

De flesta av de viktiga Stackinformation redan extraherade i olika variabler, men du kan sätta ihop den `details` strukturen för att få mer. Eftersom den här strukturen är dynamiska, bör du omvandla resultatet till vilken du förväntar dig. Exempel:

```AIQL
exceptions
| extend method2 = tostring(details[0].parsedStack[1].method)
```

Om du vill associera undantag med relaterade begäranden, använder du en koppling:

```
exceptions
| join (requests) on operation_Id 
```

## <a name="tracktrace"></a>TrackTrace
Använd TrackTrace för att diagnostisera problem genom att skicka ”spåret spår” till Application Insights. Du kan skicka mängder diagnostikdata och granska dem i [diagnostiska Sök](app-insights-diagnostic-search.md).

I .NET [logga kort](app-insights-asp-net-trace-logs.md) använder den här API för att skicka loggar med från tredje part till portalen.

I Java för [Standard loggar tangenttryckningar som Log4J Logback](app-insights-java-trace-logs.md) använder Application Insights Log4j eller Logback Appenders för att skicka loggar från tredje part till portalen.

*C#*

    telemetry.TrackTrace(message, SeverityLevel.Warning, properties);

*Java*

    telemetry.trackTrace(message, SeverityLevel.Warning, properties);
    
*Node.js*

    telemetry.trackTrace({message: message, severity:applicationInsights.Contracts.SeverityLevel.Warning, properties:properties});


Du kan söka i meddelandeinnehåll, men du kan inte filtrera (till skillnad från egenskapsvärden) på den.

Storleksgräns för `message` är mycket högre än gränsen på Egenskaper.
En fördel med TrackTrace är att du kan publicera relativt lång data i meddelandet. Koda till exempel det postdata.  

Dessutom kan du lägga till en allvarlighetsgrad för meddelandet. Och precis som andra telemetri kan du lägga till egenskapsvärden som hjälper dig att filtrera eller söka efter olika uppsättningar av spår. Exempel:

*C#*

```C#
    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });
```

*Java*

```Java

    Map<String, Integer> properties = new HashMap<>();
    properties.put("Database", db.ID);
    telemetry.trackTrace("Slow Database response", SeverityLevel.Warning, properties);

```

I [Sök](app-insights-diagnostic-search.md), därefter kan du enkelt filtrera ut alla meddelanden från en viss allvarlighetsgrad som relaterar till en viss databas.


### <a name="traces-in-analytics"></a>Spåren i Analytics

I [Application Insights Analytics](app-insights-analytics.md), anrop till TrackTrace visas i den `traces` tabellen.

Om [provtagning](app-insights-sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. Exempel itemCount == 10 innebär att 10 anrop till `trackTrace()`, provtagning processen skickas endast ett av dem. För att få en korrekt antal trace-anrop kan du bör använda därför kod som `traces | summarize sum(itemCount)`.

## <a name="trackdependency"></a>TrackDependency
Använda TrackDependency-anropet för att spåra svarstider och slutförandefrekvenser för anrop till ett externt kodavsnitt. Resultatet visas i beroendediagrammen i portalen.

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
}
```

*Java*

```Java
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

```Javascript
var success = false;
var startTime = new Date().getTime();
try
{
    success = dependency.Call();
}
finally
{
    var elapsed = new Date() - startTime;
    telemetry.trackDependency({dependencyTypeName: "myDependency", name: "myCall", duration: elapsed, success:success});
}
```

Kom ihåg att server SDK inkluderar en [beroende modulen](app-insights-asp-net-dependencies.md) som identifierar och spårar vissa beroendeanrop automatiskt – till exempel till databaser och REST API: er. Du måste installera en agent på servern för att göra modulen fungerar. 

I Java, vissa beroendeanrop kan spåras automatiskt med hjälp av [agenten Java](app-insights-java-agent.md).

Du kan använda det här anropet om du vill spåra anrop som automatisk spårning inte fånga eller om du inte vill installera agenten.

Om du vill inaktivera modulen standard beroende spårning i C#, redigera [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) och ta bort referensen till `DependencyCollector.DependencyTrackingTelemetryModule`. I Java, inte installera agenten java om du inte vill samla in standard beroenden automatiskt.

### <a name="dependencies-in-analytics"></a>Beroenden i Analytics

I [Application Insights Analytics](app-insights-analytics.md), trackDependency anropar visa i den `dependencies` tabellen.

Om [provtagning](app-insights-sampling.md) är i drift, egenskapen itemCount visar ett värde större än 1. För exempel itemCount == 10 innebär att 10 anrop till trackDependency() provtagning processen bara skickas en av dem. För att få en korrekt antal beroenden segmenterade av mål-komponenten kan använda koden som:

```
dependencies | summarize sum(itemCount) by target
```

Om du vill associera beroenden med relaterade begäranden, använder du en koppling:

```
dependencies
| join (requests) on operation_Id 
```

## <a name="flushing-data"></a>Tömmer data
Normalt skickar SDK data alltid valt att minimera påverkan på användaren. Men i vissa fall kan kanske du vill i bufferten – till exempel om du använder SDK: N i ett program som stängs av.

*C#*
 
 ```C#
    telemetry.Flush();
    // Allow some time for flushing before shutdown.
    System.Threading.Thread.Sleep(5000);
```

*Java*

```Java
    telemetry.flush();
    //Allow some time for flushing before shutting down
    Thread.sleep(5000);
```

    
*Node.js*

    telemetry.flush();

Observera att funktionen är asynkron för den [server telemetri kanal](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/).

Vi rekommenderar ska flush() metoden användas i aktiviteten avstängning av programmet.

## <a name="authenticated-users"></a>Autentiserade användare
I en webbapp identifieras användare (som standard) av cookies. En användare kan räknas mer än en gång om de har åtkomst till din app från en annan dator eller webbläsare, eller om de tar bort cookies.

Om användare loggar in på din app, kan du få en mer korrekt antal genom att ange autentiserat användar-ID i webbläsaren koden:

*JavaScript*

```JS
// Called when my app has identified the user.
function Authenticated(signInId) {
    var validatedId = signInId.replace(/[,;=| ]+/g, "_");
    appInsights.setAuthenticatedUserContext(validatedId);
    ...
}
```

På en webbplats med ASP.NET MVC-program, till exempel:

*Razor*

        @if (Request.IsAuthenticated)
        {
            <script>
                appInsights.setAuthenticatedUserContext("@User.Identity.Name
                   .Replace("\\", "\\\\")"
                   .replace(/[,;=| ]+/g, "_"));
            </script>
        }

Det är inte nödvändigt att använda användarens faktiska inloggningsnamn. Har bara vara ett ID som är unika för användaren. Namnet får inte innehålla blanksteg eller något av tecknen `,;=|`.

Användar-ID är också i en sessionscookie och skickas till servern. Om server SDK installeras, skickas autentiserat användar-ID som en del av kontextegenskaperna för både klienten och servern telemetri. Därefter kan du filtrera och söka på den.

Om din app-grupper användare till konton, kan du överföra en identifierare för kontot (med teckenbegränsningar för samma).

      appInsights.setAuthenticatedUserContext(validatedId, accountId);

I [Metrics Explorer](app-insights-metrics-explorer.md), du kan skapa ett diagram som räknar **autentiserade användare,**, och **användarkonton**.

Du kan också [Sök](app-insights-diagnostic-search.md) för klienten datapunkter med specifika användarnamn och konton.

## <a name="properties"></a>Filtrera, söka och segmentera dina data med hjälp av egenskaper
Du kan bifoga egenskaper och mått i händelserna (och sidan också vyer, undantag och andra telemetridata till statistik,).

*Egenskaper för* är strängvärden som du kan använda för att filtrera din telemetri i användningsrapporten. Till exempel om din app erbjuder flera spel, kan du koppla spelet till varje händelse så att du kan se vilka spel är mer populära.

Det finns en gräns på 8192 på string-längden. (Om du vill skicka stora mängder data kan du använda message-parameter för [TrackTrace](#track-trace).)

*Mått* är numeriska värden som kan presenteras grafiskt. Du kanske vill se om det finns en stegvis ökning av resultaten som din spelare uppnå. Diagram kan segmenterade av egenskaperna som skickas med händelsen, så att du kan få separat eller staplade diagram för olika spel.

För måttvärden visas korrekt, bör de vara större än eller lika med 0.

Det finns några [gränser för antal egenskaper och egenskapsvärden mått](#limits) som du kan använda.

*JavaScript*

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


*C#*

    // Set up some properties and metrics:
    var properties = new Dictionary <string, string>
       {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var metrics = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics);

*Node.js*

    // Set up some properties and metrics:
    var properties = {"game": currentGame.Name, "difficulty": currentGame.Difficulty};
    var metrics = {"Score": currentGame.Score, "Opponents": currentGame.OpponentCount};

    // Send the event:
    telemetry.trackEvent({name: "WinGame", properties: properties, measurements: metrics});


*Visual Basic*

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim metrics = New Dictionary (Of String, Double)
    metrics.Add("Score", currentGame.Score)
    metrics.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, metrics)


*Java*

    Map<String, String> properties = new HashMap<String, String>();
    properties.put("game", currentGame.getName());
    properties.put("difficulty", currentGame.getDifficulty());

    Map<String, Double> metrics = new HashMap<String, Double>();
    metrics.put("Score", currentGame.getScore());
    metrics.put("Opponents", currentGame.getOpponentCount());

    telemetry.trackEvent("WinGame", properties, metrics);


> [!NOTE]
> Se till att logga personligt identifierbar information i egenskaperna.
>
>

*Om du använde mått*, öppna Metrics Explorer och välja ett mått från den **anpassad** grupp:

![Öppna Metrics Explorer, markera diagrammet och välj mått](./media/app-insights-api-custom-events-metrics/03-track-custom.png)

> [!NOTE]
> Om din mått inte visas, eller om den **anpassad** rubrik finns inte där, Stäng bladet markeringen och försök igen senare. Mått kan ta en timme så att de sammanställs via pipeline.

*Om du använde egenskaper och mått*, segmentera måttet av egenskapen:

![Ange gruppering och välj sedan egenskapen under Gruppera efter](./media/app-insights-api-custom-events-metrics/04-segment-metric-event.png)

*I diagnostiska sökningen*, du kan visa egenskaper och mått för enskilda förekomster av en händelse.

![Välj en instans och välj sedan ”...”](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-4.png)

Använd den **Sök** fältet för att se händelsen förekomster som har ett visst egenskapsvärde.

![Skriv en term i sökrutan](./media/app-insights-api-custom-events-metrics/appinsights-23-customevents-5.png)

[Mer information om sökuttryck](app-insights-diagnostic-search.md).

### <a name="alternative-way-to-set-properties-and-metrics"></a>Alternativa sätt att ange egenskaper och mått
Om det är enklare kan du samla in parametrarna för en händelse i ett separat objekt:

    var event = new EventTelemetry();

    event.Name = "WinGame";
    event.Metrics["processingTime"] = stopwatch.Elapsed.TotalMilliseconds;
    event.Properties["game"] = currentGame.Name;
    event.Properties["difficulty"] = currentGame.Difficulty;
    event.Metrics["Score"] = currentGame.Score;
    event.Metrics["Opponents"] = currentGame.Opponents.Length;

    telemetry.TrackEvent(event);

> [!WARNING]
> Inte återanvända samma telemetri objektet instans (`event` i det här exemplet) att anropa Track*() flera gånger. Detta kan orsaka telemetri skickas med felaktig konfiguration.
>
>

### <a name="custom-measurements-and-properties-in-analytics"></a>Anpassade mått och egenskaper i Analytics

I [Analytics](app-insights-analytics.md), anpassade mått och egenskaper visas i den `customMeasurements` och `customDimensions` attribut för varje post telemetri.

Till exempel om du har lagt till en egenskap med namnet ”spelet” din begärandetelemetri för den här frågan antal förekomster av olika värden för ”spel” och visar medelvärdet för det anpassade mått ”resultatet”:

```
requests
| summarize sum(itemCount), avg(todouble(customMeasurements.score)) by tostring(customDimensions.game) 
```

Observera att:

* När du extraherar ett värde från customDimensions eller customMeasurements JSON, den har dynamiska typ, och så måste du skicka den `tostring` eller `todouble`.
* Med hänsyn till möjligheten att [provtagning](app-insights-sampling.md), bör du använda `sum(itemCount)`, inte `count()`.



## <a name="timed"></a> Tidsinställning händelser
Ibland vill du skapa diagram över hur lång tid det tar för att utföra en åtgärd. Du kanske exempelvis vill veta hur länge användare ta att överväga alternativen i spel. Du kan använda parametern mått för den här.

*C#*

```C#
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

```Java
    long startTime = System.currentTimeMillis();

    // perform timed action

    long endTime = System.currentTimeMillis();
    Map<String, Double> metrics = new HashMap<>();
    metrics.put("ProcessingTime", endTime-startTime);

    // Setup some propereties
    Map<String, String> properties = new HashMap<>();
    properties.put("signalSource", currentSignalSource.getName());

    //send the event
    telemetry.trackEvent("SignalProcessed", properties, metrics);

```


## <a name="defaults"></a>Standardegenskaperna för anpassad telemetri
Om du vill ange standardvärdet egenskapsvärden för några av de anpassa händelser som du skriver ange du dem i en TelemetryClient-instans. De är kopplade till varje telemetri-objekt som skickas från klienten.

*C#*

    using Microsoft.ApplicationInsights.DataContracts;

    var gameTelemetry = new TelemetryClient();
    gameTelemetry.Context.Properties["Game"] = currentGame.Name;
    // Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame");

*Visual Basic*

    Dim gameTelemetry = New TelemetryClient()
    gameTelemetry.Context.Properties("Game") = currentGame.Name
    ' Now all telemetry will automatically be sent with the context property:
    gameTelemetry.TrackEvent("WinGame")

*Java*

    import com.microsoft.applicationinsights.TelemetryClient;
    import com.microsoft.applicationinsights.TelemetryContext;
    ...


    TelemetryClient gameTelemetry = new TelemetryClient();
    TelemetryContext context = gameTelemetry.getContext();
    context.getProperties().put("Game", currentGame.Name);

    gameTelemetry.TrackEvent("WinGame");
    
*Node.js*

    var gameTelemetry = new applicationInsights.TelemetryClient();
    gameTelemetry.commonProperties["Game"] = currentGame.Name;

    gameTelemetry.TrackEvent({name: "WinGame"});



Enskilda telemetri anrop kan åsidosätta standardvärdena i sina egenskapen ordlistor.

*Web klienter för JavaScript*, [använder JavaScript telemetri initierare](#js-initializer).

*Att lägga till egenskaper till all telemetri*, inklusive data från standard samling moduler [implementera `ITelemetryInitializer` ](app-insights-api-filtering-sampling.md#add-properties).

## <a name="sampling-filtering-and-processing-telemetry"></a>Provtagning, filtrering och bearbetar telemetri
Du kan skriva kod för att bearbeta din telemetri innan den skickas från SDK. Bearbetningen innehåller data som skickas från standard telemetri moduler, till exempel HTTP request-samlingen och beroende samling.

[Lägga till egenskaper](app-insights-api-filtering-sampling.md#add-properties) till telemetri genom att implementera `ITelemetryInitializer`. Du kan till exempel lägga till versionsnummer eller värden som beräknats från andra egenskaper.

[Filtrering](app-insights-api-filtering-sampling.md#filtering) kan ändra eller ta bort telemetri innan den skickas från SDK genom att implementera `ITelemetryProcesor`. Du styr vilka skickas eller tas bort, men du behöver för effekten på din mått. Beroende på hur du ta bort objekt, kan du förlora möjligheten att navigera mellan relaterade objekt.

[Provtagning](app-insights-api-filtering-sampling.md) är en paketerad lösning för att minska mängden data som skickas från din app på portalen. Detta sker utan att påverka mätvärdena som visas. Och detta sker utan att påverka möjligheterna att diagnostisera problem genom att navigera mellan relaterade objekt som undantag, förfrågningar och sidvyer.

[Läs mer](app-insights-api-filtering-sampling.md).

## <a name="disabling-telemetry"></a>Inaktivera telemetri
Att *dynamiskt stoppa och starta* insamling och vidarebefordran av telemetri:

*C#*

```csharp

    using  Microsoft.ApplicationInsights.Extensibility;

    TelemetryConfiguration.Active.DisableTelemetry = true;
```

*Java*

```Java
    
    telemetry.getConfiguration().setTrackingDisabled(true);

```

Att *inaktivera valda standard insamlare*--exempelvis prestandaräknare, HTTP-begäranden eller beroenden – ta bort eller kommentera ut relevanta rader i [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Du kan göra detta, till exempel om du vill skicka TrackRequest data.

*Node.js*

```Javascript

    telemetry.config.disableAppInsights = true;
```

Att *inaktivera valda standard insamlare*--exempelvis prestandaräknare, HTTP-begäranden eller beroenden--till Initieringstid, kedja configuration metoder för att din kod för initiering av SDK:

```Javascript

    applicationInsights.setup()
        .setAutoCollectRequests(false)
        .setAutoCollectPerformance(false)
        .setAutoCollectExceptions(false)
        .setAutoCollectDependencies(false)
        .setAutoCollectConsole(false)
        .start();
```

Inaktivera dessa efter initiering med konfigurationsobjektet: `applicationInsights.Configuration.setAutoCollectRequests(false)`

## <a name="debug"></a>Utvecklarläge
Vid felsökning, är det praktiskt att ha din telemetri expedierade via pipeline så att du kan se resultatet direkt. Du kan också hämta meddelandena som hjälper dig spåra eventuella problem med telemetrin. Stänga av den i produktion, eftersom det kan påverka din app.

*C#*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = true;

*Visual Basic*

    TelemetryConfiguration.Active.TelemetryChannel.DeveloperMode = True


## <a name="ikey"></a> Ange den instrumentation nyckeln för valda anpassad telemetri
*C#*

    var telemetry = new TelemetryClient();
    telemetry.InstrumentationKey = "---my key---";
    // ...


## <a name="dynamic-ikey"></a> Dynamisk instrumentation nyckel
För att undvika blandas in telemetri från utvecklings-, test- och produktionsmiljöer, kan du [skapa separata Application Insights-resurser](app-insights-create-new-resource.md) och ändra deras nycklar, beroende på miljön.

Du kan ange den i koden i stället för att hämta nyckeln instrumentation från konfigurationsfilen. Ange nyckeln i en initieringsmetod, till exempel global.aspx.cs i en ASP.NET-tjänst:

*C#*

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey =
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      ...

*JavaScript*

    appInsights.config.instrumentationKey = myKey;



På webbsidor, kanske du vill ange från webbserverns tillstånd i stället kodning bokstavligt till skriptet. Till exempel i en webbsida som skapats i en ASP.NET-app:

*JavaScript i Razor*

    <script type="text/javascript">
    // Standard Application Insights webpage script:
    var appInsights = window.appInsights || function(config){ ...
    // Modify this part:
    }({instrumentationKey:  
      // Generate from server property:
      @Microsoft.ApplicationInsights.Extensibility.
         TelemetryConfiguration.Active.InstrumentationKey"
    }) // ...


## <a name="telemetrycontext"></a>TelemetryContext
TelemetryClient har en kontextegenskap som innehåller värden som skickas tillsammans med alla telemetridata. De normalt har angetts som standard telemetri moduler, men du kan också ange dem själv. Exempel:

    telemetry.Context.Operation.Name = "MyOperationName";

Om du anger dessa värden själv kan du ta bort den aktuella raden från [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), så att värdena och värdena som standard inte bli förvirrad.

* **Komponenten**: appen och dess version.
* **Enheten**: Data om enheten där programmet körs. (I web apps detta är den server eller klientenheten som telemetrin skickas från.)
* **InstrumentationKey**: The Application Insights-resurs i Azure var telemetrin visas. Det hämtas vanligtvis från ApplicationInsights.config.
* **Plats**: den geografiska platsen för enheten.
* **Åtgärden**: I web apps aktuella HTTP-begäran. I andra apptyper kan du ange att gruppera händelser tillsammans.
  * **ID**: ett genererat värde som korreleras olika händelser så att när du granska alla händelser i diagnostiska sökning, kan du hitta relaterade objekt.
  * **Namnet**: ett ID, vanligtvis URL: en för HTTP-begäran.
  * **SyntheticSource**: om inte null eller är tom, en sträng som anger att källan för begäran har identifierats som ett program eller webbplats test. Som standard är den inte beräkningar i Metrics Explorer.
* **Egenskaper för**: egenskaper som skickas med alla telemetridata. Den kan åsidosättas i enskilda spåra * anrop.
* **Sessionen**: användarens session. ID som anges till ett genererat värde ändras när användaren inte har varit aktiv under en stund.
* **Användaren**: användarinformation.

## <a name="limits"></a>Begränsningar
[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

Använd för att undvika träffa hastighetsbegränsning data [provtagning](app-insights-sampling.md).

För att fastställa hur länge data bevaras, se [datalagring och sekretess](app-insights-data-retention-privacy.md).

## <a name="reference-docs"></a>Referensdokument
* [ASP.NET-referens](https://msdn.microsoft.com/library/dn817570.aspx)
* [Referens för Java](http://dl.windowsazure.com/applicationinsights/javadoc/)
* [JavaScript-referens](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
* [Android SDK](https://github.com/Microsoft/ApplicationInsights-Android)
* [iOS SDK](https://github.com/Microsoft/ApplicationInsights-iOS)

## <a name="sdk-code"></a>SDK-kod
* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-aspnetcore)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [Windows Server-paket](https://github.com/Microsoft/applicationInsights-dotnet-server)
* [Java SDK](https://github.com/Microsoft/ApplicationInsights-Java)
* [Node.js SDK](https://github.com/Microsoft/ApplicationInsights-Node.js)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)
* [Alla plattformar](https://github.com/Microsoft?utf8=%E2%9C%93&query=applicationInsights)

## <a name="questions"></a>Frågor
* *Vilka undantag kan Track_() anrop utlösa?*

    Ingen. Du behöver inte lägga in dem i försök catch-satser. Om det uppstår problem i SDK, loggas meddelanden i konsolen felsökningsresultat och – om meddelanden får via--i diagnostiska sökningen.
* *Finns det en REST-API för att hämta data från portalen?*

    Ja, den [API för dataåtkomst](https://dev.applicationinsights.io/). Andra sätt att extrahera data är [exportera från Analytics till Power BI](app-insights-export-power-bi.md) och [löpande export](app-insights-export-telemetry.md).

## <a name="next"></a>Nästa steg
* [Sök händelser och loggar](app-insights-diagnostic-search.md)

* [Felsökning](app-insights-troubleshoot-faq.md)


